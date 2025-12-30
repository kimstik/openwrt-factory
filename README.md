# OpenWrt Factory

Automatic OpenWrt builds for legacy routers with flash modifications.

## Supported Devices

| Device            | SoC    | Target        | Stock Flash | Variants      |
|-------------------|--------|---------------|-------------|---------------|
| D-Link DIR-825-B1 | AR7161 | ath79/generic | 8MB         | 8/16/32/64 MB |
| HAME MPR-A5       | RT5350 | ramips/rt305x | 4MB         | 8/16/32/64 MB |

## Downloads

Ready-to-use firmware available in [Releases](../../releases).

| Tag           | Device      |
|---------------|-------------|
| `v24.10.x`    | DIR-825-B1  |
| `v24.10.x-a5` | HAME MPR-A5 |

## Optimization Levels

| Level  | Description              | Default |
|--------|--------------------------|---------|
| base   | Standard -Os             | Yes     |
| x      | Experimental flags       | Yes     |
| lto    | Link-Time Optimization   | No      |
| lto-gc | LTO + GC sections        | No      |

Default: 8 builds per device (4 flash x 2 opt). Enable lto/lto-gc via workflow UI.

---

## D-Link DIR-825-B1

AR7161-based router with mtd-concat flash layout.

<details>
<summary>Partition Layout</summary>

Caldata is located in the middle of flash (0x660000-0x670000).
When replacing flash with a larger one, only the fwconcat1 partition expands.

```
+------------------+
| 0x000000 u-boot  |
| 0x040000 config  |
| 0x050000 fwconcat0 (firmware part 1)
| 0x660000 caldata | <- DOES NOT MOVE!
| 0x670000 fwconcat1 (firmware part 2)
| ...              | <- expands with larger flash
+------------------+
```

| Flash | fwconcat1 size      |
|-------|---------------------|
| 8MB   | 0x190000  ( 1.6MB)  |
| 16MB  | 0x990000  ( 9.6MB)  |
| 32MB  | 0x1990000 (25.6MB)  |
| 64MB  | 0x3990000 (57.6MB)  |

</details>

<details>
<summary>Flash Chips</summary>

| Flash | Chip     |
|-------|----------|
| 8MB   | Stock    |
| 16MB  | W25Q128  |
| 32MB  | W25Q256  |
| 64MB  | W25Q512  |

</details>

---

## HAME MPR-A5

RT5350-based portable 3G router. Uses HAME MPR-A1 compatible firmware.

Device was removed from OpenWrt 23.x (4/32 policy). This build adds it back via patch.

<details>
<summary>Partition Layout</summary>

Linear flash layout - firmware partition expands with larger flash.

```
+------------------+
| 0x000000 u-boot  | 192K
| 0x030000 u-boot-env | 64K
| 0x040000 factory | 64K (caldata)
| 0x050000 firmware |
| ...              | <- expands with larger flash
+------------------+
```

| Flash | Firmware size       |
|-------|---------------------|
| 8MB   | 0x7B0000  ( 7.7MB)  |
| 16MB  | 0xFB0000  (15.7MB)  |
| 32MB  | 0x1FB0000 (31.7MB)  |
| 64MB  | 0x3FB0000 (63.7MB)  |

</details>

<details>
<summary>Hardware</summary>

- SoC: Ralink RT5350 (360 MHz)
- RAM: 32 MB (upgradeable)
- Flash: 8-64 MB (upgradeable, stock 4 MB)
- WiFi: RT5350 built-in 802.11bgn
- USB: 1x USB 2.0 host
- Ethernet: 1x 100M

</details>

---

## GitHub Actions

| Workflow              | Description                      |
|-----------------------|----------------------------------|
| `check-release.yml`   | Hourly check, triggers both builds |
| `build-dir-825-b1.yml`| DIR-825-B1 firmware              |
| `build-hame-a5.yml`   | HAME MPR-A5 firmware             |

Firmware is built automatically on new OpenWrt release or manually via workflow_dispatch.

## License

GPL-2.0 (same as OpenWrt)
