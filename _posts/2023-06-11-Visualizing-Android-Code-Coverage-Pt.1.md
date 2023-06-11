Decompilers are essential when reverse engineering Android applications and binaries; unfortunately with static analysis it's up to the reverse engineer to determine which of these complex paths to investigate.

This write-up is meant to show how to find narrow reverse engineering efforts to executed paths using Frida, Lighthouse Coverage Generator, Ghidra, and Dragon Dance. At the end of this write-up you should have a solid basis for visualizing code coverage in Ghidra as seen below.

![example](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/example.webp)


We will be looking at the popular application Facebook Messenger in this part; part 2 will cover writing a harness to trigger this functionality and how to generate coverage for native Android binaries (which is not currently supported).

#### Pre-requisites
To follow along with this write-up you'll need to install the following tools:

* Ghidra == 9.0.2
	* DragonDance currently only supports up to 9.0.2
* DragonDance Ghidra Extension found [here](https://github.com/0ffffffffh/dragondance).
* Git clone the Lighthouse Coverage Explorer [here](https://github.com/gaasedelen/lighthouse).
* Example application (Using Facebook Messenger from [here](https://m.apkpure.com/facebook-messenger/com.facebook.orca)).
* Frida Server & Frida Tools (Learn how to set-up Frida for Android [here](https://frida.re/docs/android/)).
* Android emulator or device with `root` access

#### Setting up
Let's setup Frida and find an interesting native library within our Application of choice.

1. On your Android device run `./frida-server-XX.X.X-android-<arch>` from /data/local/tmp
2. Open the app you'd like to get coverage for via the gui
3. From the host terminal run `frida-ps -U | grep <packageName||applicationName>` and note the Process name
4. Start the frida interpreter with `frida -U <processName>`
5. From the interpreter run `Process.enumerateModules()` to find all the native libraries loaded by the application (note: some libraries are only loaded as necessary and may require you hit those paths first).
7. From here pick a native library you'd like to reverse engineer, for this example we'll reverse `libmsysinfra.so`

#### Frida Tracing `libmsysinfra.so`
Now that we have a native library within an Application we'd like to investigate, `libmsysinfra.so` in this case, let's find a function worth reverse engineering.

1. While the application is running, execute `frida-trace -U Messenger -i 'libmsysinfra.so!*'`
	   (Note: `'<nativeLibrary>!<functionName>'` is the format associated with tracing native code with `-i` and this example will trace all exported functions within the native library).
2. Use the application until you trigger some functionality that you are interested in; in this instance opening a Messenger Chat has triggered the function `MCQMessengerOrcaAppLoadThreadViewData()` which has subsequently called many many additional functions, quite interesting!

![interestingFunction](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/interestingFunction.png)

3. Extract the native library and load it into Ghidra
4. Find the initial function we'll be starting with in Ghidra and marvel at it's complexity, how can we narrow down our reversing? Coverage!

![confusingFunctionGraph](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/confusingFunctionGraph.png)


#### Generating Code Coverage for `MCQMessengerOrcaAppLoadThreadViewData()`
Lets generate a visualization of the basic block code coverage to help guide how we reverse engineer this function.
1. Navigate to `lighthouse/coverage/frida` directory
2. Execute `python3 frida-drcov.py -D emulator-5554 Messenger` and note that this will kill the application we will need to narrow our search before generating the coverage. To narrow our results we'll use the `-w ` and `-t` flags described below:
   
   `usage: frida-drcov.py [-h] [-o OUTFILE] [-w WHITELIST_MODULES] [-t THREAD_ID] [-D DEVICE] target`

3. To find the thread ID we'll need to use another terminal and trace our function of interest with:
   
    `frida-trace -U Messenger -i 'libmsysinfra.so!MCQMessengerOrcaAppLoadThreadViewData'`

4. While the function is being traced, use the application to trigger the function and note the thread ID, in the image below it's `0x2dbe` which corresponds to `11710` in decimal. Using this thread ID and library filter we are able to capture 1118 blocks of coverave which are saved to `frida-cov.log`

![coverageGeneration](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/coverageGeneration.png)


#### DragonDance Visualization
Now we have the coverage data for our function of interest let's use Dragon Dance Ghidra extension to visualize the basic blocks actually taken.
1. Install the extension using the instructions [here](https://github.com/0ffffffffh/dragondance#installation).
2. Once the extension is installed, open the native library to analyze and from `Window>Dragon Dance` add the coverage file generated in the previous section.
![importingCoverage](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/importingCoverage.png)

3. Right-click the coverage file and select "switch-to"; if you have Ghidra viewing the the function of interest it should immediately change colour to blue/purple/red if coverage has been found.
4. Marvel and the beauty of a coloured function graph knowing that you've just clarified your reverse engineering path!

![coverageFunctionGraph](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/coverageFunctionGraph.png)