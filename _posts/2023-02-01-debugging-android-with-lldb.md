With the (new?) M1 Macbooks I've been experimenting with conducting security assessments on Apps within Android emulators which benefit from the native Arm architecture. Unfortunately as of Android ndk 24, GDB has been removed and for LLDB instead (changelog [here](https://github.com/android/ndk/wiki/Changelog-r24)) and the M1 Macbook doesn't support gdb all that well yet. 

While I had been getting along just fine with the gdbserver setup described in this fantastic blog written by [Simone Aonzo](https://simoneaonzo.it/gdb-android/) I figure Google is telling me it's time to move on. So what better to do than describe how I replaced gdb/gdbserver/gef with lldb/lldb-server/voltron.

## Pre-requisites
* Android emulator with root privledges
* Latest Android NDK (at the time of writting it is android-ndk-r25b-darwin.dmg, latest available [here](https://developer.android.com/ndk/downloads))

## Emulator Setup

From the root directory of the NDK find the lldb-server appropriate to your device, if you're debugging and emulator on an M1 Macbook you'll want aarch64.

```bash
$ cd ~/Downloads/android-ndk-r25b
$ find ./ -name 'lldb-server'
.//toolchains/llvm/prebuilt/linux-x86_64/lib64/clang/14.0.6/lib/linux/i386/lldb-server
.//toolchains/llvm/prebuilt/linux-x86_64/lib64/clang/14.0.6/lib/linux/arm/lldb-server
.//toolchains/llvm/prebuilt/linux-x86_64/lib64/clang/14.0.6/lib/linux/aarch64/lldb-server
.//toolchains/llvm/prebuilt/linux-x86_64/lib64/clang/14.0.6/lib/linux/x86_64/lldb-server
```

Copy the lldb-server binary to your emulators temporary folder which is /data/local/tmp

```bash
$ adb push .//toolchains/llvm/prebuilt/linux-x86_64/lib64/clang/14.0.6/lib/linux/aarch64/lldb-server /data/local/tmp
$ adb shell "chmod 777 /data/local/tmp/lldb-server"
```

Before continuing we'll need to setup networking by configuring adb to forward the emulators local port.

```bash
$ adb forward tcp:1337 tcp:1337
```

And finally enter an adb shell, disable selinux, and start the binary you'd like to target with lldb. For this example we can debug the dexdump binary.

```bash
$ adb shell
# su
# setenforce 0
# /data/local/tmp/lldb-server 
```

Finally we'll target the ziptool binary within the Android emulator as a toy example. This binary can be used as in the following image with any .apk file on disk.

![[Screen Shot 2023-01-25 at 8.40.29 PM.png]]

Finally, run lldb-server from /data/local/tmp with the following command:

```bash
# ./lldb-server platform --server --listen 127.0.0.1:1337
```

## LLDB and Voltron

First download voltron from here and add a .lldbinit file into your home directory.

```bash
$ git clone https://github.com/snare/voltron
$ cd voltron
$ ./install.sh
$ find /Users/user/Library/Python/ -name 'entry.py'
/Users/user/Library/Python/3.8/lib/python/site-packages/voltron/entry.py
$ echo 'command script import /Users/user/Library/Python/3.8/lib/python/site-packages/voltron/entry.py' > ~/.lldbinit
$ cat ~/.lldbinit
command script import /Users/user/Library/Python/3.8/lib/python/site-packages/voltron/entry.py
```

Also you'll need to change the default voltron port from 5555 so as to not collide with adb. This can be done by creating a config file under the ~/.voltron directory and modifying the port 5555 to whatever you'd like (I use 5222 here).

```shell
$ cat voltron/voltron/config/default.cfg > ~/.voltron/config
$ vim ~/.voltron/config

server:
    listen:
        domain: "~/.voltron/sock"
        tcp:
        - 127.0.0.1
        - 5222 <- change this to 5222.
view:
    api_url: http://localhost:5222/api/request <- and this to 5222.
```

If Voltron has installed successfully it will look like the following when you run lldb

```bash
$ lldb
Voltron loaded.
```

Once you have lldb started and the device is running lldb-server, start by selecting the platform and connecting to the device.

```shell
$ lldb
Voltron loaded.
(lldb) platform select remote-android
	Platform: remote-android
	Connected: no
	
(lldb) platform connect connect://localhost:1337
	Platform: remote-android
	Triple: aarch64-unknown-linux-android
	OS Version: 31 (5.10.110-android12-9-00004-gb92ac325368e-ab8731800)
	Hostname: localhost
	Connected: yes
	WorkingDir: /data/local/tmp
    Kernel: #1 SMP PREEMPT Tue Jun 14 13:40:53 UTC 2022
```

Then create a target for lldb, in this instance ziptool, and then run ziptool with arguments similar to below.

```
(lldb) target create /system/bin/ziptool
	Current executable set to '/system/bin/ziptool' (aarch64).

(lldb) process launch --stop-at-entry -- zipinfo /data/local/tmp/AdsDynamite.apk
```

Finally, use the `voltron view {disasm,d,dis,command,c,cmd,registers,r,reg,register,memory,m,mem,stack,s,st,breakpoints,b,bp,break,backtrace,t,bt,back}` command to create various views within your terminal (highly recommend using tmux to get more views for your buck).

![setup](https://github.com/theappanalyst/theappanalyst.github.io/raw/main/_posts/lldb-voltron-setup.png)

And that's it, hope this helps with all your LLDB/Android needs!

---

## Appendix

Apply the patch below to voltron to support aarch64

```
Subject: [PATCH] adding aarch64 clone of arm64

---
 voltron/dbg.py                   |  2 +
 voltron/plugins/view/register.py | 86 ++++++++++++++++++++++++++++++++
 2 files changed, 88 insertions(+)

diff --git a/voltron/dbg.py b/voltron/dbg.py
index 4d92f5b..0ef4b0c 100644
--- a/voltron/dbg.py
+++ b/voltron/dbg.py
@@ -98,6 +98,7 @@ class DebuggerAdaptor(object):
         "armv7":    {"pc": "pc", "sp": "sp"},
         "armv7s":   {"pc": "pc", "sp": "sp"},
         "arm64":    {"pc": "pc", "sp": "sp"},
+        "aarch64":    {"pc": "pc", "sp": "sp"},
         "powerpc":  {"pc": "pc", "sp": "r1"},
     }
     cs_archs = {}
@@ -110,6 +111,7 @@ class DebuggerAdaptor(object):
             "armv7":    (capstone.CS_ARCH_ARM, capstone.CS_MODE_ARM),
             "armv7s":   (capstone.CS_ARCH_ARM, capstone.CS_MODE_ARM),
             "arm64":    (capstone.CS_ARCH_ARM64, capstone.CS_MODE_ARM),
+            "aarch64":    (capstone.CS_ARCH_ARM64, capstone.CS_MODE_ARM),
             "powerpc":  (capstone.CS_ARCH_PPC, capstone.CS_MODE_32),
         }
 
diff --git a/voltron/plugins/view/register.py b/voltron/plugins/view/register.py
index a4a0a4a..720ba2d 100644
--- a/voltron/plugins/view/register.py
+++ b/voltron/plugins/view/register.py
@@ -115,6 +115,16 @@ class RegisterView (TerminalView):
                 'category':         'general',
             },
         ],
+        'aarch64': [
+            {
+                'regs':             ['pc', 'sp', 'x0', 'x1', 'x2', 'x3', 'x4', 'x5', 'x6', 'x7', 'x8', 'x9', 'x10',
+                                    'x11', 'x12', 'x13', 'x14', 'x15', 'x16', 'x17', 'x18', 'x19', 'x20',
+                                    'x21', 'x22', 'x23', 'x24', 'x25', 'x26', 'x27', 'x28', 'x29', 'x30'],
+                'label_format':     '{0:3s}',
+                'value_format':     SHORT_ADDR_FORMAT_64,
+                'category':         'general',
+            },
+        ],
         'powerpc': [
             {
                 'regs':             ['pc','msr','cr','lr', 'ctr',
@@ -313,6 +323,82 @@ class RegisterView (TerminalView):
             }
         },
         'arm64': {
+            'horizontal': {
+                'general': (
+                    "{pcl} {pc}{pcinfo}\n"
+                    "{spl} {sp}{spinfo}\n"
+                    "{x0l} {x0}{x0info}\n"
+                    "{x1l} {x1}{x1info}\n"
+                    "{x2l} {x2}{x2info}\n"
+                    "{x3l} {x3}{x3info}\n"
+                    "{x4l} {x4}{x4info}\n"
+                    "{x5l} {x5}{x5info}\n"
+                    "{x6l} {x6}{x6info}\n"
+                    "{x7l} {x7}{x7info}\n"
+                    "{x8l} {x8}{x8info}\n"
+                    "{x9l} {x9}{x9info}\n"
+                    "{x10l} {x10}{x10info}\n"
+                    "{x11l} {x11}{x11info}\n"
+                    "{x12l} {x12}{x12info}\n"
+                    "{x13l} {x13}{x13info}\n"
+                    "{x14l} {x14}{x14info}\n"
+                    "{x15l} {x15}{x15info}\n"
+                    "{x16l} {x16}{x16info}\n"
+                    "{x17l} {x17}{x17info}\n"
+                    "{x18l} {x18}{x18info}\n"
+                    "{x19l} {x19}{x19info}\n"
+                    "{x20l} {x20}{x20info}\n"
+                    "{x21l} {x21}{x21info}\n"
+                    "{x22l} {x22}{x22info}\n"
+                    "{x23l} {x23}{x23info}\n"
+                    "{x24l} {x24}{x24info}\n"
+                    "{x25l} {x25}{x25info}\n"
+                    "{x26l} {x26}{x26info}\n"
+                    "{x27l} {x27}{x27info}\n"
+                    "{x28l} {x28}{x28info}\n"
+                    "{x29l} {x29}{x29info}\n"
+                    "{x30l} {x30}{x30info}\n"
+                ),
+            },
+            'vertical': {
+                'general': (
+                    "{pcl} {pc}{pcinfo}\n"
+                    "{spl} {sp}{spinfo}\n"
+                    "{x0l} {x0}{x0info}\n"
+                    "{x1l} {x1}{x1info}\n"
+                    "{x2l} {x2}{x2info}\n"
+                    "{x3l} {x3}{x3info}\n"
+                    "{x4l} {x4}{x4info}\n"
+                    "{x5l} {x5}{x5info}\n"
+                    "{x6l} {x6}{x6info}\n"
+                    "{x7l} {x7}{x7info}\n"
+                    "{x8l} {x8}{x8info}\n"
+                    "{x9l} {x9}{x9info}\n"
+                    "{x10l} {x10}{x10info}\n"
+                    "{x11l} {x11}{x11info}\n"
+                    "{x12l} {x12}{x12info}\n"
+                    "{x13l} {x13}{x13info}\n"
+                    "{x14l} {x14}{x14info}\n"
+                    "{x15l} {x15}{x15info}\n"
+                    "{x16l} {x16}{x16info}\n"
+                    "{x17l} {x17}{x17info}\n"
+                    "{x18l} {x18}{x18info}\n"
+                    "{x19l} {x19}{x19info}\n"
+                    "{x20l} {x20}{x20info}\n"
+                    "{x21l} {x21}{x21info}\n"
+                    "{x22l} {x22}{x22info}\n"
+                    "{x23l} {x23}{x23info}\n"
+                    "{x24l} {x24}{x24info}\n"
+                    "{x25l} {x25}{x25info}\n"
+                    "{x26l} {x26}{x26info}\n"
+                    "{x27l} {x27}{x27info}\n"
+                    "{x28l} {x28}{x28info}\n"
+                    "{x29l} {x29}{x29info}\n"
+                    "{x30l} {x30}{x30info}\n"
+                ),
+            },
+        },
+        'aarch64': {
             'horizontal': {
                 'general': (
                     "{pcl} {pc}{pcinfo}\n"
-- 
2.32.1 (Apple Git-133)
```
