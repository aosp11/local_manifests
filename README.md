# Project

This projects aims to create an upgradable AOSP build system for the Oneplus 5 (Cheeseburger).
No repository from AOSP is changed: no commit over AOSP is at the moment present on these sources.

All the changes are provided in separate repositories: this allows to upgrade Android with minimal effort, potentially also for future major versions.

Most of the added repositories is provided by LineageOS or CAF, so thanks to them for those repos.

# Build instructions
Follow the instructions from Google to setup a machine to build Android 11:
https://source.android.com/setup/build/initializing

Then, sync all the sources:
```
$ repo init -u https://android.googlesource.com/platform/manifest -b android-11.0.0_r27 --depth=1 
$ cd .repo
$ git clone https://github.com/aosp11/local_manifests.git local_manifests
$ cd ..
$ repo sync  --force-sync --current-branch --no-tags --no-clone-bundle --optimized-fetch --prune -j$(nproc --all)
```
Turn on caching to speed up build
Make use of ccache if you want to speed up subsequent builds by running:
```
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
```
and adding that line to your ~/.bashrc file. Then, specify the maximum amount of disk space you want ccache to use by typing this:
```
ccache -M 100G
```
then:
```
$ source build/envsetup.sh
$ lunch aosp_dumpling-userdebug
$ make -j$(nproc --all)
```
the images will be available in `out/target/product/dumpling`.

To build an OTA:
```
$ source build/envsetup.sh
$ lunch aosp_dumpling-userdebug
$ make -j(nproc --all)
$ make otatools-package -j$(nproc --all)
$ make otapackage -j$(nproc --all)
```
and the OTA zip will be available in `out/target/product/dumpling`.

# Flash instructions
The images can be flashed using fastboot:
```
$ fastboot flash boot out/target/product/cheeseburger/boot.img
$ fastboot flash recovery out/target/product/cheeseburger/recovery.img
$ fastboot flash system out/target/product/cheeseburger/system.img
$ fastboot flash vendor out/target/product/cheeseburger/vendor.img
```
Format data if this is a first flash:
```
$ fastboot format userdata
```
Note that not all the fastboot versions support the 'format' command.

The OTA can be flashed from the recovery (using the 'ADB sideload' feature) or using third party recoveries like TWRP.

