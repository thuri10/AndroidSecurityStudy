[toc]

# Pixel1 Replacement: OnePlus 3 phones swipe into KaliNethunter to unpack full Linux command environment

# Meaning

As you know, on the Linux, we want to do statistics, positioning, visualization, analysis and dump of data packets, there is a series of tools and software. These tools for our network data packet analysis and processing provides unparalleled support, we can say that there are these tools, there is no packet can not be caught, if combined with hook technology, there is no protocol that can not be unlocked.

Although the tool is good, but they need a complete Linux environment to run, such as jnettop/htop/nethogs and so on need root authority, such as iwlist/aircrack/HID and so on need kernel driver support, monitoring mode open, Wireshark/Charles/BP will need the support of desktop environment, visual analysis in the GUI.

The details are explained in detail in this article: ["Confrontation from High Altitude: Replacing the Android Kernel and Unsealing Linux Commands and Environments"](https://mp.weixin.qq.com/s/PIiGZKW6oQnOAwlCqvcU0g)

Over time, the Nexus device gradually dropped out of the historical scene, while the Kali Nethunter was slow to support the Pixel device; now we have to settle for the next best option, to continue the tradition of swiping the phone with Kali Nethunter to unseal the entire Linux environment.

Based on the OnePlus 3(t) phone, this one is the performance equivalent of the Pixel generation. The operating environment is Android 10. The accessories used in this paper are located in Baidu Disk:Link:https://pan.baidu.com/s/1c01pB6JIY6a-25P-855dAAExtraction code:e2gn

This series is a good series of works for the students. The accessories, apk, code and so on, are in my project. You can choose from:

[https://github.com/r0ysue/AndroidSecurityStudy](20230403https://github.com/r0ysue/AndroidSecurityStudy)



# Intro

1. Why nethunter environments
+ You need to get ahead of your work when you're ready. With nethunter's powerful tools, work is easy
2. Why oneplus3T
+ oneplus3T This model, lineage, nethunter, twrp, is well supported
+ oneplus3T is now very cheap
+ oneplus3T performance is good, much better than the nexus 5x
3. Why write this brush article
Not only because of the power of the nethunter and oneplus3t price performance, but also because the brush always has such a problem, leading to failure. After several days of testing, a more relaxed approach was found. All toolkits used during this swipe are provided later

# Pre-Press Set-up
Developer mode needs to be turned on, bootloader unlock, usb debug
If the phone meets these conditions, it can go straight into the lineage

## Turn on developer mode
The system version number (clicked multiple times), the general way to access is inside the phone, set->about the phone->version number
![1](202304061.jpg)
![2](202304062.jpg)

## Turn on usb debug and oem unlock licenses
+ Settings->System->Developer Options
![3](202304063.jpg)

![4](202304064.jpg)
## Unlock OEM
1. Reboot to bootloader
+ Use the adb command

```bash
adb reboot bootloader
```
+ Turn on the phone, then On + Phone Volume Down
![5](202304065.png)
![6](202304066.jpg)

1. Unlock

```bash
fastboot oem unlock
```

![7](202304067.png)
![8](202304068.jpg)

Once the phone has this interface, choose UNLOCK THE BOOTLOADER
Automatically restart after waiting to unlock
Note: After unlocking the reboot, start developer mode and usb debugging again after you have completed basic set-up
# flush lineage
The system provided by nethunter is the mirror package of android10. We need to input a bottom package of android10. Here we choose the system of lineage
## Flush twrp
1. Restart your phone, swipe into twrp.img
```bash
adb reboot bootloader
fastboot flash recovery recovery.img
```
![9](202304069.png)
1. Phone volume up and down, select recovrey mode to enter the twrp system

![11](2023040611.jpg)

## Clean up data
1. In twrp, Wipe->Format Data->Enter 'yes'->After success, back to a place where you can select Advanced Wipe
![12](2023040612.jpg)
![10](2023040610.jpg)

1. Advanced Wipe->Check All

![15](2023040615.jpg)

## Inbound Wall Bag

1. Advanced->ADB Sideload
![17](2023040617.jpg)
2. Enter commands

```bash
adb sideload oxygenos-9.0.6-bl-km-5.0.8-firmware-3.zip
```
![40](2023040640.png)
![41](2023040641.jpg)

## Package flushed into lineage
1. Advanced->ADB Sideload
2. Enter the following command

```bash
adb sideload lineage-17.1-20210215-nightly-oneplus3-signed.zip
```
![42](2023040642.png)
![43](2023040643.jpg)

## Flush magisk
1. Advanced->ADB Sideload
2. Enter the following command
```bash
adb sideload app-release.zip
```

![44](2023040644.png)
![45](2023040645.jpg)

3. Restart
4. Turn on developer mode, usb debugging
5. Use the following command to install the magisk app
"
```bash
adb install app-release.apk
```

![46](2023040646.png)
![47](2023040647.jpg)
6. Reboot, After installing magisk, reboot is required for configuration to take effect for the first time
![48](2023040648.jpg)
7. magisk has been successfully installed as above

# Flush nethunter using Magisk

1. Package Incoming to nethunter

```bash
adb push kalihunter.zip /sdcard/Download
```
![28](2023040628.png)
2. Select Module -> Local, Select, Package for nethunter, Wait to Finish, Restart
![29](2023040629.jpg)

# nethunter initialization settings
1. After reboot, connect to wifi, enter command, set time

```bash
settings put global captive_portal_http_url https://www.google.cn/generate_204
settings put global captive_portal_https_url https://www.google.cn/generate_204
settings put global ntp_server 1.hk.pool.ntp.org
reboot
```
![30](2023040630.png)

2. Initialize, click Kali Chroot Manager to start initializing

![31](2023040631.jpg)
![32](2023040632.jpg)
3. The command window is available, successful
![33](2023040633.jpg)

# Flush successful!

It's a reverse journey that can be started! Look forward to updating the OnePlus Family's Kali Nethunter swipe-through tutorial as well! vx:r0ysue. Selling equipment and tutorials. Help you get promoted and get a raise!