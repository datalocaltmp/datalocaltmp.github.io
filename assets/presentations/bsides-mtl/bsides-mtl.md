---
marp: true
size: 16:9
paginate: true
theme: default
class:
  - invert
markdown.marp.enableHtml: true

---

<!-- 
Notes: 
  * Hi everyone! 

  * Name is datalocaltmp or datalocal"temp".

  * In today's talk titled "A Ghidra Visualization is worth a thousand GDB Breakpoints" I'll be describing how I've gone about generating visualizations of executed code in my binaries to assist in my reverse engineering and debugging tasks.

  * Thanks to BSides Montreal for the space and all the hard work they've done gathering everyone!

-->

<style>
section { 
    font-size: 20px; 
}
img[alt~="center"] {
  display: block;
  margin: 0 auto;
}
video::-webkit-media-controls {
  will-change: transform;
}
</style>
<style scoped>section { font-size: 20px; }</style>

## A Ghidra visualisation is worth a thousand GDB breakpoints.

**@datalocaltmp**

![bg right:30% w:200px](./media/bsides-logo.webp)

---
<!-- footer: 'datalocaltmp | https://datalocaltmp.github.io/ | 2023' -->

<!-- 
Notes: 
  * Independent security researcher focused on mobile
  
  * Previously dedicated to researching privacy within mobile apps and featured in TechCrunch as "theappanalyst"
  
  * Some notable bounty programs I've worked with include Bird Scooters, Biden Campaign App, Ring Cameras, Match.com.
  
  * Nowadays I focus on mobile platform security and in particular reverse engineering the native layer
  
  * Virtual Reality Enthusiast
-->

# $whoami
## Security Researcher
* Previously focused on privacy issues within mobile applications.
  * Featured in TechCrunch for shining a light on apps screenshot'ing credit card information & passwords.

* Claimed bounties with: Bird Scooters, the Biden Campaign App, Ring Cameras, Match.com, etc.

* Nowadays focus on mobile platform security with a recent eye towards native reverse engineering.

* A virtual reality enthusiast!

---

<!-- 
Notes: 
  
  * 

-->

# Resources

* [Debugging Android with LLDB and Voltron](https://datalocaltmp.github.io/debugging-android-with-lldb.html)

* [Visualizing Android Code Coverage Pt.1](https://datalocaltmp.github.io/visualizing-android-code-coverage-pt-1.html)

* [Visualizing Android Code Coverage Pt.2](https://datalocaltmp.github.io/visualizing-android-code-coverage-pt-2.html)

* [NCC Groups Cartographer Tool](https://github.com/nccgroup/Cartographer)

* [Lighthouse Coverage Generation Tool](https://github.com/gaasedelen/lighthouse/tree/master/coverage/frida)

* [Extended Coverage Generation Tool & Samples](https://github.com/datalocaltmp/frida-cov)

---

<!-- 
Notes: 
  
  * My goal today is to introduce you to the world of code coverage visualizations all within 25 minutes. I'll try and do this by first....

  * Describing a scenario where you would have to do traditional debugging with gdb and a decompiler, highlight some of the shortcomings.

  * Then I'll describe how generating visualizations of code coverage can address these issues.

  * Next I'll show you how to generate these visualizations, specifically for a common Android App (in this instance Facebook Messenger).

  * Then I'll finish with addressing the first scenario by using code coverage visualizations and hopefully illustrate their benefit.

-->

![bg right:40% height:80%](./media/browsing.gif)

# Content

* Classic debugging/reverse engineering
    * Tracking execution with GDB

* Why generate visualizations
    * A picture is worth ...

* Generating Visualizations - Android Apps
    * Showcase standard tooling
    * Example: Facebook Messenger Native Library

* Generating Visualizations - Executables
    * Showcasing my frida-cov tool available on [github](https://github.com/datalocaltmp/frida-cov)
    * Example: Meta Quest 2 Binaries

---

<!-- 
Notes: 

    * To start I'd like to talk about when you would conduct traditional debugging or reverse engineering.

    * Often debugging and reversing happens when you have a binary and you want to understand what it's doing but don't have the source code to guide you.

        * I.e Corporate mobile apps, imported  libraries, malware.

    * This process generally consists of static and dynamic components
        
        * Decompiling 
        * Debugging

-->
![bg right:30% width:50% height:80%](./media/complexity.png)

# Classic Reverse Engineering & Debugging

* Often we have a binary that we want to understand but won't have the code
    * A mobile application your company uses on their corporate devices,
    * libraries your companies software interfaces with,
    * or run-of-the-mill malware.

* Generally consists of both dynamic and static analysis
    * Static - Decompiling w/ IDA, Ghidra, Binary Ninja, radare2 etc.
    * Dynamic - Debugging w/ GDB, LLDB, or even Frida

* Problem: Understanding a large amount of complexity can be time-consuming and wrought with red-herrings.
    * I.e Messengers `MCQMessengerOrcaAppLoadThreadViewData` function

---

<!-- 
Notes: 

    * Because I want to have an example that we can actually demo the coverage generation against later, I've written a bit of a toy scenario for the Meta Quest 2.

    * Lets say you're a game dev who wants to interface with a utilities library on the Quest 2

    * Your game calls a function that returns a processes name given a pid, and you're hoping to use this to catch cheaters

    * You go ahead and test out your game and...

-->

# Example: Meta Quest 2 Binaries

* You're a Quest 2 Game Developer and you've written a simple game that depends on a utilities library - `libosutils.so`
    * Note: You don't have the source code for this library.

* Your game calls `getProcessName` within `libosutils.so` to return a process name based on the a pid
    * Perhaps this is part of some anti-cheat engine

* Go ahead and test out your game and ....

---

<!-- 
Notes: 

    * Your anti-cheat engine crashes the game and it's all because you wanted to get the process name using that utilities library.

    * A couple of notes here from this crash dump
        * 1. We see that we're running this from the command line, that's a toy program I've written that we'll use later to generate coverage.
        
        * 2. We can see where the program crashed and we can get underway setting our breakpoints.
    
    * And this is when we'd start the debugging process.

-->

# Example: `libosutils.so` Crash

![center width:900px](./media/crash.png)

---

<!-- 
Notes: 

    * Often we have a problem

-->

# Example:  Debugging `libosutils.so`

1. Decompile `libosutils.so` with your tool of choice and examine `getProcessName`
    * Static portion of our analysis 

2. Run your game with GDB attached and set a breakpoint on `getProcessName`
    * Dynamic portion of our analysis

3. Setting breakpoints within the crashing function
    * In this instance it would be good to set a breakpoint just prior to the crashing instruction at `0x2c9b0`
    * Taking detailed notes on the execution flow prior to the crash to understand the program state prior to the crash

4. Iterate, iterate, iterate
---

<!-- 
Notes: 

    * Often we have a problem

-->

# Example: `libosutils.so` Traditional Debugging

<video controls>
  <source src="./media/lldb-demo.webm" type="video/webm">
  Your browser does not support the video tag.
</video> 

---

<!-- 
Notes: 

    * Often we have a problem

-->

# Example: `libosutils.so` Take-aways

* Process requires a lot of interations to comprehend
* Prone to human-error
* If only we could accelerate 

---

<!-- 
Notes: 

    * Often we have a problem

-->

# Thanks too...

* Frida
    * Ole Andre (Maintainer)
* Ghidra
* Cartographer
* Lighthouse

---

<!-- 
Notes: 

    * O

-->

# Ghidra + Cartographer + Frida

* General Usage

---

<!-- 
Notes: 

    * O

-->

# Generating Coverage: Facebook Messenger

<video controls>
  <source src="./media/crash.webm" type="video/webm">
  Your browser does not support the video tag.
</video> 

---

<!-- 
Notes: 

    * O

-->

# Generating Coverage: Facebook Messenger

<video controls>
  <source src="./media/good.webm" type="video/webm">
  Your browser does not support the video tag.
</video> 

---

<!-- 
Notes: 

    * O

-->

# Importing into Ghidra

Demo Time

---

<!-- 
Notes: 

    * O

-->

# Generating Coverage: Non-App Processes

* What if we want to generate coverage for a process that isn't an app?
* What if we want to generate coverage for a process on a non-rooted device?
* Unfortunately that was not supported ...
    * Until now!
* All demo files, patched Cartographer version, and tooling is available at [github.com/datalocaltmp/frida-cov](https://github.com/datalocaltmp/frida-cov)

---

# Add another library to the mix

1) Instead of using frida-server, use frida gadget via `LD_PRELOAD=./libgadget.so`

2) Within `libgadget.config.so` reference the lighthouse modifided javascript `frida-drcov.js`

3) `frida-drcov.js` stores raw coverage data in `/data/local/tmp/rawcov.dat`

4) Use modified `frida-drcov.py` to convert raw data to DragonDance coverage map

5) Import converage map into Ghidra!

---

# Obligatory Diagram

![center width:900px](./media/sequence.png)

---

# Live Demo!

---

# Questions?

---

# Thanks!
