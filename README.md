# armbian-onecloud
[![build](https://img.shields.io/github/workflow/status/hzyitc/armbian-onecloud/CI)](https://github.com/hzyitc/armbian-onecloud/actions) [![downloads](https://img.shields.io/github/downloads/hzyitc/armbian-onecloud/total)](https://github.com/hzyitc/armbian-onecloud/releases) [![downloads@latest](https://img.shields.io/github/downloads/hzyitc/armbian-onecloud/latest/total)](https://github.com/hzyitc/armbian-onecloud/releases/latest)

[README](README.md) | [中文文档](README_zh.md)

**All changes were push to [the official repository](https://github.com/armbian/build).**

## First-login

Hostname: `onecloud`

Account:  `root`

Password: `1234`

## Build Parameters

### `BOARD`=`onecloud`

### `BRANCH`={`edge`,`current`,`legacy`}

| BRANCH    | kernel version                                                                                          | eMMC | HDMI | VPU |
| :-:       | :-:                                                                                                     | :-:  | :-:  | :-: |
| `edge`    | [xdarklight's `v5.18-rc7`](https://github.com/xdarklight/linux/tree/meson-mx-integration-5.18-20220516) | ✔️¹  | ✔️  | ✔️ |
| `current` | `v5.15`                                                                                                 | ✔️¹  | ❌  | ❌ |
| `legacy`  | `v5.10`                                                                                                 | ✔️¹  | ❌  | ❌ |
> ¹: Need a patch

### `BUILD_DESKTOP`={`no`,`yes`}
See above. Only work with `BRANCH=edge`

## How to boot from `u-boot` ?

### Boot from `USB`

```
setenv bootdev "usb 0"
usb start
fatload ${bootdev} 0x20800000 boot.scr && autoscr 0x20800000
```

### Boot from `eMMC`

```
setenv bootdev "mmc 1"
fatload ${bootdev} 0x20800000 boot.scr && autoscr 0x20800000
```

## `GPIO`

In the board, there is a missing 44-pins chip (WiFi module possibly) which has lots of pins connected to the `SoC`. They are ablt to be used as `GPIO`.

Please check the `dts` (added by `patch/kernel/archive/meson-{5.10,5.18}/support-xunlei-onecloud.patch`) for more details.

NOTE: These pins were found in `V1.0 board`. Those in `V1.3 board` was not confirmed yet.

## Related link

[`armbian/build`](https://github.com/armbian/build) - Armbian offical

[`xdarklight/linux@meson-mx-integration-5.18-20220417`](https://github.com/xdarklight/linux/tree/meson-mx-integration-5.18-20220417) - `edge` kernel

[`S805_Datasheet V0.8 20150126.pdf`](https://dn.odroid.com/S805/Datasheet/S805_Datasheet%20V0.8%2020150126.pdf) - S805 datasheet
