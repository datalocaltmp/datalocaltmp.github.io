---
marp: true
size: 16:9
paginate: true
theme: default
class:
  - invert
markdown.marp.enableHtml : true

---

<!-- 
Notes: 
  * Hi everyone! 

  * Name is datalocaltmp or datalocal"temp".

  * In today's talk titled "A Ghidra Visualization is worth a thousand GDB Breakpoints" I'll be describing how I've gone about generating visualizations of executed code in my binaries to assist in my reverse engineering and debugging tasks.

  * Thanks to BSides Montreal for the space and all the hard work they've done gathering everyone!

  * Also after this presentation I'll be tweeting out a link to my slide-deck so if you're interested in any of the links here; you can grab them there.
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
<style scoped>section { font-size: 30px; }</style>

## A Ghidra visualisation is worth a thousand GDB breakpoints.

**@datalocaltmp**

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
  

  * I'll describe the general problem 

  * We'll go through the current options for visualization generation within Apps

  * We'll then follow up with an extended method for generation visualziations when you're not looking specifically at Applications or do not have root.

-->

![bg right:40% height:80%](./media/browsing.gif)

# Content

* Traditional debugging/reverse engineering 
    * Examining Quest 2 binaries with LLDB + Voltron

* Why generate visualizations
    * A picture is worth ...

* Generating Visualizations Pt.1
    * Visualizing the execution of Android applications
    * Example: Facebook Messenger Native Library

* Generating Visualizations Pt.2
    * What if you're not reversing an app or you don't have root?
    * Example: Meta Quest 2 Binaries

---

<!-- 
Notes: 

    * Often we have a problem

-->
![bg right:30% width:50% height:80%](./media/complexity.png)

# Traditional Debugging/Reverse Engineering

* Often we have a binary that we want to understand
    * A mobile application your company deploys to Google Play Store,
    * Native libraries you're interfacing with,
    * Malware on your grandmothers PC.

* Generally an analysis has both dynamic and static components
    * Decompilers like IDA, Ghidra, Binary Ninja, radare2 etc.
    * Debuggers like GDB, LLDB, or even Frida 

* Problem: Understanding large amounts of complexity is time-consuming and wrought with pitfalls and red-herrings.

---

<!-- 
Notes: 

    * Often we have a problem

-->

# Example: Meta Quest 2 Binaries

* Scenario:
    * You're a Meta Engineer and you've written a simple OS utilities library named `libosutils.so`
    * You write a toy program that calls the `getProcessName` function within the library to return a pid's process name

---

<!-- 
Notes: 

    * Often we have a problem

-->

# Example: `libosutils.so` Crash

![center width:900px](./media/crash.png)

---

<!-- 
Notes: 

    * Often we have a problem

-->

# Example: `libosutils.so` Crash

![center width:900px](./media/crash.png)

---

<!-- 
Notes: 

    * Often we have a problem

-->

# Example: `libosutils.so` Traditional Debugging

<video controls>
    <source src="./media/crash.webm" type="video/webm">
    Browser does not support video tag.
</video>

---

<!-- 
Notes: 

    * Often we have a problem

-->

# Example: `libosutils.so` Traditional Debugging

<video controls>
    <source src="./media/good.webm" type="video/webm">
    Browser does not support video tag.
</video>