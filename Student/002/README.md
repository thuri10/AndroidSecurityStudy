
<!— @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} —>

<!— code_chunk_output —>

- [iPhone Profile Acquisition/Software Installation/Projection in 1.Windows](#iphone Profile Acquisition Software Installation Projection in 1windows)
- [(1) Profile Acquisition](#1 Profile Acquisition)
- [(2) Software Installation](#2 Software Installation)
- [(3) Phone Drop Screen] (#3 Phone Drop Screen)
- [2. Escape Device Analysis Recommendation/unC0ver Process Details] (#2 Escape Device Analysis Recommendation unc0ver Process Details)
- [(1) Analysis and recommendation of escape devices](#1 Analysis and recommendation of escape devices)
- [(2) checkra1n Escape Detail Procedure] (#2checkra1n Escape Detail Procedure)
- [(1) Make an escape u disk] (#1 Make an escape u disk)
- [(2) Escape by painting your phone] (#2 Escape by painting your phone)
- [3. Post-Escape Phone Utilities: Log/Network/Package Management/No Signature] (#3 Post-Escape Phone UtilitiesLog Network Package Management No Signature)
- [(1) cydia Native Source Tool Installation] (#1cydia Native Source Tool Installation)
- [(2) Tripartite Source Tool Installation] (#2 Tripartite Source Tool Installation)
- [4.Frida/Objection/Any Version Install Switch/Dynamic Analysis] (#4fridaobjection Any Version Install Switch Dynamic Analysis)
- [(1) Install the latest frida] (#1 Install the latest frida)
- [(2) Install objection] (#2 Install objection)
- [5.ios Shell Smashing/IPA Static Analysis Basic Flow/IDAF5 Function Positioning] (#5ios Shell Smashing ipa Static Analysis Basic Flow idaf5 Function Positioning)
- [(1)ios crush] (#1ios crush)
- [(2) IPA Static Analysis Basic Flow and IDAF5 Function Positioning] (#2ipa Static Analysis Basic Flow and idaf5 Function Positioning)
- [iphone Phone Info Acquisition in 6.linux: ID/Name/Details/Signature/Screenshot/Location] (#iphone Phone Info Acquisition in 6linux id Name Details Signature Screenshot Location)
- [(1) get device id] (#1 get device id)
- [(2) get device name] (#2 get device name)
- [(3) Get Screenshot] (#3 Get Screenshot)
- [(4) Set Virtual Location] (#4 Set Virtual Location)
- [(5) Get Device Details] (#5 Get Device Details)
- [7. Install APP/IPA: Install/Uninstall/Upgrade/Back-up/Restore APP] (#7 Install appipa Install Uninstall Upgrade Back-up Restore APP)
- [(1) Install Software] (#1 Install Software)
- [(2) Uninstall Software] (#2 Uninstall Software)
- [(3) View installed apps] (#3 View installed apps)
- [(4) Other] (#4 Other)
- [8. Send and receive transfer files: usbmuxd OpenSSH=adb pull/push] (#8 Send and receive transfer files usbmuxd-opensshadb-pullpush)
- [(1) adb pull/push over usbmuxd] (#1 adb-pullpush over usbmuxd)
- [(2) adb pull/push over OpenSSH] (#2 adb-pullpush over openssh)
- [9. Remote File Management: Disk Mapping/Browse Phone Folder on PC] (#9 Remote File Management Disk Mapping Browse Phone Folder on PC)
- [(1) Mount File System] (#1 Mount File System)
- [(2) Unmount] (#2 Unmount)
- [10. Elevations and Activations: Recovery Mode/Firmware Elevations/Phone Activation] (#10 Elevations and Activations Recovery Mode Firmware Elevations Phone Activation)

<!— /code_chunk_output —>

# Intro

When many people learn reverse iOS app, they have two pain points:

1. iOS device too expensive
2. Requires macOS environment

The former requires a few thousand iPhone, while the latter requires at least a few thousand Macbook. This series is designed to address these two pain points, all done on a hundred dollar iPhone6 and demonstrated on a r0env/Win, giving us a healthy, cost-free set of environments that can drive us far and wide!

This article is "Challenge Not Using macOS to Reverse iOS APP" series of the first course environment to build in order to achieve some of the iOS APP reverse process in the environment of the general needs, specific objectives are as follows:
- iPhone Essentials Acquisition/Software Installation/Screen Shot in windows
- Escape Device Analysis Recommendations / Tool Recommendations / unC0ver Process Details
- Post-escape mobile phone tools: Blog/Network/Package Management/Autograph-free
- Frida/Objection/Any Version Installation Switch/Dynamic Analysis
- ios shrapnel/ipa static analysis basic flow/IDAF5 function positioning
- iphone Phone Info Acquisition in linux: ID/Name/Details/Signature/Screenshot/Location
- Install APP/IPA: Install/Uninstall/Upgrade/Back-up/Restore APP
- Send and receive transfer files: usbmuxd OpenSSH=adb pull/push
- Remote File Management: Disk Mapping/Browse Phone Folders on PC
- Elevations and Activations: Recovery Mode/Firmware Elevations/Mobile Activation



# iPhone Basics Get/Software Install/Screen in 1.Windows
## (1)Access to basic information
The official recommendation for operating iPhone in Windows is to use iTunes, but we will install the unsigned app in the future, so here we recommend using the Love Assistant. Go to the website to download and install AIZ Assistant, turn on and trust this PC on your phone
![1](202304041.png)

![2](202304042.png)

## (2)Software Installation

Software Installation: Click on the AIX Assistant app game to install directly, Aix installed software on the APP Store or not on the shelf but signed by the corporate account, specific content related to the IPA signature, will be explained in the follow-up article.
![3](202304043.png)

## (3)Cellular Projection
You can use a wired screen shot directly from the IZ Assistant, or you can use a wireless screen shot from the same LAN
![4](202304044.png)
The phone slides up, clicks on the screen image, clicks on the AIX screen.

# 2. Escape Equipment Analysis Recommendations / unC0ver Process Details

## (1)Analysis and recommendation of escape equipment

Check out how Assistant Ace escaped

Can Support ios The highest version is the unc0ver method which can support iOS14.8, and due to the failure to restart the device after the iPhone escape, the factory settings will need to be restored automatically to the latest iOS version supported by the current device, while the highest version of iPhone6 is 12.5.4, and the latest versions of iPhone6s and iPhone7 are both iOS 15, so we chose to use iPhone6 as the escape device so that the latest version will remain under the tool support even if the escape fails.
In this paper, the comparison of the escape modes is made. The analysis of the escape modes of unc0ver and Checkra1n is presented
checkra1n: It's more complicated, and you have to make a u-disk, but it's more stable
unc0ver: It's a simple process, but it's a lot of luck, and it's a lot of trying to be successful

## (2) checkra1n Escape Details

### (1) Make an escape u disk

![5](202304045.png)
![6](202304046.jpg)
![7](202304047.jpg)

### (2) Escape by swiping his phone

Enter the computer BIOS Select VendorCo ProductCode to boot from the u disk
![8](202304048.jpg)
Select ALT+F2 to Enter the Checkra1n Brush System
![9](202304049.jpg)
Up, down, left, right spacebar controls start to begin escape
![10](2023040410.jpg)
next
![11](2023040411.jpg)



![12](2023040412.jpg)
Follow the prompts when you enter the brush interface
![13](2023040413.jpg)
1. Tap start
2. Press the side key and home key at the same time
3. Press home
This interface is a brush in, and ALL Done is a brush in.
![14](2023040414.jpg)
Add: It doesn't matter how many times it's always successful, but be careful not to buy a locked phone. Second-hand iphone sellers sometimes hide ID locks, which can log in to their ID but send it if the escape fails.

# 3. Post-escape mobile phone common tools: blog/network/package management/no signature

When the phone is released from prison, the checkra1n icon is displayed on the desktop and cydia is clicked to install. cydia is a three-way software warehouse that needs to be used after the escape. Here we are mainly installing the following tools:
##(1) cydia Native Source Tool Installation
cydia native tools can search for installations directly
![15](2023040415.png)

- oslog log viewing tool, installed by default in /usr/bin
- OPENSSH  Remote connection software, which allows you to connect to your phone remotely, with a default account password of root/alpine
- Filza File Manager 		It is the mobile phone file management software, the role is to let us more convenient operation of the mobile phone file.
- A plug-in tool on the Apple File Conduit "2" IOS that helps us manipulate files on our phones on the computer side


## (2) Three-party source tool installation

- AppSync Unifield

The AppSync Unifield is a plug-in tool on IOS that helps us install an IPA that is not signed by Apple. After installation, the software that is not signed by Apple can be installed. The specific process is as follows:
Add source first: Edit->Add->Enter cydia.angelxwind.net
![16-2](20230404162.png)

Then install the plug-in: karen->Plug-ins->AppSync->Install->Confirm->Reboot

![17](2023040417.png)

- frida-server


Uninstall Historical Version
```bash
dpkg -l | grep frida matches an already installed frida
dpkg -P re.frida.server uninstall software
```
Download the latest frida-server to install
![18(2)](2023040418.png)

```bash
dpkg -i frida_16.0.10_iphoneos-arm.deb
```
# 4.Frida/Objection/Any Version Installation Switch/Dynamic Analysis

Here using r0env, with native pyenv supporting multi-version Frida/objection switching, here installing the latest version as a demo

## (1) Install the latest frida

```
pyenv install 3.9.5
pyenv global 3.9.5
pip install frida==16.0.5
```
It will be slow to install here without hanging up the agent. An error was encountered after hanging up the agent
![19](2023040419.jpg)
The error reason is that there are no socks related libraries

```
unset ALL_PROXY
pip install pysocks
```

Remount Agent Installation

```
export ALL_PROXY="socks:// Proxy IP:port"
pip install frida
pip install frida-tools
```
Installation Successful

![20(2)](2023040420.png)

## (2) Install objection
```bash
pip install objection
objection -g gaokao back explore
```
![21(2)](2023040421.png)

# 5.ios Shell Smashing / IPA Static Analysis Basic Process / IDAF5 Function Positioning
## (1)ios smashing shell
ios shell smashing tools are also available, here we recommend frida-ios-dump, below is the detailed installation and use process:

```bash
git clone https://github.com/AloneMonkey/frida-ios-dump
cd frida-ios-dump/
apt-get install usbmuxd
pip install -r requirements.txt —upgrade
iproxy 2222 22
./dump.py Gaokao Bei
```

## (2) IPA static