[toc]

# Pixel4 Tile: OnePlus 7-brush NetHunter (android10) guide

## Intro

## Why use the OnePlus7 device
1. Kali Nethunter officially supports OnePlus 7
2. The OnePlus7 is an OnePlus previous generation model that is inexpensive and performs as well as the Pixel4 generation
3. The OnePlus7 has the Qualcomm 9008 brick-rescue model, and the machine can be restored no matter how messy.
>> Brick Rescue mode is a feature provided by Qualcomm CPU
4. OnePlus7 has numerous tripartite rom packages on xda and plays with it


Based on the OnePlus 7, this is the performance equivalent of four generations of Pixel phones. The operating environment is Android 10. The accessories used in this paper are located in Baidu Disk:Link:https://pan.baidu.com/s/1gtmCfqfQmEX5JvMZtvUH7wExtraction code:euuo

This series is a good series of works for the students. The accessories, apk, code and so on, are in my project. You can choose from:

[https://github.com/r0ysue/AndroidSecurityStudy](20230403https://github.com/r0ysue/AndroidSecurityStudy)

## It's a myth how to make a brush. It's a science
1. The brush requires testing the various versions of the tool, and it is not always that you have taken the wrong steps, it may simply be that the wrong version of the kit is being used
Scenario: Provide all toolkits used in the following steps, inside the enclosure
2. System Version, There are so many system versions of OnePlus that it will affect the results that the bootloader may not be unlocked (here's a sad story)
Scenario: Provide a Brush Firmware Pack, Start Over, This Is Sure

## Start Swiping
### Download and Install Qualcomm Driver

1. Install the Qualcomm 9008 driver named "QDLoader_HS-USB_Driver_64bit_Setup.exe"
+ Install
![11](2023041811.png)
![12](2023041812.png)
![13](2023041813.png)
![14](2023041814.png)
![15](2023041815.png)
+ Open cmd with administrator privileges, enter the command to turn on test mode, and then restart the computer
```powershell
bcdedit /set testsigning on
```
+ Check if the test mode is turned on and the following image in the lower right corner of the PC's desktop after reboot indicates success, to continue to the next step

![19](2023041819.png)

### Flash in the firmware package

1. The PC unpacks the firmware package guacamoleb_14_P.32_210127.zip.
![20](2023041820.png)
2. Boot the software MsmDownloadTool V4.0.exe
![21](2023041821.png)
3. Tap start and wait for your phone to enter edl mode
![22](2023041822.png)
4. The phone enters edl mode, and MsmDownloadTool automatically enters the android 10 system
+ Mode 1: Turn the phone on, connect the cable to the PC, and press and hold the volume up and down keys at the same time
+ Mode 2: If your phone is still on, you can also connect to adb

```powershell
adb reboot edl
```

![23](2023041823.png)

5. Wait for the system to flush and enter the Simple Set-up
![24](2023041824.jpg)

### Unlock your phone bootloader

1. Turn on Phone Developer Mode and turn on adb debugging
![2](202304182.jpg)
2. Open oem unlock
![1](202304181.jpg)
3. Enter fastboot mode using the command
```powershell
adb reboot bootloader
```
4. Unlock with command
```powershell
fastboot oem unlock
```
![24](2023041824.png)

5. Select "UNLOCK THE BOOTLOADER", wait for reboot, enter, reset usb debug and developer mode
  
![18](2023041818.jpg)

### Flush twrp

1. Restart your phone to fastboot mode
```powershell
adb reboot bootloader
```
2. Flush twrp
```powershell
fastboot boot twrp.img
```
![25](2023041825.png)

3. Once installed, go straight to revovery mode, twrp.
4. Operational Interface Select Advanced->Flash Current TWRP

![26](2023041826.jpg)

4. Operational Interface Select Wipe->Format Data->Enter yes

![27](2023041827.jpg)

5. Do not restart at this time

### Flush Magisk

1. Advanced->ADB Sideload->Swipe to Start Sideload
![28](2023041828.jpg)
![29](2023041829.jpg)
![30](2023041830.jpg)
2. Enter the command to flush the Magisk.zip
![32](2023041832.png)
![31](2023041831.jpg)

### Flush Disable_Dm-Verity_ForceEncrypt_11.02.2020.zip

1. Advanced->ADB Sideload->Swipe to Start Sideload
2. Press the Volume + key when the Select Option appears by swiping the Disable_Dm-Verity_ForceEncrypt_11.02.2020.zip
![33](2023041833.png)
![34](2023041834.png)
3. Restart and enter the settings, one of which is selected as None
![35](2023041835.jpg)

### Flush nethunter

1. Reboot to fastboot mode, then volume key - enter recovery mode, re-enter twrp
![36](2023041836.jpg)

2. Advanced->ADB Sideload->Swipe to Start Sideload
3. Enter the command to flush the "kernel-nethunter-2021.3-oneplus7-oos-ten.zip"

```powershell
adb sideload kernel-nethunter-2021.3-oneplus7-oos-ten.zip
```
4. Advanced->ADB Sideload->Swipe to Start Sideload
5. Enter the command, enter "nethunter-2022.1-oneplus7-oos-ten-kalifs-full.zip"
```powershell
adb sideload nethunter-2022.1-oneplus7-oos-ten-kalifs-full.zip
```
![38](2023041838.png)
![39](2023041839.jpg)
![40](2023041840.jpg)
![41](2023041841.jpg)

### Install a magisk
1. Enter the command to install the magisk.app
```powershell
adb install magisk.app
```
![44](2023041844.png)
![45](2023041845.jpg)

### Initialize Nethunter
1. wifi, Modified
2. Open the red circle in the figure to get nethunter
![46](2023041846.jpg)
3. Tap kali Chroot Manager
![47](2023041847.jpg)
4. Tap START KALI CHROOT
![48](2023041848.jpg)
5. Success
![49](2023041849.jpg)
6. Verify, open a command line window, and enter a command

```bash
apt update
```
![50](2023041850.jpg)
   