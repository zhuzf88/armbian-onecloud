# armbian-onecloud
[![build](https://img.shields.io/github/workflow/status/hzyitc/armbian-onecloud/CI)](https://github.com/hzyitc/armbian-onecloud/actions) [![downloads](https://img.shields.io/github/downloads/hzyitc/armbian-onecloud/total)](https://github.com/hzyitc/armbian-onecloud/releases) [![downloads@latest](https://img.shields.io/github/downloads/hzyitc/armbian-onecloud/latest/total)](https://github.com/hzyitc/armbian-onecloud/releases/latest)

[README](README.md) | [中文文档](README_zh.md)

**All changes were push to [the official repository](https://github.com/armbian/build).**

## Build Parameters

### `BOARD`=`onecloud`

### `BRANCH`={`edge`,`current`,`legacy`}

| BRANCH    | kernel version                                                 | Boot             | Shutdown/Reboot | eMMC | HDMI | VPU |
| :-:       | :-:                                                            | :-:              | :-:             | :-:  | :-:  | :-: |
| `edge`    | [xdarklight's](https://github.com/xdarklight/linux) `v5.18-rc` | ✔️               | ✔️             | ✔️   | ✔️  | ✔️ |
| `current` | `v5.14`                                                        | ✔️               | ✔️             | ✔️   | ❌  | ❌ |
| `legacy`  | `v5.11`                                                        | ✔️               | ✔️             | ✔️   | ❌  | ❌ |
|           | <=`v5.10`                                                      | ✔️               | ✔️             | ❌   | ❌  | ❌ |
|           | >=`v5.15`                                                      | Take a long time | ❌             | ✔️   | ❌   | ❌ |

### `BUILD_DESKTOP`={`no`,`yes`}
See above. Only work with `BRANCH=edge`

## How to boot from `u-boot` ?

### Boot from `USB`

```
bootdev="usb 0"
rootdev="/dev/sda2"
usb start
fatload ${bootdev} 0x20800000 boot.scr && autoscr 0x20800000
```

### Boot from `eMMC`

```
bootdev="mmc 1"
rootdev="/dev/mmcblk1p2"
fatload ${bootdev} 0x20800000 boot.scr && autoscr 0x20800000
```

## `GPIO`

In the board, there is a missing 44-pins chip (WiFi module possibly) which has lots of pins connected to the `SoC`. They are ablt to be used as `GPIO`.

Please check the `dts` (added by `patch/kernel/archive/meson-{5.10,5.18}/support-xunlei-onecloud.patch`) for more details.

NOTE: These pins were found in `V1.0 board`. Those in `V1.3 board` was not confirmed yet.

## Known bug

### `V1.0 board` boots fail

Probably due to the power, `CPU` doesn't work well in `1.5GHz`. So the boot fails.
