# nvidia_avuidea_j120support
#### Follow these instructions to build and install the kernel Image and device tree.
###1. Download and install the Toolchain
###### NVIDIA recommends using the Linaro 7.3.1 2018.05 toolchain for L4T 32.*

###### Execute the following commands to extract the toolchain:

`mkdir $HOME/l4t-gcc`

`cd $HOME/l4t-gcc`

`wget http://releases.linaro.org/components/toolchain/binaries/7.3-2018.05/aarch64-linux-gnu/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz
tar xf gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz`

###2. Download the kernel sources
###### You can download the kernel source files and then manually extract them. it is recommended to instead sync with git.
######In a browser, navigate to: https://developer.nvidia.com/embedded/downloads. Locate and download the L4T Sources for your release. (L4T Sources 32.*) and extract the files in the same directory

###### Execute the following commands to extract the kernel:

`cd public_sources/`

`JETSON_TX2_KERNEL_SOURCE=$(pwd)`

`tar -xvf kernel_src.tbz2`

###### Before step 3, you need to copy the attached .dts and .dtsi files into the respective files in the kernel directory
`$HOME/l4t-gcc/public_sources//hardware/nvidia/platform/t18x/` 

`../t18x/common`

`../t18x/quill`

###3. Compile kernel and dtb

######Follow the steps:
`cd $JETSON_TX2_KERNEL_SOURCE`

`CROSS_COMPILE=$HOME/l4t-gcc/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-`

`KERNEL_OUT=$JETSON_TX2_KERNEL_SOURCE/build`
`KERNEL_MODULES_OUT=$JETSON_TX2_KERNEL_SOURCE/modules`

`make -C kernel/kernel-4.9/ ARCH=arm64 O=$KERNEL_OUT tegra_defconfig`

`make -C kernel/kernel-4.9/ ARCH=arm64 O=$KERNEL_OUT menuconfig # if is required to change the default configuration`

`make -C kernel/kernel-4.9/ ARCH=arm64 O=$KERNEL_OUT CROSS_COMPILE=${CROSS_COMPILE} -j6 Image`

`make -C kernel/kernel-4.9/ ARCH=arm64 O=$KERNEL_OUT CROSS_COMPILE=${CROSS_COMPILE} -j6 dtbs`

`make -C kernel/kernel-4.9/ ARCH=arm64 O=$KERNEL_OUT CROSS_COMPILE=${CROSS_COMPILE} -j6 modules`

`make -C kernel/kernel-4.9/ ARCH=arm64 O=$KERNEL_OUT modules_install INSTALL_MOD_PATH=$KERNEL_MODULES_OUT`

### Flash Jetson TX2
######This guide assumes that the user already have JetPack 4.2 installed. This link contains details about how to install JetPack 4.2: https://docs.nvidia.com/sdk-manager/download-run-sdkm/index.html

######Make sure the Jetson TX2 is in recovery mode

######JETPACK_4_2 contains the directory where JetPack 4.2 was installed. For example.


`cd ${JETPACK_4_2}/JetPack_4.2_Linux_P3310/Linux_for_Tegra`
###### Copy kernel generated
`cp $JETSON_TX2_KERNEL_SOURCE/build/arch/arm64/boot/Image kernel/`
###### Copy device tree generated
`cp $JETSON_TX2_KERNEL_SOURCE/build/arch/arm64/boot/dts/tegra186-quill-p3310-1000-c03-00-base.dtb kernel/dtb/`
###### Copy new modules
`sudo cp -a $JETSON_TX2_KERNEL_SOURCE/modules/lib rootfs/`
###### Flash memory 
`sudo ./flash.sh jetson-tx2 mmcblk0p1`



