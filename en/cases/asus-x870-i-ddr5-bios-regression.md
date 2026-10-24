---
layout: default
title: ASUS X870-I — BIOS Regression with 96 GB DDR5
description: Case study of a Ryzen 9 9900X and 2×48 GB KINGBANK DDR5-6400 failing POST after upgrading ASUS X870-I BIOS from 1022 to 1805, and recovery via USB BIOS FlashBack.
lang: en
translation_key: asus-x870-i-ddr5
alternate_url: /cases/asus-x870-i-ddr5-bios-regression.html
date: 2026-10-24
---

# ASUS ROG STRIX X870-I — When a BIOS Update Breaks 96 GB DDR5

### From a working DDR5-6400 configuration to failed POST, and back to BIOS 1022

**Status:** Windows boot restored after downgrading to BIOS 1022. Long-duration memory stability testing remains outstanding.

## Hardware

| Component | Model |
|---|---|
| Motherboard | ASUS ROG STRIX X870-I GAMING WIFI |
| CPU | AMD Ryzen 9 9900X |
| RAM | KINGBANK KFXB, 96 GB (2×48 GB), DDR5-6400 CL32 |
| EXPO timings | 32-39-39-99 |
| GPU | NVIDIA GeForce RTX 4090 |
| SSDs | Samsung 990 PRO 4 TB + 2 TB |
| Diagnostics | ROG STRIX HIVE II Q-LED, HWiNFO64 |

Under **BIOS 1022**, the system had operated without observed issues with both modules installed and EXPO enabled at DDR5-6400. Before manually tuning UCLK, the measured clocks were MCLK 3200 MHz and UCLK 1600 MHz (1:2); SoC voltage was approximately 1.25 V.

## Failure after the upgrade

Following the standard **BIOS 1022 → 1805** update, the machine repeatedly stalled at the **yellow DRAM Q-LED** on the ROG STRIX HIVE II. Waiting for memory training, restarting, and attempting to boot with a single memory module did not restore reliable booting.

On some attempts the system passed POST, but Windows failed with various errors:

- `0xc0000221` — driver-image/loading failure;
- `0xFC` — ATTEMPTED_EXECUTE_OF_NOEXECUTE_MEMORY;
- `0x7F` — UNEXPECTED_KERNEL_MODE_TRAP.

Problems occurred even with **EXPO disabled at DDR5-4800**. These errors do not establish that the Windows installation or RAM hardware had been permanently damaged: they appeared during an unstable boot period.

## Firmware troubleshooting timeline

| BIOS version | Observed behavior |
|---|---|
| **1022, before upgrade** | 96 GB DDR5-6400 EXPO worked without observed issues |
| **1805** | DRAM Q-LED stalls, unreliable POST and Windows boot failures |
| **1805, reflashed with USB FlashBack** | No resolution |
| **1686 beta** | POST with a single 48 GB DIMM; two-DIMM configuration failed; UEFI display rendering glitch |
| **1681** | Windows booted with one DIMM at 4800; both DIMMs reached UEFI after testing DRAM VDD/VDDQ at 1.20 V; EXPO did not boot reliably |
| **1022, restored with USB FlashBack** | UEFI detected 96 GB; after an initial failed restart/reset, EXPO was enabled and Windows booted at 6400 |

Rollbacks were performed with **USB BIOS FlashBack** using a physical FAT32 USB drive and the ASUS-renamed firmware file `A5573.CAP`.

Despite ASUS release notes warning that rollback is not supported for some releases, **this particular motherboard did successfully return from 1805 to 1022**. This observation should not be treated as a guarantee for other boards or future BIOS versions.

After restoring 1022, setting **UCLK = MEMCLK** yielded HWiNFO64 readings of **MCLK 3200 MHz / UCLK 3200 MHz (1:1)**, with CPU SoC around 1.25 V. Successfully reaching Windows is **not** proof of long-term stability at DDR5-6400 1:1.

## What may have changed?

Between BIOS 1022 and 1805, ASUS release notes mention JEDEC compatibility changes, high-frequency DDR5 training improvements, additional EXPO profile support, and AMD AGESA updates. BIOS 1805 also refers to improved memory performance and compatibility with CXMT memory chips.

**Working hypothesis:** the regression affects automatic memory training or memory-controller parameter selection (such as voltage, ODT, drive strength, and signal timing), rather than necessarily indicating failed DRAM chips.

KINGBANK lists SK hynix M-die for the corresponding memory kit, so the CXMT-specific change in BIOS 1805 is not direct evidence of the cause of this incident.

A failure at JEDEC DDR5-4800 alongside a successful EXPO DDR5-6400 boot does **not** establish DRAM degradation. EXPO also changes timings, voltages, and other initialization parameters. During troubleshooting, the BIOS, installed DIMM count, and DRAM VDD/VDDQ were changed; no single correction was isolated under controlled test conditions.

## Conclusion

The configuration which worked before the update booted again after restoring **BIOS 1022**. This suggests a potential firmware-related compatibility regression involving the specific combination of **Ryzen 9 9900X + ASUS X870-I + 2×48 GB KINGBANK DDR5**, but the exact cause remains unconfirmed.

Further verification should include MemTest86 or OCCT, WHEA error checks, and multiple cold boots. The recovered DDR5-6400 1:1 setup should not yet be considered stress-test-validated.

**Request to ASUS:** compare DDR5 training and automatically selected settings between BIOS 1022 and 1805 for this two-DIMM, high-capacity configuration.

## References

- [Full technical report in GitHub](https://github.com/ieya100/ieya100.github.io/blob/main/hardware/asus-x870-i-ddr5-bios-regression.md)
- [Official ASUS X870-I BIOS downloads and release notes](https://rog.asus.com/ru/motherboards/rog-strix/rog-strix-x870-i-gaming-wifi/helpdesk_bios/)
