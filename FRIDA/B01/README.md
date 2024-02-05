
<!— @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} —>
<!— code_chunk_output —>

* [FRIDA Scripting Series (I) Primer: dump Bluetooth Interfaces and Instances on Android '8.1'] (Primer on #frida Scripting Series: dump Bluetooth Interfaces and Instances on Android 81)
* [What is 0x01.FRIDA? Why is it so popular?](#0x01frida What is it and why is it so popular)
* [concept of 0x02.FRIDA script] ( concept of #0x02frida script)
* [0x03. simple script one: enumerate all classes] (#0x03 simple script one enumerates all classes)
* [0x04. Simple Script II: Locate Target Class and Print Instances of Class] (#0x04 Simple Script II Locate Target Class and Print Instances of Class)
* [0x05. Simple Script III: Enumerate all methods and locate methods] (#0x05 Simple Script III enumerates all methods and locates methods)
* [0x06. Composite case: dump Bluetooth interface and instance on Android '8.1':] (#0x06 composite case dump Bluetooth interface and instance on Android 81)

<!— /code_chunk_output —>


## FRIDA Script Series (I) Getting Started: dump Bluetooth Interfaces and Instances on Android '8.1'

### What is 0x01.FRIDA? Why is it so hot?

'frida' is currently very popular and the framework is everything from 'Java' layer hook to 'Native' layer hook. Although persistence relies on development frameworks such as 'Xposed' and 'hookzz', the dynamic and flexible nature of 'frida' can be very helpful in reverse and in automating reverse.

What is 'frida'? The github directory [Awesome Frida](https://github.com/dweinstein/awesome-frida) describes 'frida' in this way:

>Frida is Greasemonkey for native apps, or, put in more technical terms, it’s a dynamic code instrumentation toolkit. It lets you inject snippets of JavaScript into native apps that run on Windows, Mac, Linux, iOS and Android. Frida is an open source software.

'frida' is the 'Greasemonkey' of the native 'app' of the platform, and to be professional, it is a dynamic instrumentation tool that can insert some code into the memory space of the native 'app' (to dynamically monitor and modify its behavior), which native platforms can be 'Win', 'Mac', 'Linux', 'Android' or 'iOS'. And 'frida' is open source.

'Greasemonkey' may not be understood as a set of plug-ins for 'firefox', and scripts written using it can directly alter the way 'firefox' organizes webpages to achieve any desired functionality. And the plug-in is external, very flexible.

'frida' makes the same sense. So why is it so popular?

Static and dynamic modification of memory cheating has always been a necessity, such as the Golden Mountain Ranger, essentially 'frida' does what it does. In principle, 'frida' can be used to "outboard" Jin Shan Rangers, including 'CheatEngine'.

Of course, it's not the age when you can simply modify your memory to rest easy. Don't do that. It's illegal to do a plugging.

The same is true in reverse work, where 'frida' is used to "see" what is not normally seen. Due to the characteristics of compiled language, machine code in the CPU and memory in the process of execution, its internal data interaction and jump, is invisible to the user. Of course, if you have source code on hand, even if you have a debuggeable executable package, you can use a debugger such as 'gbd', 'lldb', and so on.

And if not? What if it's a black box? To reverse and dynamically debug the app, and even automate analysis and gather information on a large scale, we need fine-grained process control and code-level customizable systems, as well as a framework for constant dynamic correction of debugging and programmable debugging, which is 'frida'.

'frida' uses 'python', 'JavaScript' and other 'glue languages' as a reason for its popularity, quickly automating reverse processes and integrating them into existing architectures and systems to lay the groundwork for the release of products such as 'threat intelligence', 'data platforms' and even 'AI risk control'.

![](pic/01.png)

Officials have even made their ability to 'agile development' and 'quickly adapt to existing architectures' their core selling points.

### Concepts for 0x02.FRIDA scripts

'FRIDA script' is a piece of code that uses the 'FRIDA' dynamic instrumentation framework, 'FRIDA' exported 'APIs' and methods, to monitor, modify, or replace object methods in memory space. The 'API' of 'FRIDA' is implemented using 'JavaScript', so we can take full advantage of the advantages of the 'JS' anonymity function, as well as the numerous 'hook' and callback function APIs.

Let's take the most intuitive example: 'hello-world.js'

```js

setTimeout(function(){
Java.perform(function(){
console.log("hello world!");
});
});
```

This is essentially a 'FRIDA' version of "Hello World!", we pass an anonymous function as an argument to the 'setTimeout()' function, whereas the 'Java.perform()' function in the body of functions itself accepts an anonymous function as an argument, which eventually calls the 'console.log()' function to print a "Hello world!"String. We need to call the 'setTimeout()' method because it registers our function with the 'JavaScript' runtime, and then we need to call the 'Java.perform()' method to register the function with the 'Java' runtime of 'Frida', to perform the operations in the function, and of course there's just a 'log'.

We then run 'frida-server' on our phones and operate it on our computers:

`$ frida -U -l hello-world.js android.process.media`

![](pic/001.png)

You can then see that 'console.log()' was executed successfully and the string was printed.

### 0x03. Simple script one: enumerate all classes

We are now going to add a little bit of functionality to this 'HelloWorld.js', such as enumerating all the classes that have been loaded, which takes into account the 'enumerateLoadedClasses' method of the 'Java' object. The code is as follows:

```js
setTimeout(function(){
    Java.perform(function(){
        console.log("\n[*] enumerating classes...");
        Java.enumerateLoadedClasses({
        onMatch: function(_className){
        console.log("[*] found instance of '"+_className+"'");
    },
    onComplete: function(){
        console.log("[*] class enuemration complete");
    }
    });
    });
});
```

First, make sure that 'frida-server' is running on your phone, and then on your PC:

```bash
$ frida -U -l enumerate_classes.js android.process.media
```

![](pic/002.png)

![](pic/003.png)

### 0x04. Simple Script II: Position the target class and print an instance of the class

Now we have found all the loaded classes in the target process, for example, now our goal is to see its Bluetooth-related classes, we can modify the code to this:

```js
Java.enumerateLoadedClasses({
    onMatch: function(instance){
    if(instance.split(".")[1] == "bluetooth"){
        console.log("[->]\t"+instance);
    }
    },
    onComplete: function(){
        console.log("[*] class enuemration complete");
    }
    });
```

Here's what it does:

![](pic/005.png)

So many Bluetooth-related classes can be found. It is also possible, of course, to use the method contained in the string, using the 'indexOf()', 'search()', or 'match()' method of the 'JavaScript' string, which is left to the reader.

Once you have located the class we want to study, you can print an instance of the class and look at the ['FRIDA's API Manual'](https://www.frida.re/docs/javascript-api/#java) to see that you should use the 'Java.choose()' function to select an instance.

![](pic/004.png)

We add the following lines to the code for the instance of the 'android.bluetooth.BluetoothDevice' class selected.

```js
Java.choose("android.bluetooth.BluetoothDevice",{
onMatch: function(instance){
console.log("[*] "+" android.bluetooth.BluetoothDevice instance found"+" :=> '"+instance+"'");
bluetoothDeviceInfo(instance);
},
onComplete: function(){ console.log("[*] —");}
});
```

After your phone turns on Bluetooth and connects to my Walker Bluetooth headset to start playing content:

![](pic/006.jpeg)

To run a script on your PC:

```js
$ frida -U -l enumerate_classes_bluetooth_choose.js com.android.bluetooth
```

You can see that my Bluetooth device is correctly detected:

![](pic/007.png)

### 0x05. Simple script III: Enumerate all methods and locate them

We have enumerated the classes and instances above, and then we enumerate all the methods, mainly using the 'Java.use()' function.

![](pic/008.png)

'Java.use()' differs from 'Java.choose()' in that the former creates a new object and the latter selects an instance that already exists in memory.

Add code as follows:

```js

function enumMethods(targetClass)
{
    var hook = Java.use(targetClass);
    var ownMethods = hook.class.getDeclaredMethods();
    hook.$dispose;

    return ownMethods;
}

...
...

var a = enumMethods("android.bluetooth.BluetoothDevice")
a.forEach(function(s){
console.log(s);
});
```

To operate on a PC while maintaining the previous section of the environment:

```js
$ frida -U -l enumerate_classes_bluetooth_choose_allmethod.js
```