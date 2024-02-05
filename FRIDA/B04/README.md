
<!— @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} —>

<!— code_chunk_output —>

- [FRIDA Script Series (IV) Updates: Major Updates of Several Major Mechanisms] (#frida Script Series IV Updates: Major Updates of Several Major Mechanisms)
- [Process Creation Mechanism Big Update] (#Process Creation Mechanism Big Update)
- [Existing Problem: Unable to prepare parameters and environment for new process] (#Existing Problem Unable to prepare parameters and environment for new process)
- [Cause of Problem (I): No Implementation in Original Source] (# Cause of Problem: No Implementation in Original Source)
- [Cause of the problem (ii): 'spawn()' Historical Legacy] (#Cause of the problem (ii) Historical Legacy of spawn)
- [process creation mechanism update (i): parameters, directories, environment can be set] (# process creation mechanism update a parameter - directories - environment can be set)
- [Process Creation Mechanism Update (II): Platform-specific functions implemented using 'aux' mechanism] (#Process Creation Mechanism Update II) Platform-specific functions implemented using 'aux' mechanism)
- [Subprocess Instrumentation Big Update] (#Subprocess Instrumentation Big Update)
- [Problem: Sub-process multithreading mechanism chaos easy to collapse] (#Problem: Sub-process multithreading mechanism chaos easy to collapse)
- [Resolution: Introduces new subprocess control 'API`:`Child gating'] (#Resolution Introduces new subprocess control apichild-gating)
- [exit (crash) message mechanism big update] (#exit crash message mechanism big update)
- [Problem: Message Not Ready When Program Crashes] (#ProblemMessage Not Ready When Program Crashes)
- [Solution: Interpolate 'APIs' of stop processes for major platforms] (#SolutionInterpolate stop process api for major platforms)

<!— /code_chunk_output —>

## FRIDA Script Series (IV) Updates: Major Updates to Several Major Mechanisms

Recently indulged in learning, can't get rid of oneself, there are still some problems can't think of rode the sister, go through the official website document again, find out that several recent major versions, update is quite large, so spent a bit of time and effort, digestion, absorption, here to share with you.

### Process Creation Mechanism Big Update

#### Problem: Unable to prepare parameters and environment for new process

When we use the 'binding' of 'Frida Python', it is generally written as follows:

```bash
pid = device.spawn(["/bin/cat", "/etc/passwd"])
```

Or on iOS platforms, it would read:

```bash
pid = device.spawn(["com.apple.mobilesafari"])
```

At this point it seems that this 'API' can only be written in this way, and there are many problems in writing this, such as not considering the full list of parameters, or whether the environment of the new process is inherited from the bundled 'host' machine environment or the device 'client' environment? Nor does it take into account the desire to implement custom functions, such as turning 'safari' on with 'ASLR' mode turned off.

#### Cause of the problem (1): It was not implemented in the original source code

Let's start by looking at how this 'API' was achieved in 'frida-core':

```vala
namespace Frida {
    ...
    public class Device : GLib.Object {
    ...
    public async uint spawn(string path,
    string[] argv, string[] envp)
    throws Frida.Error;
    public uint spawn_sync(string path,
    string[] argv, string[] envp)
    throws Frida.Error;
}
...
}
```

The code is written in the language 'vala', 'frida-core' is written in 'vala', 'vala' looks like 'C#' and is eventually compiled into 'C' code. As you can see from the code, the first method—'spawan' is asynchronous, and the caller can do something else once it has been called, rather than waiting for the call to complete, while the second method—'spawn_sync' needs to wait until the call is completely completed and returned.

Both methods are compiled into C code as follows:

```c
void frida_device_spawn(FridaDevice * self,
const gchar * path,
gchar ** argv, int argv_length,
gchar ** envp, int envp_length,
GAsyncReadyCallback callback, gpointer user_data);
guint frida_device_spawn_finish(FridaDevice * self,
GAsyncResult * result, GError ** error);
guint frida_device_spawn_sync(FridaDevice * self,
const gchar * path,
gchar ** argv, int argv_length,
gchar ** envp, int envp_length,
GError ** error);
```

The first two functions form the procedure 'spawn()', first calling the first to obtain a callback, and then calling the second function—'spawn_finish()' when the callback is obtained, the return value of the callback is used as an argument to 'GAsyncResult'. The final return value is 'PID' and of course 'error no' if there is 'error'.

The third function—'spawn_sync()' also explains that it is completely synchronized, and 'Frida Python' uses this in fact. 'Frida nodejs' uses the first two, because the bindings in 'nodejs' are asynchronous by default. Of course, it should also be considered in the future to migrate the binding of 'Frida Python' to an asynchronous mode, using the 'async/await' mechanism introduced in the 'Python 3.5' version.

Going back to the two examples in the previous section, it turns out that the format of the call is not exactly the same as the 'API' that we wrote. A close look at the source code shows that a list of strings like 'envp' is not exposed to the upper 'API'. If you look at the binding process of 'Frida Python', you can see that it was actually written later in the binding as follows:

```c
envp = g_get_environ();
envp_length = g_strv_length(envp);
```

That is to say, ultimately we pass on the 'spawn()' function to the caller's 'Python' environment, which is clearly incorrect, and the 'Python' environment of 'host' is certainly not the same as the 'Python' of 'client', as in the case where 'client' is 'iOS' or 'Android'.

Of course, we set in 'frida-server' that 'envp' will be overlooked by default during the process of 'spawn()' 'Android or 'iOS', which more or less reduces the problem.

#### The cause of the problem (ii): 'spawn()' The legacy of the problem

Another problem is the definition of 'spawn()', this ancient 'API' —'string[] envp', which means that it cannot be empty (if written as 'string[]? envp' can be virtually empty), meaning that there is no fundamental distinction between "default environment configuration" and "no environment configuration at all".

#### Process Creation Mechanism Update (I): Parameters, directories, environment can be set

Since you have decided to fix this 'API', consider all the issues related to this 'API':

- How to provide some additional environment parameters to the command
- Set working directory
- Custom Standard Input Stream
- Platform-specific parameters passed in

After fixing the 'bug' above, the final code will look like this:

```vala
namespace Frida {
    ...
    public class Device : GLib.Object {
    ...
    public async uint spawn(string program,
    Frida.SpawnOptions? options = null)
    throws Frida.Error;
    public uint spawn_sync(string program,
    Frida.SpawnOptions? options = null)
    throws Frida.Error;
    }
    ...
    public class SpawnOptions : GLib.Object {
    public string[]? argv { get; set; }
    public string[]? envp { get; set; }
    public string[]? env { get; set; }
    public string? cwd { get; set; }
    public Frida.Stdio stdio { get; set; }
    public GLib.VariantDict aux { get; }

    public SpawnOptions();
    }
    ...
}
```

Finally, we go back to the example code at the beginning, which we wrote:

```py
device.spawn(["com.apple.mobilesafari"])
```

Now it has to be:

```py
device.spawn("com.apple.mobilesafari")
```

The first argument is the command to be 'spawn', followed by a list of strings for 'argv', and 'argv' is used to set the parameter's command, such as:

```py
device.spawn("/bin/busybox", argv=["/bin/cat", "/etc/passwd"])
```

If you want to replace the default environment with your own settings:

```py
device.spawn("/bin/ls", envp={ "CLICOLOR": "1" })
```

Change only one parameter in the environment variable:

```py
device.spawn("/bin/ls", env={ "CLICOLOR": "1" })
```

To change the working directory of a command:

```py
device.spawn("/bin/ls", cwd="/etc")
```

To redirect a standard input stream:

```py
device.spawn("/bin/ls", stdio="pipe")
```

>'stdin'The default input is 'inherit', plus the option 'stdio="pipe"', which makes it a pipe.

##### Process Creation Mechanism Update (II): 'aux' mechanism for platform-specific functionality

Here we cover almost all of the options for 'spawn()', leaving the last option—'aux', which is essentially a dictionary of platform-specific parameters. This parameter can be set with a 'Python' binding, and any key-value pair that cannot be captured by the preceding parameter is placed directly at the end of the command line.

For example, open 'Safari' and notify it to open a specific 'URL':

```py
device.spawn("com.apple.mobilesafari", url="https://bbs.pediy.com")
```

Another example would be to execute a command in the mode of turning off 'ASLR':

```py
device.spawn("/bin/ls", aslr="disable")
```

Or open an Android 'Activity' with a specific 'app':

```py
spawn("com.android.settings", activity=".SecuritySettings")
```

The 'aux' mechanism makes command lines easily customizable, which is much easier than writing code separately for each platform. In fact, the underlying code hasn't changed in one line^.<
