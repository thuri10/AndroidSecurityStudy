
<!— @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} —>
<!— code_chunk_output —>

* [1.3 Android/iOS](#13-androidios)
* [1.3.1 Android root](#131-android-root)
* [1.3.1.1 Hardware Preparation] (#1311 - Hardware Preparation)
* [1.3.1.2 brush into official 'Android 8.1'] (#1312- brush into official android-81)
* [1.3.1.3 flush 'twrp recovery'] (#1313-flush twrp-recovery)
* [1.3.1.4 flush 'Magisk'] (#1314-flush magisk)
* [1.3.1.5 Get 'root' Permission] (#1315- Get root Permission)
* [1.3.2 Android frida-server Installation] (#132-android-frida-server-Installation)

<!— /code_chunk_output —>


### 1.3 Android/iOS

#### 1.3.1 Android root

##### 1.3.1.1 Hardware Preparation

In terms of mobile phone hardware, Google's 'Nexus' and 'Pixel' series are preferred first and foremost. In view of the fact that the test cases on the Web site were tested on the 'Nexus 5X' mobile phone, the system was tested on the 'Android 8.1.0' version, we will also use this mobile phone, and this version of the system, for experimentation.

The Web site also states that it is not possible to fully test on all mobile phones and all ROMs, and this is certainly a case-by-case analysis.

>Also note that most of our recent testing has been taking place on a Nexus 5X running Android 8.1.0. Older and newer ROMs may work, but if you’re running into basic issues like Frida crashing the system when launching an app, this is due to ROM-specific quirks. We cannot test on all possible devices, so we count on your help to improve on this. However if you’re just starting out with Frida it is strongly recommended to go with a Nexus device with factory software, or an official 8.x emulator image for arm or arm64.(x86 may work too but has gone through significantly less testing.)

The Web site continues to indicate that it is also OK to choose a simulator for the 'Android 8.x' system and that the architecture must choose either 'arm' or 'arm64', and that 'x86' may have a 'crash' situation.

'Nexus 5X' The hardware parameters for this phone are:

![](pic/1.3.1.1a.png)
![](pic/1.3.1.1b.png)

##### 1.3.1.2 brush off official'Android 8.1'

frida's website identifies 'factory software' as Google's [official factory image site](https://developers.google.com/android/images), which may require scientific Internet access. There are some instructions in the middle of the website, and on the right is the list of mobile models, where we choose the 'Nexus 5X' model 'bullhead'.

![](pic/1.3.1.2a.png)

You can see support from Android 6 to Android 8, and the latest support goes to '8.1.0'.

![](pic/1.3.1.2b.png)
...
![](pic/1.3.1.2c.png)

We use the 'wget' command to download the latest version of '8.1.0 (OPM7.181205.001, Dec 2018)', which is the fastest.

![](pic/1.3.1.2d.png)

Remember to verify 'SHA-256 Checksum' after the download is complete and you must agree with the official website or the download file is corrupt and cannot be used and you must download it again.

```bash
$ openssl dgst -sha256 bullhead-opm7.181205.001-factory-5f189d84.zip
SHA256(bullhead-opm7.181205.001-factory-5f189d84.zip)= 5f189d84781a26b49aca0de84a941a32ae0150da0aab89f1d7709d56c31b3c0a
```

It is clear that 'SHA-256 Checksum' is the same as the Web site and that the next step is to flush into the system.

First, enter the phone into the 'fastboot' state, following the procedure:

1. Disconnect the 'USB' cable and ensure that the cell phone has about '80%' of the power;
2. Turn off your mobile phone completely;
3. Press and hold the Volume Down Arrow key and the Power On button at the same time;
4. The mobile phone will enter the 'fastboot' state;

The status is as follows:

![](pic/1.3.1.2e.jpeg)

>PS: If the phone is not unlocked, that is, 'DEVICE STATE - 'display 'locked', then the phone needs to be unlocked first, and 'unlocked', that is, the status on the diagram, before it can be subsequently swiped into 'recovery' and so on. 'Nexus 5X' phone unlocking is relatively simple and is not being demonstrated here.

The phone uses a USB cable to connect to the computer, runs a script, and brushes the system into the phone.

```bash
$ unzip bullhead-opm7.181205.001-factory-5f189d84.zip
Archive: bullhead-opm7.181205.001-factory-5f189d84.zip
creating: bullhead-opm7.181205.001/
inflating: bullhead-opm7.181205.001/radio-bullhead-m8994f-2.6.42.5.03.img
inflating: bullhead-opm7.181205.001/bootloader-bullhead-bhz32c.img
inflating: bullhead-opm7.181205.001/flash-all.bat
inflating: bullhead-opm7.181205.001/flash-all.sh
extracting: bullhead-opm7.181205.001/image-bullhead-opm7.181205.001.zip
inflating: bullhead-opm7.181205.001/flash-base.sh
$ cd bullhead-opm7.181205.001
$ ./flash-all.sh
target reported max download size of 536870912 bytes
sending 'bootloader'(4610 KB)...
OKAY [ 0.197s]
writing 'bootloader'...
OKAY [ 0.174s]
finished. total time: 0.371s
rebooting into bootloader...
OKAY [ 0.020s]
finished. total time: 0.020s
target reported max download size of 536870912 bytes
sending 'radio'(56630 KB)...
OKAY [ 1.629s]
writing 'radio'...
OKAY [ 0.917s]
finished. total time: 2.546s
rebooting into bootloader...
OKAY [ 0.020s]
finished. total time: 0.020s
extracting android-info.txt(0 MB)to RAM...
extracting boot.img(11 MB)to disk... took 0.125s
target reported max download size of 536870912 bytes
archive does not contain 'boot.sig'
archive does not contain 'dtbo.img'
archive does not contain 'dt.img'
extracting recovery.img(17 MB)to disk... took 0.093s
archive does not contain 'recovery.sig'
extracting system.img(1909 MB)to disk... took 17.733s
archive does not contain 'system.sig'
archive does not contain 'vbmeta.img'
extracting vendor.img(185 MB)to disk... took 1.895s
archive does not contain 'vendor.sig'
wiping userdata...
mke2fs 1.43.3(04-Sep-2016)
Creating filesystem with 2874363 4k blocks and 719488 inodes
Filesystem UUID: 79218aab-c322-4823-a83f-5a26ad3fd27e
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208

Allocating group tables: done
Writing inode tables: done
Creating journal(16384 blocks): done
Writing superblocks and filesystem accounting information: done

wiping cache...
mke2fs 1.43.3(04-Sep-2016)
Creating filesystem with 24576 4k blocks and 24576 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal(1024 blocks): done
Writing superblocks and filesystem accounting information: done

—
Bootloader Version...: BHZ32c
Baseband Version.: M8994F-2.6.42.5.03
Serial Number.: 00f4d2e97de8bd23
—
checking product...
O
```