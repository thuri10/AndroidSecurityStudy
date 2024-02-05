
<!— @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} —>

<!— code_chunk_output —>

- [Intro](#intro)
- ["Summary of root Detection Methods for Common Java Layer Anti-Debugging Techniques"](#Summary of root Detection Methods for Common java Layer Anti-Debugging Techniques)
- [Execute 'which su' command detect su](#Execute which-su command detect su)
- [Detect the presence of illegal binaries in popular directories](# Detect the presence of illegal binaries in popular directories)
- [Determine if SELinux is open,](#Determine if selinux is open)
- [Detect ro.debuggable and ro.secure values](# Detect rodebuggable and rosecure values)
- [Check specific path for write permissions](#Check specific path for write permissions)
- [Detect test-keys](# Detect test-keys)
- [Detect Illegal Apps] (# Detect Illegal Apps)

<!— /code_chunk_output —>

# Intro

This series is a good series of works for the students. The accessories, apk, code and so on, are in my project. You can choose from:

[https://github.com/r0ysue/AndroidSecurityStudy] (https://github.com/r0ysue/AndroidSecurityStudy)

# "Summary of Java Detection Methods for Common root Layer Anti-Debugging Techniques"

The Android system was designed to protect the security and stability of user equipment and control access to the system. Therefore, the Android system will limit the unauthorized operation of root. In the penetration test of Android applications, most technologies require root privileges to install a variety of tools, which endanger the security of the application. The following is a summary of the author on several Root detection methods, and some remarks


## Execute 'which su' command to detect su
The su command can be used to switch to other users, Android phones for security reasons, the system generally does not have the su executable file, so based on the way to check whether the system exists su file can determine whether the system has been Root

The following command tests a phone that has been Root, and you can see that su exists
```bash
bullhead:/ $ which su
which su
/system/bin/su
```

Another Huawei phone, however, was not Root and no su was detected
```bash
D:\Users\wyz\Desktop\Fastboot>adb shell
HWEVR:/ $ which su
which su
1|HWEVR:/ $
```

Detecting the presence of su commands with the java implementation
```java
package com.example.checksu;

import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import android.widget.Toast;
public class MainActivity extends AppCompatActivity {

@Override
protected void onCreate(Bundle savedInstanceState){
    super.onCreate(savedInstanceState);

    if(checkSuExists())
    {
        Toast.makeText(getApplicationContext(), "su command detected",
        Toast.LENGTH_SHORT).show();
    }
    else
    {
        Toast.makeText(getApplicationContext(), "su command not detected",
        Toast.LENGTH_SHORT).show();
    }
}

public boolean checkSuExists(){
    Process process = null;
    try {
        process = Runtime.getRuntime().exec(new String[] { "which", "su" });
        BufferedReader in = new BufferedReader(new InputStreamReader(process.getInputStream()));
        return in.readLine()!= null;
    } catch(Throwable t){
        return false;
    } finally {
        if(process != null)process.destroy();
    }
    }
}
```

Huawei Phones Without root
![](202303211.png)

Detection of N5x Over root
![](202303212.png)

## Detects the presence of illegal binaries in common directories

The N5x of the author root has su and magisk in some environment variable paths
```bash
bullhead:/system/bin $ which magick
which magick
1|bullhead:/system/bin $ which magisk
which magisk
/system/bin/magisk
bullhead:/system/bin $ which su
which su
/system/bin/su
```

```bash
1|bullhead:/system/bin $ echo $PATH | grep /system/bin
echo $PATH | grep /system/bin
/product/bin:/apex/com.android.runtime/bin:/apex/com.android.art/bin:/system_ext/bin:/system/bin:/system/xbin:/odm/bin:/vendor/bin:/vendor/xbin
```

Therefore, we can use the means of traversing the system PATH to detect some illegal binary files. The implementation of java is as follows
```java
package com.example.checkpath;

import android.os.Bundle;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;
import java.io.Fi;
import java.util.ArrayList;
import java.util.Arrays;


public class MainActivity extends AppCompatActivity {

@Override
protected void onCreate(Bundle savedInstanceState){
    super.onCreate(savedInstanceState);
    checkForBinary("magisk");
    checkForBinary("su");
    checkForBinary("busybox");
}

private static final String[] suPaths = {
    "/data/local/",
    "/data/local/bin/",
    "/data/local/xbin/",
    "/sbin/",
    "/su/bin/",
    "/system/bin/",
    "/system/bin/.ext/",
    "/system/bin/failsafe/",
    "/system/sd/xbin/",
    "/system/usr/we-need-root/",
    "/system/xbin/",
    "/cache/",
    "/data/",
    "/dev/"
    };

public void checkForBinary(String filename){

    String[] pathsArray = this.getPaths();

    boolean flag = false;

    for(String path : pathsArray){
        String completePath = path + filename;
        File f = new File(path, filename);
        boolean fileExists = f.exists();
    if(fileExists){
        Toast.makeText(getApplicationContext(), "Illegal binary detected: "+path+filename, Toast.LENGTH_LONG).show();
    }
    }
    }

private String[] getPaths(){
ArrayList<String> paths = new ArrayList<>(Arrays.asList(suPaths));

    String sysPaths = System.getenv("PATH");

    // If we can't get the path variable just return the static paths
    if(sysPaths == null || "".equals(sysPaths)){
        return paths.toArray(new String[0]);
    }

    for(String path : sysPaths.split(":")){

        if(!path.endsWith("/")){
            path = path + '/';
        }

        if(!paths.contains(path)){
            paths.add(path);
        }
}

    return paths.toArray(new String[0]);
}
}
```