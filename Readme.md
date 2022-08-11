# Install platform tools
Ubuntu: `sudo apt-get install android-tools-adb`

Other: https://developer.android.com/studio/releases/platform-tools#downloads

# Install Magisk
- download apk from https://github.com/topjohnwu/Magisk/releases
- Install magisk
  - Enable developer mode
  - Enable usb debugging
  - `adb install Magisk-v25.1.apk`
  
# Getting stock boot image
- use adb shell to find boot_* on device (/dev/block/by-name)
- show why we can't use this file: 
````
130|crosshatch:/dev/block/by-name $ ls -lZ boot*
lrwxrwxrwx 1 root root u:object_r:block_device:s0  16 1970-04-19 13:31 boot_a -> /dev/block/sda11
lrwxrwxrwx 1 root root u:object_r:block_device:s0  16 1970-04-19 13:31 boot_b -> /dev/block/sda12
crosshatch:/dev/block/by-name $ ls -lZ /dev/block/sda11
brw------- 1 root root u:object_r:boot_block_device:s0  8,  11 1970-04-19 13:31 /dev/block/sda11
````
- extract boot.img from stock firmware (image-crosshatch-sp1a.210812.016.c1.zip) available at https://developers.google.com/android/images
- `T:\MCH\RootingfWorkshop>adb push boot.img /sdcard/Download`

# Rooting stock boot.img
- Start magisk app and select boot.img from Downloads
- `adb pull /sdcard/Download`
````
deponald@host:/mnt/t/MCH/RootingfWorkshop$ ls -l Download/
total 131072
-rwxrwxrwx 1 deponald deponald 67108864 Jun 24 18:52 boot.img
-rwxrwxrwx 1 deponald deponald 67108864 Jun 24 18:52 magisk_patched-25100_VL6BZ.img
````
- `mv Download/magisk_patched-25100_VL6BZ.img boot_magisk.img`

# Booting from patched boot image
- Try from original stock device
  - `adb reboot bootloader`
  - `fastboot boot boot_magisk.img`
````
Sending 'boot.img' (65536 KB)                      OKAY [  1.606s]
Booting                                            FAILED (remote: 'Fastboot command (boot) is not allowed when locked')
fastboot: error: Command failed 
````
- Unlock the bootloader
  - `fastboot flashing unlock`
  - `fastboot boot boot_magisk.img`
````
Sending 'boot.img' (65536 KB)                      OKAY [  1.606s]
Booting                                            OKAY [  5.272s]
Finished. Total time: 6.933s
````
- Enable developer mode and usb debugging again
- Install magisk again
- Get a adb shell and try su (don't forget to grant permissions in the phone GUI)

````
crosshatch:/ $ id -Z
u:r:shell:s0
crosshatch:/ $ su
127|crosshatch:/ # id -Z
u:r:magisk:s0
````
# Permanent rooting
- boot with patched boot_magisk.img
- Use Magisk app Direct Install function to overwrite stock boot.img with boot_magisk.img  
