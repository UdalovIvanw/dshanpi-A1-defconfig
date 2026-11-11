# dshanpi-A1-defconfig

U-Boot defconfigs for the **100ASK DshanPi A1** (Rockchip RK3576) board.

## Contents

| File | Description |
|------|-------------|
| `dshanpi-a1-rk3576_defconfig` | Base U-Boot configuration for DshanPi A1, derived from `evb-rk3576_defconfig` and using the upstream `rk3576-100ask-dshanpi-a1` device tree. |
| `dshanpi-a1-flipperos_defconfig` | Variant for booting Flipper OS (Debian, Btrfs root, BLS boot entries) from SD/eMMC. |

## Requirements

- U-Boot source tree with the `rk3576-100ask-dshanpi-a1` device tree (upstream U-Boot) [specify version, e.g. 2026.10]
- Rockchip binaries from [rkbin](https://github.com/rockchip-linux/rkbin): TPL (DDR init) `[version, e.g. v1.03]` and BL31
- AArch64 cross-toolchain (e.g. `aarch64-linux-gnu-`)

## Build

```bash
# 1. Copy the desired defconfig into the U-Boot tree
cp dshanpi-a1-rk3576_defconfig /path/to/u-boot/configs/

# 2. Provide rkbin blobs
export ROCKCHIP_TPL=/path/to/rkbin/bin/rk35/rk3576_ddr_*.bin
export BL31=/path/to/rkbin/bin/rk35/rk3576_bl31_*.elf

# 3. Build
cd /path/to/u-boot
export CROSS_COMPILE=aarch64-linux-gnu-
make dshanpi-a1-rk3576_defconfig
make -j$(nproc)
```

The result is `u-boot-rockchip.bin`.

## Flashing

**SD card** (write at sector 64):

```bash
sudo dd if=u-boot-rockchip.bin of=/dev/sdX seek=64 conv=fsync
```

> Double-check `/dev/sdX` before running `dd` — it will overwrite data.

## Notes

- The Flipper OS variant sets `kernel_addr_r=0x41000000` via `PREBOOT`, because the default address leaves too little room for the kernel. 
- Enabled options include `CONFIG_BOOTMETH_BLS`, Btrfs support, and the Motorcomm PHY driver.

- For working wifi you need to For Wi-Fi to work, you need to install the drivers for each FlipperOS profile
- To ensure the kernel loads successfully, you must disconnect the USB and also limit the speed of the eMMC storage to prevent errors (see DshanPi-FlipperOS-linux-Scripts)

## Status

Work in progress. A patch to upstream U-Boot is planned.

## License

[GPL-2.0+ — same as U-Boot]

## Credits

- [U-Boot](https://source.denx.de/u-boot/u-boot)
- [Rockchip rkbin](https://github.com/rockchip-linux/rkbin)
- [100ASK DshanPi A1](https://github.com/100askTeam) 
- [Flipper One Docs](https://docs.flipper.net/one)
