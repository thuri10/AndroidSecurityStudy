# EasySoCrackMe

a standard algorithm with only flower commands.

### Key Points📌

1. Analysis Based on the existing knowledge points to make bold guesses, careful verification
2. Here 'v5' 'v6' can be used as a secondary analysis by Findcrypt if there is no hint of an aes-dependent algorithm.
    
![Untitled](pic/Untitled%201.png)

End effect:

![Untitled] (pic/Untitled.png)

Annex: https://github.com/r0ysue/MobileCTF/tree/main/AndroidAlgorithm/easyso


—

# Problem solving process:

- Get the app first to see if it's rugged (because CrackMe doesn't have to verify it works here)
    
![Untitled](pic/Untitled%202.png)
    

## 1. install

- Direct installation fails here, the error prompt here **INSTALL_FAILED_TEST_ONLY** indicates that it is a test application, so the installation here requires some parameters to be specified to install
    
```bash
~\Desktop> adb install app-debug.apk
Performing Streamed Install
adb: failed to install app-debug.apk: Failure [INSTALL_FAILED_TEST_ONLY: installPackageLI]

~\Desktop> adb install -g -t -r -d app-debug.apk
Performing Streamed Install
Success
```
    

## 2. Analytical thoughts

### 2.1 Java Analysis

- Enter anything you want to Verify after the app is installed, and the tips are as follows
    
![Untitled](pic/Untitled%203.png)
    
- Next up is Jadx**Jadx app-debug.apk**, because it's CrackMe, so there's less class here, straight to MainActivity
    
![Untitled](pic/Untitled%204.png)
    
- So now is the process of verifying that the method01 was executed, and determining if we found the correct location
- 📍 Tip: The right-click method can directly copy the frida fragment of the hook method, and Jadx requires a version to support it (I remember 1.4.3+
    
![Untitled](pic/Untitled%205.png)
    

### 2.2 Java hook

- Use the 'Java.perform' method to create a new instrumentation session, then start frida-serve, trigger Verify, and perform process validation
    
```js
Java.perform(()=> {
let MainActivity = Java.use("com.roysue.easyso1.MainActivity");
MainActivity["method01"].implementation = function(str){
console.log('method01 is called' + ', ' + 'str: ' + str);
let ret = this.method01(str);
console.log('method01 ret value is ' + ret);
return ret;
};
})
```
    
```bash
# hook Information
method01 is called, str: aa
method01 ret value is ac33f2780262122a22a1f1c30aaeeae2
```
    
- With the hook information output, we can confirm that the code process we found was executed, and then the next step is to analyze the So layer code
    
```js
public static native String method01(String str);
```
    

### 2.3 So Analysis

- There is only **armeabi-v7a**, or 32-bit program, and the 32-bit program caveat is that the address needs +1** for **hook
    
![Untitled](pic/Untitled%206.png)
    
- Drag **libroysue.so** into the 32-bit IDA for analysis and leave any pop-up prompts out
- Search the export window for the keyword method01, and if not, the function is not a static registration (what is a dynamic and static registration look at Meat Master's NDK basis!)
    
![Untitled](pic/Untitled%207.png)
    

### 2.4 So Function Registration

- Statically register functions
- Certain naming rules must be followed, typically **Java_ package_class_method_name**
- The system loads the corresponding so through dlopen, gets the function address of the given name through dlsym, and then calls
- Statically registered JNI functions, must be in the export table
    
```c
// Java layer loading so library
// The native keyword represents a JNI method
public native void callBackShowToast();

// End-of-head, load support library file name libhello.so
static{
`System.loadLibrary("hello");
}

// C.C++ code
extern "C" JNIEXPORT jstring JNICALL
Java_com_kk_jnidemo_MainActivity_stringFromJNI(JNIEnv *env, jobject /* this */){}

extern "C" // Compile as C Otherwise, compile as C++ Auto-Notation Decoration (function name change)
// Implementation C++ code calls C language code, the code will be compiled in accordance with the C language link.

JNIEXPORT		// Export functions JNI Export IDA If viewed in, it appears in the export table
jstring			// return value type
JNICALL     // not used for a while
Java_com_kk_jnidemo_MainActivity_stringFromJNI	// fixed naming rules Java_package_class_method_name

// Parameters
JNIEnv *env // jni environment
jobject      // Refers to the Java object that calls the function
jclass       // static functions public static native
"
    
- dynamically registering functions JNI_OnLoad
- The same Java function can register more than one Native function, whichever is last registered
    
"cpp
// Java Layer
Log.d("MainActivity", "onCreate: " + stringFromJNI2(1, new byte[]{2}, "3"));
public native String stringFromJNI2(int a, byte[] b, String c);

—

// Dynamic Registration: so
jstring encodeFromC(JNIEnv *env, jobject obj, jint a, jbyteArray b, jstring c){
return env->NewStringUTF("encodeFromC");
}

// JNI_OnLoad
JNIEXPORT jint JNI_OnLoad(JavaVM *vm, void *reserved){
JNIEnv *env = nullptr;
// Get java env from java virtual machine
if(vm->GetEnv((void **)&env, JNI_VERSION_1_6)!= JNI_OK){
    LOGD-java("GetEnv failed");
    return -1;
}

// Registration Information
JNINativeMethod methods[] = {
    // public native String stringFromJNI2(int a, byte[] b, String c);
    {"stringFromJNI2", "(I[BLjava/lang/String;)Ljava/lang/String;",(void *)encodeFromC},
    {"stringFromJNI2", "(I[BLjava/lang/String;)Ljava/lang/String;",(void *)encodeFromC1},
    // Multiple native functions can be registered for the same Java function, whichever is last
};
// RegisterNatives The second argument is a struct body parameter:Java method name method signature function pointer
jclass MainActivityClazz = env->FindClass("com/kk/myapplication/MainActivity");
env->RegisterNatives(MainActivityClazz, methods, sizeof(methods) / sizeof(JNINativeMethod)); // Register with this function
return JNI_VERSION_1_6; // can only return JNI versions cannot return other jint values
}

// Structure
//    typedef struct {
//        const char* name;
//        const char* signature;
//        void*       fnPtr;
//    } JNINativeMethod;
```
    

### 2.5 Continue So Analysis

- Enter this decrypted pseudo-code by F5, where the parameter type of 'a1' is modified according to the So function registration rules, and 'a3' is our input
    
    
![Untitled](pic/Untitled%208.png)
    
![Untitled](pic/Untitled%209.png)
    
- Shortcut 'Y' modifies parameter type, shortcut 'N' modifies naming
    
![1.gif] (pic/1.gif)
    
- Is it much clearer next? Analysis 'a3' based on input
    
```c
int __fastcall Java_com_roysue_easyso1_MainActivity_method01(JNIEnv *env, int a2, int a3)
{
int v4; // [sp+8h] [bp-20h]
void *ptr; // [sp+Ch] [bp-1Ch]
const char *v6; // [sp+10h] [bp-18h]
// 0. a3 quoted here
```