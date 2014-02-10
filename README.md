crosstool-ng-samples
====================

Crosstool NG configuration samples to build toolchains for Android Emulator and arm device

Requirements
============

 - Ubuntu 12.04 (x86_64)
 - crosstool-ng 1.19.0
 - AOSP android-4.3.1_r1
 - Enough space on data partition (1024M)
 - Android SDK


Crosstool-NG configurations
===========================

|  Host  |  Target  | Linux     | binutils | C compiler             | C library    | Static    | Toolchain |
| ------ | -------- | --------- | -------- | ---------------------- | ------------ | --------- | --------- |
| x86_64 | i686     | 2.6.27.62 | 2.20.1a  | gcc 4.6.4              | glibc 2.12.2 | N         | Cross     |
| i686   | i686     | 2.6.27.62 | 2.20.1a  | gcc 4.6.4              | ulibc 2.12   | Y         | Canadian  |
| x86_64 | arm      | 3.0.87    | 2.20.1a  | gcc-linaro 4.6-2013.05 | glibc 2.9    | N         | Cross     |
| arm    | arm      | 3.0.87    | 2.20.1a  | gcc-linaro 4.6-2013.05 | glibc 2.9    | Y         | Canadian  |


 - GMP version (5.0.2) 
 - MPFR version (3.1.2) 
 - PPL version (0.11.2)
 - CLooG version (0.15.11)
 - MPC version (1.0.1) 

* Debug tools are disabled in order to reduce build time

Procedure
=========

Build for emulator x86
----------------------

```bash
# Step 1. Download Crosstool-NG configurations 
mkdir ~/crosstool-ng-configs
cd ~/crosstool-ng-configs
wget -O configs.tar.gz 'TODO'
tar -zxvf configs.tar.gz

# Step 2. build toolchains for host x86_64 and target i686
cd ~/crosstool-ng-configs/host-x86_64-target-i686
ct-ng i686-host-linux-gnu
ct-ng build
# Result can be found in ~/x-tools/i686-host-linux-gnu

# Step3. build toolchains for host i686 and target
cd ~/crosstool-ng-configs/host-i686-target-i686
ct-ng i686-target-linux-gnu
ct-ng build
# Result can be found in ~/x-tools/i686-target-linux-gnu

# Step4. Push toolchain to the emulator
adb root
adb remount
adb shell rm -rf /data/local/i686-target-linux-gnu
adb push ~/x-tools/i686-target-linux-gnu /data/local/i686-target-linux-gnu/

# Fix permissions of the files
cd ~/x-tools
find i686-target-linux-gnu -exec stat -c '%a %n'  {} \;| awk '{ print  "adb shell chmod", $1, "/data/local/"$2; }' > adb_fix_permissions.sh
chmod a+x adb_fix_permissions.sh
./adb_fix_permissions.sh
```

Build “Hello World” on Emulator
-------------------------------

```bash
adb shell
cd /data/local/
echo -e "#include <stdio.h>\nint main() {\n    printf(\"Hello World.\");\n    return 0;\n}" > helloworld.c
/data/local/i686-target-linux-gnu/bin/i686-target-linux-gnu-gcc helloworld.c -o helloworld -static
./helloworld
```

Build for device
----------------

Based on “Build for emulator x86”, 
Changes in used names:
 - host-x86_64-target-i686 -> host-x86_64-target-arm
 - host-i686-target-i686 -> host-arm-target-arm
 - i686-host-linux-gnu -> arm-host-linux-gnueabi
 - i686-target-linux-gnu -> arm-target-linux-gnueabi








