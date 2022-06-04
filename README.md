# armbian-onecloud
[README](README.md) | [中文文档](README_zh.md)

**Except `bootscript` or `Fix USB` all changes were push to [the official repository](https://github.com/armbian/build).**

## ❗❗❗ Need feedback ❗❗❗

Please try [test-usb-ci-20220603-2028](https://github.com/hzyitc/armbian-onecloud/releases/tag/test-usb-ci-20220603-2028) and [test-usb-ci-20220603-2029](https://github.com/hzyitc/armbian-onecloud/releases/tag/test-usb-ci-20220603-2029) to check whether `USB` closed to the `HDMI` working.

For `V1.0 board`, ***both of them*** should work.

For `V1.3 board`, ***only the second one*** should work.

Please feedback to [Issue #5](https://github.com/hzyitc/armbian-onecloud/issues/5).

## Build Parameters

### `BOARD`=`onecloud`

### `BRANCH`={`edge`,`current`,`legacy`}

| BRANCH    | kernel version                                                 | Boot  | Shutdown/Reboot | eMMC | HDMI | VPU |
| :-:       | :-:                                                            | :-:              | :-:             | :-:  | :-:  | :-: |
| `edge`    | [xdarklight's](https://github.com/xdarklight/linux) `v5.18-rc` | √                | √               | √    | √    | √   |
| `current` | `v5.14`                                                        | √                | √               | √    | ×    | ×   |
| `legacy`  | `v5.11`                                                        | √                | √               | √    | ×    | ×   |
|           | <=`v5.10`                                                      | √                | √               | ×    | ×    | ×   |
|           | >=`v5.15`                                                      | Take a long time | ×               | √    | ×    | ×   |

### `BUILD_DESKTOP`={`no`,`yes`}
See above. Only work with `BRANCH=edge`

## Bootscript

### Boot from `USB`

```
setenv bootargs "root=/dev/sda2 rootwait rw console=ttyAML0,115200n8 no_console_suspend consoleblank=0"

fatload usb 0 0x20800000 /uImage
fatload usb 0 0x22000000 /uInitrd
fatload usb 0 0x21800000 /dtb/meson8b-onecloud.dtb

fdt addr 21800000

bootm 0x20800000 0x22000000 0x21800000
```

### Boot from `eMMC`

```
setenv bootargs "root=/dev/mmcblk1p2 rootwait rw console=ttyAML0,115200n8 no_console_suspend consoleblank=0"

fatload mmc 1 0x20800000 /uImage
fatload mmc 1 0x22000000 /uInitrd
fatload mmc 1 0x21800000 /dtb/meson8b-onecloud.dtb

fdt addr 21800000

bootm 0x20800000 0x22000000 0x21800000
```

### Why not contribute to [the official repository](https://github.com/armbian/build)？

There's no way to detect which device we are booting from. So I can't write a common `bootscript`.

Although we can try to boot in a certain order, but this may make users confused.

For example, we run the `bootscript` in `eMMC`, but the first order is `USB`. So We boot the `kernel` in `USB`.

I willn't contribute to [the official repository](https://github.com/armbian/build) until I find a good solution.

## `GPIO`

In the board, there is a missing 44-pins chip (WiFi module possibly) which has lots of pins connected to the `SoC`. They are ablt to be used as `GPIO`.

Please check the `dts` (added by `patch/kernel/archive/meson-{5.10,5.18}/support-xunlei-onecloud.patch`) for more details.

NOTE: These pins were found in `V1.0 board`. Those in `V1.3 board` was not confirmed yet.

## Known bug

### Compile error

```
cache/toolchain/gcc-arm-8.3-2019.03-x86_64-arm-linux-gnueabihf/bin/../lib/gcc/arm-linux-gnueabihf/8.3.0/plugin/include/builtins.h:23:10: fatal error: mpc.h: No such file or directory
make[2]: *** [scripts/gcc-plugins/Makefile:47: scripts/gcc-plugins/arm_ssp_per_task_plugin.so] Error 1
make[1]: *** [scripts/Makefile.build:496: scripts/gcc-plugins] Error 2
make: *** [Makefile:1197: scripts] Error 2
[ error ] ERROR in function compile_kernel [ main.sh:588 -> main.sh:489 -> compilation.sh:507 -> general.sh:0 ]
```

Install libmpc-dev

```
apt install libmpc-dev
```

### The `USB` closed to the `HDMI` doesn't work

Fix in [branch usb](https://github.com/hzyitc/armbian-onecloud/tree/usb), but **need to be tested**. See [❗❗❗ Need feedback ❗❗❗](#-need-feedback-).

***NOTE: Not contribute to [the official repository](https://github.com/armbian/build) yet.***

#### Why?

The mode of `USB0` was set to `otg` due to my need for [USB Gadget](https://www.kernel.org/doc/html/latest/driver-api/usb/gadget.html). Check the `&usb0/dr_mode` in the `dts` (added by `patch/kernel/archive/meson-{5.10,5.18}/support-xunlei-onecloud.patch`).

In early test, I notice that the `USB Slave` can be detected. So I contributed to [the official repository](https://github.com/armbian/build) directly. But it doesn't working now.

For `V1.3 board`, probably due to not connecting `VBUS` to `SoC`, the `ACA` check will fail. So the `USB0` will be disabled.

#### Why not contribute to [the official repository](https://github.com/armbian/build) yet ?

I want to try to support `usb-role-switch` which allow the system to switch the `mode` of `USB` the `USB` closed to the `HDMI`.
