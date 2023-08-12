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
  
  * Talking about Virtual Workspaces and how they've helped my Security Research.
  
  * Thanks to DCGVR for the space and all the hard work they've done gathering everyone!
  
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

# Security Research in Virtual Workspaces

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
* Previously focussed on privacy issues within mobile applications.
  * Featured in TechCrunch for shining a light on apps screenshot'ing credit card information & passwords.

* Claimed bounties with: Bird Scooters, the Biden Campaign App, Ring Cameras, Match.com, etc.

* Nowadays focus on mobile platform security with a recent eye towards native reverse engineering.

* A virtual reality enthusiast!

---

<!-- 
Notes: 
  * In today's talk I'll be describing my experience in Virtual Workstations
    * You can see an example of that on the right there...
  
  * First I'll attempt to convince you that Security Research is something known as deep work
    * Apologies if this portion is filled with some corporate speak but I think it's worth saying
    * Trust me that we'll get away from this and into the virtual setup and technical portion.
  
  * Then I'll describe why I think virtual workspaces really lend themselves to Security Research
  
  * From there I'll get into my personal set-up and some pro-tips
  
  * I'll showcase a couple of technical examples of virtual workspaces
    * Debugging using LLDB and Voltron (very similar to GDB and GEF if you're familiar)
    * SRE with Ghidra and a visualization extension named Dragon Dance
      * This example particularly benefits given it's visual nature
  
  * Finally, I'll talk about what I see as the future of the space and things I'm excited about.
-->

![bg right](../media/FutureIsNow.gif)

# Content

* Security Research - Deep Work

* Virtual Workspaces - A Deep Work Environment

* Personal Setup:
  * Software & Hardware

* Example Tasks:
  * Debugging: LLDB & Voltron
  * Reverse Engineering: Ghidra & DragonDance

* Future VR Environments

---

<!-- 
Notes: 
  * We're going to kick off on describing Deep Work, and how it relates to Security Research.

  * It's a concept introduced by Cal Newport in his blog at the link there, and defines it as the "Cognitively demanding activities that leverage our training to generate rare and valuable results"

  * But, in another sense, it's the work we do that leverages our haxor skillz to find bugs.

  * This term, while sounding a bit like corporate jargon, will likely resonate with many of you; it really relates to the state I find myself in when I produced meaningful security research.

  * And so I imagine any work that you do to find CVE's or produce PoC's is likely deep work as well!
-->

# Security Research - Deep Work?

  * "Deep Work" coined by Cal Newport with a fantastic introduction in his blog [here](https://calnewport.com/knowledge-workers-are-bad-at-working-and-heres-what-to-do-about-it/)
  
    * "Cognitively demanding activities that leverage our training to generate rare and valuable results" - Cal Newport

    * "Work that leverages our skillz to find bugs" - datalocaltmp

  * The work that you do that produces a CVE or PoC is likely deep work.

---

<!-- 
Notes: 
  * Preparation is key as you're aiming to dedicate 1-3 hours of focused work time.

  * Ensure you have a clear goal in mind for this time on a topic that you think really benefits from deep work (perhaps not sending emails or updating socials).

  * Chunk that goal up and aim to tackle the first logical chunk, then try and stretch into starting the next in that session.

  * Track how long you are in this deep work state, it'll help you recognize the work you're doing and improve.

  * You only need to be in this state for a short period of time, for many that lends itself to using a virtual workspace.

-->

# Deep Work 101

* Preperation
  * Prepare your work environment to focus.

* Clarify
  * Set a dedicated goal for yourself.

* Stretch
  * Aim to tackle the next logical chunk work towards your goal;
  * attempt to push beyond that chunk.

* Track
  * Complete 1-3 hours of deep work, track your time, rinse and repeat.
  * The short nature of deep work does lend itself to VR.

---

<!-- 
Notes: 
  * Here is an example of what that would look like in practice for myself.

  * I'd clarify my goal to myself; perhaps understanding a vulnerability in a structure that is passed to a function

  * I'd define the chunks of work that I'd need to complete, perhaps starting with defining the members of the struct within a Ghidra Data Type.

  * I'd then prepare workstation and open all the software I'd need for that; perhaps GDB and Ghidra.

  * And then I'd try and stretch my deep work session into including working on investigating all access to a member of that struct.

  * And that's how it might look for me to get into my deep work state, however as I'm sure you'll resonate with;

-->

![bg right:33% width:350px](../media/struct.png)

# Deep Work: An Example

  * Clarify
    * "I'd like to understand a vulnerability in a structure that is passed to a function"
    * Concrete chunk: Define a Ghidra Data Type for that struct
 
  * Preperation
    * Prepare my workstation/environment, open Ghidra, set-up GDB.
    * "I'll open Ghidra, set my break-points in GDB, and begin analyzing"

  * Stretch
    * "Once I define the data type I'd like to understand all access to a member within that struct."

---

<!-- 
Notes: 
  * Distractions kill deep work.

  * When a distraction appears and you have to context switch. this can kill your deep work state.

  * For example in a lot of my work, which could involve tracking variables across functions when reverse engineering, or comprehending complex security write-ups; I find that distractions similar to destroying the wall of yarn that ties ideas.

  * So in a distraction rich environment I find myself often caught in the shallow end attempt to work on a deep problem.

  * This involves doing things that don't really require any skill like updating scheduling future work or updating my socials. The feel productive because they do have value, but they're not enhanced by my skills.

  * I found it wasn't often that I immersed myself in the deep end of deep work.

  * And of course if I have an audience I have to include some photos of my distractions

-->

# Distractions kill Deep Work

![bg right:33%](../media/organized.jpg)

  * Context switching destroys focus.
    * Tracking use of variables across decompiled functions.
    * Comprehending complex security write-ups.
    * Analogous to a wall of yarn that is destroyed every context switch.

  * Often caught in the shallow end while working on a deep problem.
    * "Oh I should plan a time for looking at that".
    * "Gotta spend time take producing content for my Twitter".
    * It's easy work and it feels productive...

  * It wasn't often that I immersed myself in the deep end, in deep work.

---

<!-- 
Notes: 
  
  * There's my workstation with my bizarre cat photo and 1980 wood paneled walls.

  * And there are two of my key distractors (there was no way I'd put a picture of my partner there...)

  * So how do I avoid distractions and embrace deep work?

-->

![bg](../media/distraction.jpeg)
![bg](../media/distraction3.jpeg)
![bg](../media/distraction2.jpeg)

--- 

<!-- 
Notes: 
  
  * I embrace virtual workspaces that I believe are really suitable for deep work.

  * I think there are a lot of reasons to embrace these workstations because:

  * They're highly configurable; really providing a workspace that can change to match the task at hand.

  * They're extremely portable; only requiring a vr headset in addition to a laptop to provide a consistent and familiar environment.

  * They're Immersive; it's quite easy to completely disconnect yourself from the outside world

  * I'll quickly expand out each of those points;

-->

# Virtual Workspaces - Deep Work Environments

  * **Configurable** - A workspace that changes to match the task at hand

  * **Portable** - Only requires a headset and a laptop to provide a consistent and familiar environment
  
  * **Immersive** - Completely disconnects you from the distractions of the Non-Virtual World

--- 

<!-- 
Notes: 
  
  * For configuration; having a space that is dedicated to your deep work, isn't something new. This person here has even gone so far as to use a dedicated tent to disconnect themselves while they're in this working state. 
  
  * Here are some of the key points that I think will resonate with you all:

  * Virtual monitors are available to provide you with more working space than a single screen; they can be configured to be any size.

  * There are mature tools like Unity to create your own virtual environments and security research tools.

  * Though I don't think anyone has spent a serious amount of time in making interactive VR tools and if you do please @ me on twitter!

-->

![bg right:33% width:330px](../media/deep_tent.png)

# Virtual Workspaces - Configuration

  * Virtual monitors in any configuration

  * Mature tools like Unity to modify or create your own virtual environments and tools
  
  * A world of interactive VR security research tools that really hasn't been explored

--- 

<!-- 
Notes: 
  
  * When comparing a virtual workstation to something in your home; it's quite a bit more portable.

  * As I said you really only need a vr headset in addition to your laptop to gain the benefits.

  * Hand-tracking has gotten to the point where you don't really need any additional controllers. It comes with the added benefit of looking like Tom Cruise from minority report.

  * Also there is something to be said for providing a consistent and familiar environment as it's stored on the headset itself.

  * Although you'll still need some method of associating the headset with the computer, which can be either over the LAN or tethered
    * I personally pick tethered as it provides an extremely low-latency connection.

-->

# Virtual Workspaces - Portability

  * Hand-tracking precludes any requirement for additional controllers.

  * Consistent and familiar environments saved to the headset and provided at a moments notice.
  
  * Does require tethering or connecting over the LAN

--- 

<!-- 
Notes: 
  
  * And finally Immersion;

  * I can't stress enough how a pair of noise cancelling headphones and a vr headset can completely disconnect you from a distracting environment.

  * A pro-tip is to match the soundscape to your environment; it can really enhance your immersion when you're senses are inline with each other. (I should invest in some smellscape candles...)

  * Finally, if you do need to be reachable while deep working it's best to setup a messaging application on your computer. It can be fairly frightening if someone physically interacts with you while you're in this state of immersion!

  * Now I want to share some of my environments, specifically the environments I capture on my phone when I'm on vacation. I think it's a nice way of collecting space and capturing environments you find relaxing.

-->

# Virtual Workspaces - Immersion

  * Noise cancelling headphones and the headset provide a completely disconnected environment free of distraction.

  * Pro-tip: Match the environment to your soundscape to enhance your experience.
  
  * If you must, use messaging applications to stay connected to the outside world.

--- 

<!-- 
Notes: 
  
  * Here for example is the landscape of Corsica as my vr environment

-->

![bg center](../media/corsica.jpg)

--- 

<!-- 
Notes: 
  
  * Here is me working in the streets of Korea

-->

![bg center](../media/korea1.jpg)

--- 

<!-- 
Notes: 
  
  * And at it's temples

-->
![bg center](../media/korea2.jpg)

--- 

<!-- 
Notes: 
  
  * And if you're really wild you can even work in your own home away from home.
  * cue bizarre cat photo again.
  * And so if you want to try out a virtual workspace here's what I have for my Personal set-up

-->

![bg center](../media/office_ex.jpg)

--- 

<!-- 
Notes: 
  
  * My personal setup is really everything you see in the photo there plus a set of noise cancelling headphones.

  * MacOS is nice as it supports Virtual monitors out of the box.

  * I have also ran Linux but it does require a lot of configuration to use virtual monitors.

  * If you're using Windows, it has an equivalent experience as MacOS.

  * For free software I use includes a Quest 2 App known as Immersed, which is a very fitting name, as well as adb (the Android debug bridge for those unfamiliar) which provides a lot of extra features as the Quest 2 is an Android device.

  * Specifically some of the features you get with adb are the tethering to reduce latency. For myself I get a shell in a modern Android environment, and you also get speedy file transfers between the headset and computer.

  * And now I use Frame VR for presenting!

  * Alright, I've now gone through Deep work, Virtual Workspaces, and how I set mine up; lets dive into some of these environments in practice.
  
-->


![bg right:44%](../media/setup.jpeg)

# Personal Setup

  * Quest 2 + Macbook + headphones
    * Ubuntu/Linux supported but lacking in certain features.
      * It does seem possible to use `xrandr` to fake out virtual monitors.
    * Windows has a similar support level as macOS.

  * Immersed + adb (Android Debug Bridge)
    * The Quest 2 is an Android device adb provides enhanced features
      * Tethering for reduced latency, a modern Android environment, speedy file transfers
    * And now Frame VR for presenting!

---

<!-- 
Notes: 
  
  * I've compiled two examples to illustrate real situations I've found myself in that benefitted from my virtual workspace. In these examples I was traveling and only had my laptop and headset and so I'm really comparing working from only a laptop with my vr workspace.

  * As I said before the examples I'm going to work through are specifically debugging with LLDB and Voltron, as well as reverse engineering with Ghidra and the Ghidra extension Dragon Dance.

  * Both of these examples are documented on my blog and you're welcome to learn more about it there!

  * Finally, since the Quest 2 is a mobile device effectively, I'll be using Quest 2 native libraries as a target in both examples.

  * Specifically we'll be looking at the Meta developed libosutils and the benign getProcessName function that takes a pid and returns a string.

  * If you want to attempt this later on your own time there is some C code on my blog to compile a binary that calls this native libraries function.

  * So lets start with the Debugging example.
-->

![bg right:33% width:60%](../media/android.png)

# Virtual Workspaces in Practice

* A few examples to illustrate situations that benefit from virtual workspaces
  * Comparing laptop vs virtual workspace

* These examples are documented on my blog:
  * [LLDB + Voltron](https://datalocaltmp.github.io/debugging-android-with-lldb.html)
  * [Ghidra + Dragon Dance](https://datalocaltmp.github.io/visualizing-android-code-coverage-pt-1.html)

* Because I like mobile security and the Quest 2 is an Android device...
  * Let's debug some Quest 2 native libraries
  * `libosutils.so` -> `getProcessName(int pid)`
  * C code to build toy program [here](https://datalocaltmp.github.io/visualizing-android-code-coverage-pt-2.html)

---

<!-- 
Notes: 
  
  * So for those of you that are familiar with GDB. GEF is the GDB Enhanced Features extension. It's comparable to Voltron which enhances the features of LLDB.

  * FoundryZero has just create a true recreation of GEF for LLDB and it's appropriately named LLEF and I document using on my blog here if you want to learn more about that.

  * And the reason I've moved to LLDB is due to the Android NDK shifting support from GDB to LLDB; GDB support is being deprecated in the latest Android NDKS and I figured it was time to learn a new tool.

  * So now you know what and why of LLDB; lets quickly say when to use it; And that's when we experience a binary that is crashing and perhaps we have a stacktrace but we don't really know more than that.

  * The solution to this problem is generally attaching a debugger and stepping through it's execution.

  * And in generally that looks like this on a laptop.

-->

# Example: Debugging with LLDB + Voltron 

  * GEF is to GDB as Voltron is to LLDB
    * FoundryZero has just created LLEF and my comparison is documented [here](https://datalocaltmp.github.io/debugging-android-with-leff.html)

  * Recently moved to LLDB from GDB due to Android NDK support shift
    * This is documented in the "[Using Debuggers](https://source.android.com/docs/core/tests/debug/gdb)" Android documentation

  * Problem: We have a binary but don't know why it's crashing.
    * Solution: Attach LLDB and start your analysis

---

<!-- 
Notes: 
  * So on our single screen we have a ton of condensed information
    * At the top is the LLDB interpreter
    * Below that is the Stack
    * Below that is the Registers and the Disassembly
    * Finally we have the back trace

  * It's fairly obvious, but the terminal at this point is at it's max for conveying information; it would be hard to visualize more information without additional windows that you'd have to switch between.

  * And this is only showing what it's like to visualize the information let alone taking notes on what you're finding.

  * So what does this look like in a virtual workspace...
-->

![center height:50%](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/voltron-use.webp)

---

<!-- 
Notes: 
  * In a virtual workspace we get to configure virtual monitors any way we'd like.

  * This comes at the cost of course of using a vr headset, but the value it provides I believe is worth it.

  * On the far right monitor we have:
    * the stack visible on top, followed by two hexdumps of interesting memory locations
    
  * On the middle monitor we have:
    * the same LLDB interpreter
    * the registers and disassembled binary

  * And finally on the far left monitor we have our notes capturing any findings

  * Personally, I find that this experience really prevents having to change windows which can sometimes be equivalent to context switching itself.

  * So this was an example of debugging; lets look at Reverse Engineering.
-->


![center](../media/lldb_vr.webp)

---


<!-- 
Notes: 
  * 
-->


# Example: SRE with Ghidra + DragonDance

  * Have a LLDB session but don't know where to place break points or how to approach the analysis.

  * Open your decompiler of choice and begin SRE, or ...

  * Use a tool like Lighthouse to generate coverage maps (the executed code within the binary) and use Dragon Dance to visualize execution in Ghidra's function graph!

---

![center](../media/example.webp)

---

![center](../media/dd_vr.webp)

---

![bg right:33% width:50%](../media/unity_logo.png)

# Future Virtual Workspaces

* Unity gives you the tools to build your own workspaces!

* Custom built to suit your needs and doesn't need to just be more monitors

* Large learning curve and I must admit I haven't gotten to this point.

---

![bg center width:80%](../media/unity_dev.png)

---

![center](../media/final.webp)

---

# Parting Thoughts

* When work-from-home became the standard, it was nice to be able to work outside inside.

* This isn't for everyone of course
  * Eye-fatigue, "VR legs", virtual monitors become computationally intensive
  * You're just the folks that are already in VR so...

* Remember that Deep work doesn't need to be long, it just needs to be meaningful

---

# Questions?

---

# Thanks!

![center width:800](../media/wave.webp)

