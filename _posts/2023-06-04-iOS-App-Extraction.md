There are many reasons you may want to extract iOS applications; one in particular is reviewing security and privacy aspects with an analysis tool such as Ghidra. Unfortunately, unlike .apk files for Android, .ipa files cannot be side-loaded very easily; this has led to a smaller mirroring community.

With a lack of .ipa mirrors it'll be up to the individual to extract the artifacts for analysis. This write-up is meant to be a quick tutorial on how to do just that and what to expect.

## Pre-requisites
Before getting started it's best to have the following:
* a host computer with `frida-tools`
	* clone [frida-ios-dump](https://github.com/AloneMonkey/frida-ios-dump) and pip install it's requirements.txt
* a jailbroken iOS device (this tutorial uses an iPhone 6s running iOS 14.4.2),
	* `frida-server` on iOS using the detailed instructions [Here](https://frida.re/docs/ios/#with-jailbreak)

## Setup
Once your device is jailbroken, connect it to your local network and note the ip address, then access the device via SSH at the noted ip address with `root` user and the default password `alpine`.

Assuming that `frida-server` has installed successfully, run:

`frida-server -l <ip_address>`

## Dumping .ipa Files
For this tutorial we will extract Safari as an example, however it will work for any application installed. While it's possible to extract the .ipa file with the applications name, I find it best practice to use the bundle identifier.

In order to find the bundle identifier for an application, enter the `frida-ios-dump` directory and run:

`python3 dump.py -H <iPhone_ip_address> -p <ssh_port> -l`

The image below shows example output for this command, note that the bundle identifier is `com.apple.mobilesafari`.

![bundleids](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/bundleids.png)

Then we can proceed to use this bundle identifier to extract the application. Use the following command to produce an .ipa file for Safari:

`python3 dump.py -H <iPhone_ip_address> -p <ssh_port> com.apple.mobilesafari`

The image below is example output; the `Safari.ipa` file is a valid .zip file and can be extracted in order to access the binaries for analysis.

![bundledump](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/bundledump.png)

## Analyzing .ipa Binaries

Once the .ipa file is unzipped, there will be a binary files that has the same name as the application which contains much of the application logic. In the instance of Safari a few of these files are:

* `Payload/MobileSafari.app/PlugIns/Safari.wkbundle/Safari`
* `Payload/MobileSafari.app/MobileSafari`

There are many other files in the zip, however finding all the goodies in those is left as an exercise for the reader.

![ghidraimport](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/ghidraimport.png)

From there you can import the file binaries into your analysis tool of choice and begin your iOS RE adventure (after you have another coffee of course).

![ghidradecompile](https://raw.githubusercontent.com/datalocaltmp/datalocaltmp.github.io/main/_posts/ghidradecompile.png)
