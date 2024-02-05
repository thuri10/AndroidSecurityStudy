## FRIDA Script Series (2) Growth Chapter: Dynamic-Static Combination Reverse WhatsApp

In the [FRIDA Script Series(i) Getting Started: dump Bluetooth Interfaces and Instances on Android 8.1](https://github.com/r0ysue/AndroidSecurityStudy), we learned to enumerate all classes, subclasses, and methods in the module, find all its overloads, and finally, do a little battle over the Bluetooth interface. In this paper, we reverse, starting from 'hook' all overloads, write a tool that can dynamically observe all the modules, classes, methods and other interface data out.

### All overloads of the 0x01.hook method

In the article [An article takes you through the essence of Frida (based on Android 8.1)](https://github.com/r0ysue/AndroidSecurityStudy), we've learned how to handle the reload of the playback, and let's review the code:

```js
my_class.fun.overload("int" , "int").implementation = function(x,y){
my_class.fun.overload("java.lang.String").implementation = function(x){
```

That means we need to construct an array of overloads and print out every overload. Let's go straight to the code:


```js

// Target class
var hook = Java.use(targetClass);
// Number of overloads
var overloadCount = hook[targetMethod].overloads.length;
// Print Log: How many overloads are tracked
console.log("Tracing " + targetClassMethod + " [" + overloadCount + " overload(s)]");
// Enter once for each overload
for(var i = 0; i < overloadCount; i++){
//hook Each overload
hook[targetMethod].overloads[i].implementation = function(){
console.warn("\n*** entered " + targetClassMethod);

// Can print each overloaded call stack, which is very helpful for debugging, and of course, there is a lot of information, try not to print unless the analysis is deadlocked
Java.perform(function(){
var bt = Java.use("android.util.Log").getStackTraceString(Java.use("java.lang.Exception").$new());
console.log("\nBacktrace:\n" + bt);
});

// Print parameters
if(arguments.length)console.log();
for(var j = 0; j < arguments.length; j++){
console.log("arg[" + j + "]: " + arguments[j]);
}

// print return value
var retval = this[targetMethod].apply(this, arguments); // rare crash(Frida bug?)
console.log("\nretval: " + retval);
console.warn("\n*** exiting " + targetClassMethod);
return retval;
}
}
```

So we handle all the overloads of the methods, and then we enumerate all the methods.

### All methods of the 0x02.hook class

Direct code:

```js
function traceClass(targetClass)
{
    //Java.use is a new subject, remember?
    var hook = Java.use(targetClass);
    // Use reflection to get all the methods of the current class
    var methods = hook.class.getDeclaredMethods();
    // Remember to release objects after they are built
    hook.$dispose;
    // Save method name to array
    var parsedMethods = [];
    methods.forEach(function(method){
        parsedMethods.push(method.toString().replace(targetClass + ".", "TOKEN").match(/\sTOKEN(.*)\(/)[1]);
    });
    // Remove some duplicate values
    var targets = uniqBy(parsedMethods, JSON.stringify);
        // hook all methods in the logarithm group, traceMethod is the first section
        targets.forEach(function(targetMethod){
        traceMethod(targetClass + "." + targetMethod);
    });
}
```

### All subclasses of the 0x03.hook class

Or code from the top core:

```js
// Enumerate all loaded classes
Java.enumerateLoadedClasses({
    onMatch: function(aClass){
    // Iteration and Judgment
    if(aClass.match(pattern)){
        // Make more judgments and adapt to more pattern
        var className = aClass.match(/[L]? (.*);?/)[1].replace(/\//g, ".");
        // Go inside the traceClass
        traceClass(className);
    }
    },
    onComplete: function(){}
});
```

### Export function for 0x04.hook local library

```js
// Track local library functions
function traceModule(impl, name)
{
    console.log("Tracing " + name);
    //frida's Interceptor
    Interceptor.attach(impl, {
    onEnter: function(args){

    console.warn("\n*** entered " + name);
    // Print call stack
    console.log("\nBacktrace:\n" + Thread.backtrace(this.context, Backtracer.ACCURATE)
    .map(DebugSymbol.fromAddress).join("\n"));
    },
    onLeave: function(retval){
    // print return value
    console.log("\nretval: " + retval);
    console.warn("\n*** exiting " + name);

    }
    });
}
```

### 0x05. Dynamic and static combined inverse WhatsApp

Finally, when it's time for real combat, splice the code together to form a script, which is actually awesome-frida.
https://github.com/dweinstein/awesome-frida The code is [here](https://github.com/0xdea/frida-scripts/blob/master/raptor_frida_android_trace.js), just a little bug. After [gourd-doll](https://github.com/hookmaster/frida-all-in-one) has been modified, it is finally ready to use.

Let's try several of its main functions, first of all, the local library export function.

```js

setTimeout(function(){
Java.perform(function(){
trace("exports:*!open*");
//trace("exports:*!write*");
//trace("exports:*!malloc*");
//trace("exports:*!free*");
});
}, 0);
```

We 'hook' is the 'open()' function and run to see the effect:

```bash
$ frida -U -f com.whatsapp -l raptor_frida_android_trace_fixed.js —no-pause
```

![](pic/01.png)

As shown in the figure, '*!open*' matches the derived functions 'openlog', 'open64', and so on according to the regularization, and hook all of them, printing out their parameters and their return values.

Next you want to see which part, just throw it in ['jadx'](https://github.com/skylot/jadx), "analyze" it statically, flip it yourself, or search it based on a string.

![](pic/03.png)

For example, if we want to look at the contents of the 'com.whatsapp.app.protocol' package in the figure above, we can set the 'trace("com.whatsapp.app.protocol")'.

![](pic/04.png)

![](pic/05.png)

You can see that the functions, methods, including overloads, parameters, and return values within the package are all printed. This is the charm of the 'frida' script.

Of course, scripts are ultimately just a tool, and your understanding of 'Java', Android apps, and your ideas are all that matters.

Next, you can go with [Xposed module](https://repo.xposed.info/module-overview) to see what modules have been made for 'whatsapp', what functions of 'hook', what functions have been implemented, and learn to write by yourself.

![](pic/06.png)

Of course, it's illegal to make a plug-in, and don't make and distribute any app's plug-ins, because you'll only be punished by law.
