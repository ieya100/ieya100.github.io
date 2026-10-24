# ASUS ROG STRIX X870-I: 96 GB DDR5 POST regression after BIOS update

**Observed October 2026 · Status: boot recovered after rollback to BIOS 1022; stress-test stability not yet confirmed**

## Test platform

- Motherboard: ASUS ROG STRIX X870-I GAMING WIFI (AM5)
- CPU: AMD Ryzen 9 9900X
- RAM: KINGBANK KFXB, 2 × 48 GB DDR5-6400 CL32 (96 GB), timings 32-39-39-99
- DRAM ICs: SK hynix M-die per KINGBANK's published specifications for P/N K5.01.FPM5FF9501 (confirm exact module P/N independently)
- GPU: NVIDIA GeForce RTX 4090
- Storage: Samsung 990 PRO 4 TB + 2 TB
- Diagnostics: ASUS ROG STRIX HIVE II Q-LED; HWiNFO64; Windows boot screens

## Symptoms and reproduction

The system had operated without observed problems on **BIOS 1022**, with both 48 GB modules installed and EXPO enabled at DDR5-6400. Prior to the update, HWiNFO64 showed MCLK = 3200 MHz, UCLK = 1600 MHz (1:2), FCLK = 2000 MHz, and CPU SoC around 1.25 V.

After updating to **BIOS 1805**, POST repeatedly stalled at the **yellow DRAM Q-LED** on the HIVE II. Problems continued with EXPO disabled at DDR5-4800, and with one or two modules. Occasional POST success was followed by Windows boot failures, including:
- 0xc0000221 (driver image/loading error)
- 0xFC (ATTEMPTED_EXECUTE_OF_NOEXECUTE_MEMORY)
- 0x7F (UNEXPECTED_KERNEL_MODE_TRAP)

These Windows errors do not prove damaged drivers or faulty RAM; the underlying cause was not isolated. Further Windows repair attempts were avoided during suspected memory instability.

## Recovery attempts

| BIOS | Observed result |
|---|---|
| **1022** (initial) | Previously worked with 96 GB DDR5-6400 EXPO |
| **1805** | Frequent DRAM Q-LED POST failure; boot failures even at DDR5-4800 without EXPO |
| **1805**, reflashed via USB FlashBack | Behavior not resolved |
| **1686 beta** | POST with one 48 GB module; two-module setup unsuccessful; UEFI display glitch observed |
| **1681** | One module booted Windows at DDR5-4800; both modules could reach UEFI at DDR5-4800 after DRAM VDD/VDDQ 1.20 V test; EXPO setup failed to boot reliably |
| **1022**, restored via USB FlashBack | UEFI recognized 96 GB; after an initial failed restart/reset, enabling EXPO yielded a successful Windows boot |

After restoring **1022**, a manual setting **UCLK DIV1 MODE = UCLK = MEMCLK** gave HWiNFO64 readings **MCLK 3200 MHz / UCLK 3200 MHz (1:1)** and CPU SoC around 1.25 V. This establishes a successful boot, **not** long-term stability at 6400 1:1.

**Caution:** several conditions changed across tests (BIOS/AGESA, DIMM population, automatic training, EXPO, and RAM voltages). The tests do not demonstrate that higher DRAM voltage alone fixed the problem.

## BIOS release notes that may matter

Based on the official ASUS change log:
- **1022 (2025-03-07):** performance and CPU-support changes.
- **1028 (2025-04-30):** high-capacity memory compatibility improvements.
- **1063 (2025-07-02):** AGESA 1.2.0.3e; warns rollback is unsupported.
- **1627 (2026-02-11):** AGESA Pre1.3.0.0; JEDEC memory compatibility improvements.
- **1644 (2026-03-16):** AGESA 1.3.0.0a; changes to high-frequency DDR5 training and Ryzen 9000 boot stability.
- **1654 (2026-04-27):** AGESA 1.3.0.1; warns rollback is unsupported.
- **1681 (2026-06-22):** AGESA 1.3.0.1b; EXPO ULL profile support, ECC-UDIMM optimization.
- **1686 beta (2026-07-02):** AGESA 1.3.0.1b Patch A; GNR TSME support.
- **1805 (2026-09-30):** AGESA 1.3.0.1d; memory performance, stability, low-latency CXMT memory-chip compatibility.

The incident spans multiple AGESA branches, not just one release. The change log does not identify the exact failing training parameter.

The RAM model is listed by KINGBANK as **SK hynix M-die**, so the 1805 CXMT note should **not** be taken as proof that this kit is CXMT-based.

## Hypotheses (not confirmed)

1. Regression in automatic memory-training algorithms or their initialization/fallback path.
2. Different auto-selected memory controller or PHY settings (including ODT, drive strength, signal timing, VDDIO/MC, or SoC).
3. JEDEC versus EXPO profile-selection/training behavior.
4. Memory Context Restore/training cache behavior.
5. Interaction with this particular Ryzen 9 9900X memory controller and two 48 GB DIMMs.

A successful EXPO boot after a failed JEDEC boot does **not** establish RAM degradation. Changing the profile changes many parameters besides data rate and DRAM voltage.

## Recommended follow-up

- Verify exact module part number, SPD, EXPO profiles, ranks and memory ICs.
- Record screenshots from HWiNFO64 for SoC, VDDIO/MC, DRAM VDD/VDDQ, MCLK/UCLK/FCLK.
- Test several cold boots and full memory stability with MemTest86 / OCCT / TM5 before calling the recovered configuration stable.
- Ask ASUS to compare DDR5 training/auto parameters between BIOS 1022 and 1805 for Ryzen 9 9900X with 2 × 48 GB Hynix M-die DDR5-6400.
- Avoid assuming FlashBack can bypass all rollback protection: this board did return to BIOS 1022 in this incident, despite ASUS warnings.

## Sources

- [ASUS BIOS release notes, X870-I](https://rog.asus.com/ru/motherboards/rog-strix/rog-strix-x870-i-gaming-wifi/helpdesk_bios/)
- [KINGBANK KFXB memory specifications](https://www.kingbank.com/MemoryProduct/420.html)
- [Related discussion: AM5 memory training instability across BIOS versions](https://www.reddit.com/r/overclocking/comments/1s9jgxu/am5_memory_training_instability_across_bios/)

This report documents one system's observed behavior; it is not evidence of a universal X870-I defect.
