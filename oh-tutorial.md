## Contents

- Acquiring OpenHarmony source code
- Introducing project structure and tooling
- Understanding Modularity and target systems
- Target audience: Operating System researchers, enthusiasts and device vendors


## OpenHarmony OS

- Opensource distributed Operating System
  - Permissive Apache-2.0 license
- Highly configurable for different scenarios
- Component based design 
  - Flexible tailoring to the hardware capabilities of a device

Note: Component based design allows flexible combinations of components tailored to the hardware capabilities of the device


## System types

- Mini system (MCUs, e.g. Cortex-M)
    - RAM >= 128 KiB
- Small system (application processors, e.g. Cortex-A)
    - RAM >= 1 MiB
- Standard system (application processors)
    - RAM >= 128 MiB

Note: 

- Mini system: Typical products include connection modules, sensors, and wearables for smart home.

- Small system:  This system provides higher security capabilities, standard graphics frameworks, and video encoding and decoding capabilities. Typical products include smart home IP cameras, electronic cat eyes, and routers, and event data recorders (EDRs) for easy travel.

- Standard system: This system provides a complete application framework supporting enhanced interactions, 3D GPU, hardware composer, diverse components, and various animations. Typical products include high-end refrigerator displays.


## Modularity

- Mini, small and standard systems have different requirements
- OpenHarmony offers high flexibility with optimized versions of components
- E.g. Kernel: Linux kernel, [LiteOS-A], [LiteOS-M]

[LiteOS-A]: https://docs.openharmony.cn/pages/v5.0/en/device-dev/kernel/kernel-small-overview.md
[LiteOS-M]: https://docs.openharmony.cn/pages/v5.0/en/device-dev/kernel/kernel-mini-overview.md


## Driver framework

- Device drivers are usually traditionally coupled to the kernel
- OpenHarmony offers multiple kernels
- [HDF] (Hardware Driver Foundation) provides unified driver abstraction

[HDF]: https://docs.openharmony.cn/pages/v5.0/en/device-dev/driver/driver-overview-foundation.md


![HDF Framework](assets/HDF-architecture.png)

Note:

The HDF architecture consists of the following:

    - Hardware Device Interface (HDI) layer: provides unified and stable APIs for hardware operations.

    - HDF: provides unified hardware resource management, driver loading management, device node management, device power management, and driver service models. It consists of the device management, service management, Device Host, and PnPManager modules.

    - Unified configuration interface (DevEco Studio): supports abstract description of hardware resources to shield hardware differences, and enables development of universal driver code that is not bound to configuration information. You can use HC-Gen to quickly generate configuration files. This unified configuration interface improves development and porting efficiency.

    - Operating system abstraction layer (OSAL): provides encapsulated kernel operation APIs, including the APIs for the memory, locks, threads, and semaphores, to shield operation differences between different systems.

    - Platform driver layer: provides unified APIs for peripheral drivers to operate board hardware, such as I2C, SPI, and UART buses, and uniformly abstracts the APIs for board hardware operations.

    - Peripheral driver model: provides common driver abstraction models for peripheral drivers to provide standard device drivers and implement driver model abstraction. With standard device driver models, you can deploy drivers through configuration without independent development. The driver model abstraction makes the drivers more general by shielding the interaction between drivers and different system components.



## Documentation

- [OpenHarmony](https://docs.openharmony.cn/pages/v5.0/)
- [Docs on Gitee](https://gitee.com/openharmony/docs/)
- [Github mirror](https://github.com/openharmony-rs/openharmony-docs)



## Obtaining OpenHarmony Source code


## OpenHarmony source code

- OpenHarmony is a highly configurable Operating System Framework
- Organized into Systems, Subsystems and Components
- Split into many repositories

[`repo`]: https://gerrit.googlesource.com/git-repo


## Obtaining the source code

- OpenHarmony is a multi-repository project
- Version-control: [`repo`] and git
- The [manifest] defines which repositories should be checked out

```sh [1-2 | 3 | 4]
repo init -u https://gitee.com/openharmony/manifest \
    -b OpenHarmony-5.0.3-Release
repo sync -c
repo forall -c 'git lfs pull'
```

[`repo`]: https://gerrit.googlesource.com/git-repo
[manifest]: https://gitee.com/openharmony/manifest


## Targeted Checkouts 

- Modularity means the OH often has multiple alternatives for a component
- `repo init` can be configured to just checkout a subset of the source code
- Check the [manifest readme] for a full list of options

Example: 
```bash
# code for the mini system + related chipsets
repo init -u URL -b BRANCH -g ohos:mini
# mini system + one specific chipset
repo init -u URL -b BRANCH -m chipsets/chipsetN.xml -g ohos:mini
```

[manifest Readme]: https://gitee.com/openharmony/manifest


## Build directories

| Directory | Description |
|-----|------|
| `build` | Build scripts and configuration |
| `out`   | Build output directory |
| `prebuilts` | Prebuilt toolchains |


## Core Source

| **Directory**| **Description**|
| -------- | -------- |
| applications | Application samples |
| base | Basic software service subsystem set and hardware service subsystem set.|
| drivers | Driver subsystem.|
| foundation | Basic system capability subsystem set.|
| kernel | Kernel subsystem.|
| test | Test subsystem.|

Note: 
    - foundation: E.g. ability, arkui, graphic, multimedia, window, ...
    - base: account, location, security, web


## Porting

- Currently the number of supported devices is limited
- [Mini], [Small] and [Standard] porting guides.
- [Showcase rk3568 board](https://docs.openharmony.cn/pages/v5.0/en/device-dev/porting/porting-dayu200-on_standard-demo.md)

[Mini]: https://docs.openharmony.cn/pages/v5.0/en/device-dev/porting/porting-minichip-overview.md
[Small]: https://docs.openharmony.cn/pages/v5.0/en/device-dev/porting/porting-smallchip-prepare-needs.md
[Standard]: https://docs.openharmony.cn/pages/v5.0/en/device-dev/porting/standard-system-porting-guide.md




## Building with build.sh

- [build.sh](https://docs.openharmony.cn/pages/v5.0/en/device-dev/quick-start/quickstart-pkg-common-build.md) build script.
- All options are documented [here](https://docs.openharmony.cn/pages/v5.0/device-dev/subsystems/subsys-build-all.md)

Example - Build OH for the rk3568 board:
```sh [ 1-2 | 3-4 ]
# First time setup
bash build/prebuilt_download.sh
# actual build command
./build.sh --product-name rk3568
```


## Build environment

- Requirements depend on built subset
- Currently requires x86 Ubuntu host
- [Official Dockerfile](https://gitee.com/openharmony/docs/blob/master/docker/Dockerfile)
  - Not updated in a while
- [My Dockerfile](https://gist.github.com/jschwe/0c6e69ef1dca87f50dd9a4d522aae000)
  - Based on official dockerfile, updated for this presentation
- Doesn't support arm


## build selected component

- Only build a single target / module
- Independently flash only the changed `.so` files to the device
- Beware of API / ABI changes!

```sh
./build.sh --product-name rk3568 \
  --build-target=<module_name>
```


## Flash selected component

- Root permissions required on device
- Ensure [hdc] from the SDK is in PATH.

```sh
hdc file send lib.so /data/local/tmp/lib.so
hdc shell chmod +x /data/local/tmp/lib.so
hdc shell mv /data/local/tmp/lib.so /system/lib/lib.so
# Reboot device or restart affected services
```


## hb tool

- [`hb`] offers an interactive interface
    - does not run on recent python versions
- [`uv`](https://docs.astral.sh/uv/getting-started/installation/) allows conveniently installing hb with the correct python version

```sh
uv python install 3.8
uv tool install build/hb --python 3.8
hb set
# interactive dialogue
hb build
```

[`hb`]: https://docs.openharmony.cn/pages/v5.0/en/device-dev/subsystems/subsys-build-all.md



## Summary

- You can use `repo` to checkout the sources 
- Modular architecture 
- Complete Build environment is still a bit tricky to setup
- Building individual modules works well
