# armbian-onecloud

[![build](https://img.shields.io/github/workflow/status/hzyitc/armbian-onecloud/CI)](https://github.com/hzyitc/armbian-onecloud/actions) [![downloads](https://img.shields.io/github/downloads/hzyitc/armbian-onecloud/total)](https://github.com/hzyitc/armbian-onecloud/releases) [![downloads@latest](https://img.shields.io/github/downloads/hzyitc/armbian-onecloud/latest/total)](https://github.com/hzyitc/armbian-onecloud/releases/latest)

[README](README.md) | [中文文档](README_zh.md)

**所有修改均已提交至[官方仓库](https://github.com/armbian/build)，您可以直接使用[官方仓库](https://github.com/armbian/build)进行编译。**

## 第一次登录

主机名: `onecloud`

账号:  `root`

密码: `1234`

## 编译参数

### `BOARD`=`onecloud`

### `BRANCH`={`edge`,`current`,`legacy`}

| BRANCH    | 内核版本                                                                                                 | eMMC | HDMI | VPU |
| :-:       | :-:                                                                                                     | :-:  | :-:  | :-: |
| `edge`    | [xdarklight's `v5.18-rc7`](https://github.com/xdarklight/linux/tree/meson-mx-integration-5.18-20220516) | ✔️¹  | ✔️  | ✔️ |
| `current` | `v5.15`                                                                                                 | ✔️¹  | ❌  | ❌ |
| `legacy`  | `v5.10`                                                                                                 | ✔️¹  | ❌  | ❌ |
> ¹: Need a patch

### `BUILD_DESKTOP`={`no`,`yes`}
参见上表，仅在 `BRANCH=edge` 时有效

## 如何从`u-boot`启动？

### 从`USB`启动

```
setenv bootdev "usb 0"
usb start
fatload ${bootdev} 0x20800000 boot.scr && autoscr 0x20800000
```

### 从`eMMC`启动

```
setenv bootdev "mmc 1"
fatload ${bootdev} 0x20800000 boot.scr && autoscr 0x20800000
```

## `GPIO`

板子上面有一个44脚的空焊盘(推测可能是WiFi模块)。上面有大量直连`SoC`的引脚，可用作`GPIO`。

具体定义参见`dts`(由 `patch/kernel/archive/meson-{5.10,5.18}/support-xunlei-onecloud.patch` 添加)

注：`dts`中的引脚是在`V1.0的板子`上测量出来的，未在`V1.3的板子`上面验证。

## 相关链接

[`armbian/build`](https://github.com/armbian/build) - Armbian官方

[`xdarklight/linux@meson-mx-integration-5.18-20220417`](https://github.com/xdarklight/linux/tree/meson-mx-integration-5.18-20220417) - `edge`内核

[`S805_Datasheet V0.8 20150126.pdf`](https://dn.odroid.com/S805/Datasheet/S805_Datasheet%20V0.8%2020150126.pdf) - S805数据手册
