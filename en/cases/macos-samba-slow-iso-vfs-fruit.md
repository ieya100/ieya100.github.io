---
layout: default
title: "macOS and Samba: How an ISO Transfer Went from 11 to 54 MB/s"
description: "A real-world investigation of slow large-file transfers from a MacBook Air to Samba 4.22 in a Debian LXC container backed by ZFS: iperf3, SCP, SMB Multichannel, strict sync, and vfs_fruit."
lang: en
translation_key: macos-samba-slow-iso-vfs-fruit
alternate_url: /cases/macos-samba-slow-iso-vfs-fruit.html
date: 2026-11-08
last_modified_at: 2026-11-08
---

# macOS → Samba: Where Did the Megabytes Go?

### A real-world case: one Windows 11 ISO, roughly 11 MB/s over SMB, and 54 MB/s after tuning

**Result:** transferring the same ~8.1 GB ISO to the main SMB share became roughly **five times faster**, improving from **10–12 MB/s** to **~54 MB/s**. The biggest improvement coincided with enabling `catia fruit streams_xattr` on the Samba server. This is an observation from one particular setup, **not** a universal tuning recipe for every Mac or NAS.

## Test environment

- **Client:** Apple Silicon MacBook Air on Wi-Fi, using the built-in macOS SMB client, Finder, and the `cp` command.
- **Server:** Proxmox VE 9.2, an unprivileged Debian 13 LXC container running Samba 4.22.
- **Storage:** a ZFS mirror of two 16 TB WD Ultrastar HC550 HDDs, with ZSTD compression; the storage directory was bind-mounted into the container.
- **Network:** home LAN. NFS was discussed but **not tested** in this investigation.
- **Test file:** `Win11_25H2_EnglishInternational_x64_v2.iso`, approximately 8.1 GB.

The production Samba share was `[shlak]`, mapped to `/srv/shlak`. A separate `[sync-test]` share was created for controlled changes so that the main share would not be modified blindly.

## Symptoms

Copying the ISO to the NAS through either Finder or terminal `cp` ran at about **85–95 Mbit/s** according to macOS Activity Monitor. That is only **10–12 MB/s**, not 85–95 MB/s. The slowdown was reproducible with a **single large file**, so it could not be explained away as overhead from thousands of small files.

A representative command on the Mac:

```bash
time cp ~/Downloads/Win11_25H2_EnglishInternational_x64_v2.iso \
  /Volumes/shlak/test.iso
```

An earlier experiment using `mkfile 1g` was **not directly comparable**: `mkfile` creates a zero-filled file, and zeros compress extremely well on ZFS with ZSTD enabled. Such a test does not reliably measure the speed of writing actual data to the HDDs.

## Separating the network, storage, and file-transfer protocol

First, TCP throughput between the Mac and the Samba container was measured with `iperf3`:

```bash
iperf3 -c <CONTAINER_IP>
iperf3 -c <CONTAINER_IP> -R
```

The results were about **714 Mbit/s** from Mac to server and **897 Mbit/s** in the opposite direction. A 100 Mbit/s network link was clearly not the limiting factor.

Next, we sent the **same ISO to the same storage directory** using SCP (an SSH-based file-transfer path):

```bash
time scp ~/Downloads/Win11_25H2_EnglishInternational_x64_v2.iso \
  user@<CONTAINER_IP>:/srv/shlak/control.iso
```

During transfer, the client displayed roughly **69 MB/s**. The command's end-to-end time was **2:08.23**, corresponding to approximately **63 MB/s on average**, including overhead. Another transfer was running concurrently.

This narrowed the problem considerably: reading the source ISO, the LAN, and the ZFS-backed storage could all sustain much more than 11 MB/s. It does **not** prove that their behavior is irrelevant under every workload; file-transfer protocols impose different I/O patterns.

## What we actually tested

| Experiment | Observation |
|---|---|
| Initial SMB configuration, real ISO | ~10–12 MB/s |
| Disable SMB Multichannel on macOS (`mc_on=no`) | Greatly improved early synthetic tests, but **did not eliminate** the slow transfer of the real ISO |
| `strict sync = no` on the test share only | **No substantial improvement** for the ISO |
| Temporarily set `signing_required=no` on the Mac | macOS still reported `SMB_CURR_SIGN_ALGORITHM = AES_128_GMAC`; no proof that signing was disabled |
| Enable `vfs objects = catia fruit streams_xattr` on the test share, `strict sync = no` | **2:20.27** for ~8.1 GB, approximately **58 MB/s** |
| Keep the same VFS modules and restore `strict sync = yes` | **2:38.08**, approximately **51 MB/s** |
| Use the same VFS modules on the production share, with normal sync behavior and restored macOS security settings | **2:29.90**, approximately **54 MB/s** |

Activity Monitor sometimes showed instantaneous transmit rates of **~500–550 Mbit/s**. The comparison above uses **total elapsed copy times**, not short-lived peaks.

## Configuration that produced the improvement

### 1. macOS client

The final `/etc/nsmb.conf` contained:

```ini
[default]
mc_on=no
```

Disabling SMB Multichannel had made a significant difference during the early tests on this particular Mac. **This is not a blanket recommendation**: Multichannel can improve performance on machines with multiple useful network interfaces. After changing `nsmb.conf`, disconnect and reconnect all SMB shares associated with the server before retesting.

The temporary `signing_required=no` setting was **removed after testing**. There was no reason to weaken transport security without a demonstrated benefit. The connection used SMB 3.1.1; SMB encryption was off, while macOS reported AES-128-GMAC as its negotiated signing algorithm.

### 2. Samba server

The production share section of `/etc/samba/smb.conf` after the change:

```ini
[shlak]
   path = /srv/shlak
   browseable = yes
   read only = no
   guest ok = no
   valid users = shlak
   force user = shlak
   create mask = 0660
   directory mask = 2770
   vfs objects = catia fruit streams_xattr
```

Before editing a live configuration, make a backup:

```bash
cp /etc/samba/smb.conf /etc/samba/smb.conf.before-fruit
```

Validate and reload:

```bash
testparm -s
systemctl reload smbd
```

Then **unmount and reconnect the SMB share on the Mac** to establish a fresh session.

We restored the default **`strict sync = yes`** behavior. The effective value can be checked with:

```bash
testparm -sv 2>/dev/null | grep 'strict sync'
```

We did **not** disable ZFS synchronous write handling, checksums, or other storage-integrity protections.

### 3. The `vfs_fruit` warning

`testparm` reported:

```text
WARNING: some services use vfs_fruit, others don't.
Mounting them in conjunction on OS X clients results in undefined behaviour.
```

The production and test file shares had `fruit` enabled, while the default `[homes]`, `[printers]`, and `[print$]` sections did not. Both file shares that were mounted from the Mac during the final tests had matching VFS settings, but the warning itself **had not yet been resolved**.

A later cleanup should remove genuinely unused default shares or make sure shares that will be mounted together from macOS use a compatible configuration. Do not blindly enable `fruit` on every administrative or printer share.

## Rollback

If the VFS change causes issues, restore the server-side backup:

```bash
cp /etc/samba/smb.conf.before-fruit /etc/samba/smb.conf
testparm -s
systemctl reload smbd
```

Disconnect and reconnect the SMB share on the Mac. If you also modified `/etc/nsmb.conf`, restore your own backup of that file and reconnect the shares.

## Conclusions and limitations

1. **Do not confuse Mbit/s with MB/s.** A transfer rate of 90 Mbit/s is about 11.25 MB/s.
2. **Benchmark the same data.** A zero-filled file and a real ISO generate very different physical I/O on a ZSTD-compressed ZFS pool.
3. **Test the transport independently from SMB.** `iperf3` and SCP helped isolate the slow path.
4. In this case, the major speedup occurred when we added **`catia fruit streams_xattr`**. All three VFS modules were enabled together, and we **did not perform a reverse A/B test**. Therefore we cannot establish which individual module or interaction caused the change.
5. Changing `strict sync` alone did not solve the problem. With the VFS modules enabled, performance remained much higher using the **standard `strict sync = yes`** setting.
6. **Do not disable SMB signing or ZFS durability features as a first resort.** Our measured improvement was retained without making those compromises.

The final production-share result was **~54 MB/s instead of ~11 MB/s** for a real ISO. It was still below the theoretical limit of gigabit Ethernet, but it turned a frustrating transfer into a usable NAS experience.

## Documentation and related discussions

- [Samba: vfs_fruit(8)](https://www.samba.org/samba/docs/current/man-html/vfs_fruit.8.html) — macOS integration and interaction with other VFS modules.
- [Samba: smb.conf(5)](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html) — `strict sync`, signing, and share configuration.
- [Reddit: Slow SMB shares on macOS](https://www.reddit.com/r/MacOS/comments/17jgiyw/macos_slow_smb_shares/).
- [Related macOS SMB configuration notes](https://gist.github.com/fweller/ac8d996c408dd832b6cae4d2d47a21cb).

*These measurements apply to one home-lab setup. macOS version, Wi-Fi conditions, Samba implementation, and the underlying filesystem may materially affect the results.*
