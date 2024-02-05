* [One article takes you through the essence of 'frida' (based on Android 8.1)] (#One article takes you through the essence of frida based on Android 81)
* ['What is 'frida'?] (What is #frida)
* ['frida' Why is it so hot?] (Why is #frida so hot)
* [frida Implementation] (#frida Implementation)
* [Base Competencies Ⅰ:hook Parameters, Modification Results] (#Base Competencies Ⅰ:hook Parameters - Modification Results)
* [Basic CompetenceⅡ: Parameter Construction, Method Overload, Handling of Hidden Functions] (# Basic Competence ii Parameter Construction - Method Overload - Handling of Hidden Functions)
* [Intermediate Competency: Remote Call] (#Intermediate Competency Remote Call)
* [Advanced Capabilities: Interoperability, Dynamic Modification] (#Advanced Capabilities Interoperability - Dynamic Modification)
* [Ready to do tutorials, catalog already taken] (#Ready to do tutorials - catalog already taken)

# An article takes you through the essence of 'frida' (based on Android 8.1)

Having been greatly helped by this article [What the Xposed Module Wrote](https://www.freebuf.com/articles/terminal/114910.html), I felt compelled to write an article to give back to the freebuf community. Now the most popular is 'frida', a framework that can do anything from 'Java' layer hook to 'Native' layer hook. While persistence relies on development frameworks such as 'Xposed' and 'hookzz', the dynamic and flexible nature of 'frida' can be very helpful in reverse and in automating reverse.

## What is 'frida'?

First, what is 'frida', and the github directory [Awesome Frida](https://github.com/dweinstein/awesome-frida) describes 'frida' in this way:

>Frida is Greasemonkey for native apps, or, put in more technical terms, it’s a dynamic code instrumentation toolkit. It lets you inject snippets of JavaScript into native apps that run on Windows, Mac, Linux, iOS and Android. Frida is an open source software.

'frida' is the 'Greasemonkey' of the native 'app' of the platform, and to be professional, it is a dynamic instrumentation tool that can insert some code into the memory space of the native 'app' (to dynamically monitor and modify its behavior), which native platforms can be 'Win', 'Mac', 'Linux', 'Android' or 'iOS'. And 'frida' is open source.

'Greasemonkey' may not be understood as a set of plug-ins for 'firefox', and scripts written using it can directly alter the way 'firefox' organizes webpages to achieve any desired functionality. And the plug-in is external, very flexible.

'frida' makes the same sense.

## 'frida' Why is it so popular?

Static and dynamic modification of memory cheating has always been a necessity, such as the Golden Mountain Ranger, essentially 'frida' does what it does. In principle, 'frida' can be used to "outboard" Jin Shan Rangers, including 'CheatEngine'.

Of course, it's not the age when you can simply modify your memory to rest easy. Don't do that. It's illegal to do a plugging.

The same is true in reverse work, where 'frida' is used to "see" what is not normally seen. Due to the characteristics of compiled language, machine code in the CPU and memory in the process of execution, its internal data interaction and jump, is invisible to the user. Of course, if you have source code on hand, even if you have a debuggeable executable package, you can use a debugger such as 'gdb', 'lldb', and so on.

And if not? What if it's a black box? To reverse and dynamically debug the app, and even automate analysis and gather information on a large scale, we need fine-grained process control and code-level customizable systems, as well as a framework for constant dynamic correction of debugging and programmable debugging, which is 'frida'.

'frida' uses 'python', 'JavaScript' and other 'glue languages' as a reason for its popularity, quickly automating reverse processes and integrating them into existing architectures and systems to lay the groundwork for the release of products such as 'threat intelligence', 'data platforms' and even 'AI risk control'.

![](pic/01.png)

Officials have even made their ability to 'agile development' and 'quickly adapt to existing architectures' their core selling points.

## frida Implementation Environment

Host:

>Host:Macbook Air CPU: i5 Memory:8G
System:Kali Linux 2018.4 (Native, non-virtual machine)

Client:

>client:Nexus 6 shamu CPU:Snapdragon 805 Mem:3G
System:lineage-15.1-20181123-NIGHTLY-shamu,android 8.1

The reason for using 'kali linux' is that the tools are comprehensive, the permissions are single and only one 'root' is useful as a prototype development, otherwise the various permissions, environments and dependencies of 'python' and 'node' are simply annoying. Using 'lineage' because of its convenient 'network ADB debugging' can eliminate the need for a 'usb' cable connection. (Although the real reason is that there is no money to buy new equipment, 'Nexus 6' [official](https://developers.google.com/android/images) only supports '7.1.1' and '8.1' is only 'lineage'.)Remember to brush in a 'lineage' ['su' package](https://download.lineageos.org/extras) to get the 'root' permission and 'frida' is required to run under the 'root' permission.

First, download a linux version of 'platform-tools' —'SDK Platform-Tools for Linux' from [the website](https://developer.android.com/studio/releases/platform-tools), download the extracted files and run the binaries directly inside, or, of course, add the path to the environment. Thus the 'adb' and 'fastboot' commands are available.

Then you can take 'frida-server' [download](https://github.com/frida/frida/releases) off and copy it to the Android machine, run up using the 'root' user and keep 'adb' connected.

```bash
$ ./adb root # might be required
$ ./adb push frida-server /data/local/tmp/
$ ./adb shell "chmod 755 /data/local/tmp/frida-server"
$ ./adb shell "/data/local/tmp/frida-server &"
```

Finally, install 'frida' in 'kali linux', and 'frida' in 'kali' is so simple, a one-sentence order, to make sure that nothing goes wrong. (You may need to install 'pip' first, or a one-sentence command: 'curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py')

```bash
pip install frida-tools
```

Then connect it with the 'frida-ps -U' command and you can see the running process.

```js
root@kali:~# frida-ps -U
Waiting for USB device to appear...
PID Name
— —
431 ATFWD-daemon
3148 adbd
391 adspd
2448 android.ext.services
358 android.hardware.cas@1.0-service
265 android.hardware.configstore@1.0-service
359 android.hardware.drm@1.0-service
360 android.hardware.dumpstate@1.0-service.shamu
361 android.hardware.gnss@1.0-service
266 android.hardware.graphics.allocator@2.0-service
357 android.hidl.allocator@1.0-service
...
...
```

## Basic Competencies Ⅰ:hook Parameters, Modification Results

Write your own 'app' first:

```java
package com.roysue.demo02;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;

public class MainActivity extends AppCompatActivity {

@Override
protected void onCreate(Bundle savedInstanceState){
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    while(true){

    try {
        Thread.sleep(1000);
        } catch(InterruptedException e){
        e.printStackTrace();
    }

    fun(50,30);
    }
    }

    void fun(int x , int y){
        Log.d("Sum" , String.valueOf(x+y));
    }

}
```

In principle, it is simple that 'fun()' is the function that outputs the result of the function 'fun(50,30)' at a time interval of one second at the console. The function of 'fun()' is summation. The final result is shown below in the console.

```bash
$ adb logcat |grep Sum
11-26 21:26:23.234 3245 3245 D Sum     : 80
11-26 21:26:24.234 3245 3245 D Sum     : 80
11-26 21:26:25.235 3245 3245 D Sum     : 80
11-26 21:26:26.235 3245 3245 D Sum     : 80
11-26 21:26:27.236 3245 3245 D Sum     : 80
11-26 21:26:28.237 3245 3245 D Sum     : 80
11-26 21:26:29.237 3245 3245 D Sum     : 80
```

Now let's write a piece of 'js' code and load it into 'com.roysue.demo02' using 'frida-server' to execute the 'hook' function in it.

```javascript
$ nano s1.js
```

```js
console.log("Script loaded successfully ");
Java.perform(function x(){
console.log("Inside java perform function");
// Location class
var my
```