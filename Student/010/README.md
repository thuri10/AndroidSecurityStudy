# Frida and VPN Packet Capture Configuration After Pixel4 Flushes Into KernelSU

Hands teach you to brush KernelSU on Pixel4 and configure your frida and VPN capture environments.

## KernelSu environment

- https://github.com/msnx/KernelSU-Pixel4XL environment
- Based on KernelSU from weishu:[https://github.com/tiann/KernelSU](https://github.com/tiann/KernelSU)
- Device Name: Pixel 4xl
- Android Version: 13
- Baseband version: g8150 - 00123 - 220708 b - 8810441
- Kernel Version: 4.14.276-g8ae7b4ca8564-ab8715030
- Build Numbers: TP1A.220624.014
- Environment variables need to be added here if you are using a r0env machine, no one can use 'adb' 'fastboot'
    
```yaml
PATH=$PATH:/root/Android/Sdk/platform-tools;export PATH;
source ~/.bashrc
```
    

### Official mirror printer

1. Restore factory settings by swiping in the official firmware package
    
```bash
adb reboot bootloader    # ** Phone to VM is required here, No Power + Volume key to enter **
7z x adb flame-tp1a.220624.014-factory-7b8f6f73.zip
cd flame-tp1a.220624.014
./flash-all.sh           # Just wait to flush
```
    
![Untitled](pic/Untitled.jpeg)
    
![Untitled](pic/Untitled%201.jpeg)
    
2. Brush here Enter bootloader mode again (press and hold the Power key + Volume key) Brush in the Android13 image
    
```bash
fastboot flash boot pixel4xl_android13_4.14.276_v057.img
```
    
3. Install KernelSu
    
# Go to Phone Settings -> About Phones -> Tap the release number seven times to enable developer mode
#System->Advanced->Developer Options->
# 1. Turn off automatic system updates
# The 2.USB debug switch is enabled if the pop-up trust flyout requires trust
    
# Use adb to install KernelSU
adb install KernelSU_v0.5.7_10866-release.apk
    
# Screen shot
scrcpy
"
    
![Untitled](pic/Untitled%202.jpeg)
    

## Catch Environment

### System Certificate Installation

1. Save the Charles certificate and import it to your phone to install 'adb push 123.pem /sdcard/'
    
![Untitled](pic/Untitled.png)
    
2. Mobile Phone Install Certificate: Settings → Security → More Security → Encryption and Credentials → Install Certificate → CA Certificate
    
![1.gif](pic/1.gif)
    
3. Appears in the user directory after installation completes
    
![Untitled](pic/Untitled%201.png)
    
4. Here you need to move the certificate to the root directory, import the certificate module to the phone, use KernelSU to install and restart the phone
    
```bash
adb push Move_Certificates-v1.9.zip
```
    
5. Charles's certificate can be seen at the system after the phone restarts **XK72 Ltd**
    
    
![Untitled](pic/Untitled%202.png)
    
![Untitled](pic/Untitled%203.png)
    

### Charles

### SSL Proxy

![Untitled](pic/Untitled%204.png)

### Port Settings

![Untitled](pic/Untitled%205.png)

### access control

![Untitled](pic/Untitled%206.png)

### Environment Validation

- Install Agent Tools and Cool Apps
    
```bash
adb install Postern_3.1.3_Apkpure.apk
adb install coolapk.apk
```
    
- Mobile phone to LAN WI-FI hint exclamation point, execute the following command to remove (not to remove, just hint, network works)
    
```bash
settings put global captive_portal_http_url https://www.google.cn/generate_204
settings put global captive_portal_https_url https://www.google.cn/generate_204
settings put global ntp_server 1.hk.pool.ntp.org
```
    
![Untitled](pic/Untitled%207.png)
    
- Agent Configuration: Actually based on PC IP, don't copy here
    
![Untitled](pic/Untitled%208.png)
    
- Enable VPN, turn on CoolAnn to log on, and if you see the following information to prove that the capture environment is OK
    
![Untitled](pic/Untitled%209.png)
    

## frida & Objection

- Executing this command will install the latest version of frida, firda-tools, obj directly
    
```bash
pip install objection -i https://mirrors.aliyun.com/pypi/simple
    
objection version # Output Version Number Detect if installation succeeded
frida —version
```
    
- Get the frida-server for the installed version from https://github.com/frida/frida/releases
    
```bash
# Check the device platform first, then download the corresponding server version on the website
adb shell getprop ro.product.cpu.abi    # View Device CPUs
adb push [computer-side frida-server path] /data/local/tmp/fs
adb shell
su
cd /data/local/tmp
chmod 777 fs				# File Permission Modification
./fs						    # Launch frida-server
./fs -l 0.0.0.0:8888		# The listening port starts like this
```
    

### Small Cases - No Screenshots

- App download [MobileCTF/AndroidNetwork/MZT at main · r0ysue/MobileCTF](https://github.com/r0ysue/MobileCTF/tree/main/AndroidNetwork/MZT)
    
```bash
Android API No Screenshots
activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_SECURE);
activity.getWindow().clearFlags(WindowManager.LayoutParams.FLAG_SECURE);
```
    
- There's no record of detailed testing, as homework and hand-training

## References and large attachments:

- [VPN Capture and frida Configuration_Bilibili_bilibili after Pixel4 Flushes into KernelSU](https://www.bilibili.com/video/BV1M8411Z7rC/?spm_id_from=333.999.0.0&vd_source=bfb9720533da6436a36b48fddb3d128a)
- Official Image: [Factory image of Nexus and Pixel devices | Google Play services | Google for Developers](https://developers.google.cn/android/images?hl=zh-cn#coral)
- ****Pixel4**** Official Mirroring, KernelSU, Postern, Move_Certificates
- [https://github.com/r0ysue/MobileCTF/tree/main/AndroidEnvironment/Pixel4KernelSU/attachment](https://github.com/r0ysue/MobileCTF/tree/main/AndroidEnvironment/Pixel4KernelSU/attachment)
