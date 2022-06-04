# armbian-onecloud
[README](README.md) | [中文文档](README_zh.md)

**除了`启动脚本`和`USB修复`外的所有修改均已提交至[官方仓库](https://github.com/armbian/build)，您可以直接使用[官方仓库](https://github.com/armbian/build)进行编译。**

## ❗❗❗ 需要反馈 ❗❗❗

请使用[test-usb-ci-20220603-2028](https://github.com/hzyitc/armbian-onecloud/releases/tag/test-usb-ci-20220603-2028)和[test-usb-ci-20220603-2029](https://github.com/hzyitc/armbian-onecloud/releases/tag/test-usb-ci-20220603-2029)，测试靠近`HDMI`的`USB`能否正常使用。

对于`V1.0的板子`，***这两者*** 应该都能正常工作。

对于`V1.3的板子`，应该 ***只有后者*** 能正常工作。

请反馈到[Issue #5](https://github.com/hzyitc/armbian-onecloud/issues/5)。

## 编译参数

### `BOARD`=`onecloud`

### `BRANCH`={`edge`,`current`,`legacy`}

| BRANCH    | 内核版本                                                        | 启动  | 关机/重启 | eMMC | HDMI | VPU |
| :-:       | :-:                                                            | :-:   | :-:      | :-:  | :-:  | :-: |
| `edge`    | [xdarklight's](https://github.com/xdarklight/linux) `v5.18-rc` | ✔️    | ✔️      | ✔️  | ✔️   | ✔️  |
| `current` | `v5.14`                                                        | ✔️    | ✔️      | ✔️  | ❌   | ❌  |
| `legacy`  | `v5.11`                                                        | ✔️    | ✔️      | ✔️  | ❌   | ❌  |
|           | <=`v5.10`                                                      | ✔️    | ✔️      | ❌  | ❌   | ❌  |
|           | >=`v5.15`                                                      | 非常久 | ❌      | ✔️  | ❌   | ❌  |

### `BUILD_DESKTOP`={`no`,`yes`}
参见上表，仅在 `BRANCH=edge` 时有效

## 启动脚本

### 从`USB`启动

```
setenv bootargs "root=/dev/sda2 rootwait rw console=ttyAML0,115200n8 no_console_suspend consoleblank=0"

fatload usb 0 0x20800000 /uImage
fatload usb 0 0x22000000 /uInitrd
fatload usb 0 0x21800000 /dtb/meson8b-onecloud.dtb

fdt addr 21800000

bootm 0x20800000 0x22000000 0x21800000
```

### 从`eMMC`启动

```
setenv bootargs "root=/dev/mmcblk1p2 rootwait rw console=ttyAML0,115200n8 no_console_suspend consoleblank=0"

fatload mmc 1 0x20800000 /uImage
fatload mmc 1 0x22000000 /uInitrd
fatload mmc 1 0x21800000 /dtb/meson8b-onecloud.dtb

fdt addr 21800000

bootm 0x20800000 0x22000000 0x21800000
```

### 为什么不提交到[官方仓库](https://github.com/armbian/build)？

由于`U-Boot`中并没有提供可以判断当前引导设备的方法，我暂时想不出通用`启动脚本`的写法。

当然也可以按照一定的顺序尝试启动，但我认为这可能造成用户困惑。

比如，用户运行了`eMMC`上的`启动脚本`，但默认尝试顺序却是先尝试`USB`，因此启动了`USB`上的`内核`。

因此，在我没找到合适的方法前，暂时不会提交到[官方仓库](https://github.com/armbian/build)。

## `GPIO`

板子上面有一个44脚的空焊盘(推测可能是WiFi模块)。上面有大量直连`SoC`的引脚，可用作`GPIO`。

具体定义参见`dts`(由 `patch/kernel/archive/meson-{5.10,5.18}/support-xunlei-onecloud.patch` 添加)

注：`dts`中的引脚是在`V1.0的板子`上测量出来的，未在`V1.3的板子`上面验证。

## 已知bug

### 编译错误

```
cache/toolchain/gcc-arm-8.3-2019.03-x86_64-arm-linux-gnueabihf/bin/../lib/gcc/arm-linux-gnueabihf/8.3.0/plugin/include/builtins.h:23:10: fatal error: mpc.h: No such file or directory
make[2]: *** [scripts/gcc-plugins/Makefile:47: scripts/gcc-plugins/arm_ssp_per_task_plugin.so] Error 1
make[1]: *** [scripts/Makefile.build:496: scripts/gcc-plugins] Error 2
make: *** [Makefile:1197: scripts] Error 2
[ error ] ERROR in function compile_kernel [ main.sh:588 -> main.sh:489 -> compilation.sh:507 -> general.sh:0 ]
```

安装libmpc-dev

```
apt install libmpc-dev
```

### 靠近`HDMI`的`USB`不可用

已在 [usb分支](https://github.com/hzyitc/armbian-onecloud/tree/usb) 修复，但**需要测试**。参见[❗❗❗ 需要反馈 ❗❗❗](#-需要反馈-)。

***注：该修复还未提交到[官方仓库](https://github.com/armbian/build)。***

#### 原因？

由于我自己需要使用[USB Gadget](https://www.kernel.org/doc/html/latest/driver-api/usb/gadget.html)，因此我把`USB0`的模式设置成了`otg`。具体见`dts`(由 `patch/kernel/archive/meson-{5.10,5.18}/support-xunlei-onecloud.patch` 添加)中的 `&usb0/dr_mode` 。

在早期测试中，我发现它可以自动识别到`USB从设备`，因此我就直接提交到[官方仓库](https://github.com/armbian/build)。但后来又不行了。

`V1.3的板子`可能由于没有把`VBUS`连到`SoC`，因此在`ACA`检查时会失败，导致`USB0`被禁用。

#### 为什么还没提交到[官方仓库](https://github.com/armbian/build)？

我想尝试支持`usb-role-switch`来允许系统切换靠近`HDMI`的`USB`的`模式`。
