# YahboomJetsonNanoHelp
A detailed guide for total beginners to help them overcome the limitations of the Jetson Nano 4GB boot with USB Board
I spent 8 weeks trying to get the Jetson Nano optimised.  It is not an easy AI for beginners.  The Jetson Nano Tutorials by Paul McWhorter are brilliant but you end up running out of memory and space for everthing because the Jetson Nano is so limited out of the box.
These are my personal notes that I put together that got me to a point where the Jetson was performing well enough to continue the Paul McWhorter tutorials and build something meaningful with the bot.
Please don't judge me I am a total newbie that stuggled like mad to salvage this purchase.  I was ready to throw my Jetson Nano in the dustbin at a stage.  
Hope this helps:

This is an important document.  It is the holy grail of how I got past the limitation on memory and disk storage.  There are a few things in this document that you must follow in order when prepping the Jetson Nano from scratch  Steps as follows:



**NOTES:  WILL UPLOAD A WORD VERSION THAT IS NEATER.**
•	Buy a 2 x 64GB USB drives.
•	Download Balena Etcher so you can clone the original disk image onto the drive
•	Boot the Jetson Nano with the new flashed image and note only 30GB of storage is allocated on the 64GB USB
•	After booting up and getting onto your network and running the usual SUDO update and upgrade and clean commands and once you know all your software is up to date then;
•	Remove LIBRE office and remove Thunderbird if you wish (not needed anymore because you have space after this exercise
•	I normally remove Chrome and add Firefox as well
•	Then do the following sequentially to create an optimal Jetson Nano
o	SWAP File tutorial to create more RAM
o	Increase space allocation Tutorial to create more space
o	Upgrade Jetpack to 4.4.1 Tutorial
o	Install Visual Studio Code (not Code-OSS this takes too much resources)
o	Install python3 framework in VS Code and python interpreter
o	Install Numpy, MatplotLIB
o	You don’t have to install CV2 anymore as you are running 4.1.1 already
o	Create a back up the USB by cloning it using Balena Etcher OR if you struggle with the size matching of the USB i.e. Balena keeps saying your USB is too small to clone to despite both being 64GB’s then simply use step 3 of the “Increase Space Allocation” tutorial to copy the entire application area over to a new USB.  This only considers the size of the application files when measuring the space requirement and not the size of the USB

SWAP File tutorial to create more RAM
If you have not installed GIT Clone as yet you have to do so now in order to clone GIT repo’s.  Use this command line:
**Sudo apt-get install git**
The GIT repository to install the Swap File Software is located at this link:
**https://github.com/JetsonHacksNano/installSwapfile**
The command line to clone the repo is as follows:
**git clone https://github.com/JetsonHacksNano/installSwapfile.git**
Command to install the swap file and implement the code as follows:
**cd installSwapfile
./installSwapfile.sh
sudo reboot now**

Refer to this tutorial for more instructions:
**https://jetsonhacks.com/2019/04/14/jetson-nano-use-more-memory/**

Increase space allocation Tutorial to create more space
The tutorial resides at this link:
**https://github.com/JetsonHacksNano/rootOnUSB.git**
Things you must consider when following the tutorial.  I note them upfront before you begin and as you start down the tutorial you will see where the points fit into the bigger picture:
1.	Keep track of what USB has what directory.  In my example the new USB I was writing to was a second USB on the Nano (because I don’t boot from SD Card like some models I have the 4GB B01 board) and as such the file path after format is /dev/sdb1.  Its important to not this because you change the root boot folder in this tutorial and this context matters
2.	This is only to note if you specify the boot sequence using the UUID else ignore.  The tutorial uses the path which correct for being able to swap USB’s between backup or not.  It will make sense as you go through the tutorial.  I followed the tutorial 100% so this note I am making is for any future user who does it with the UUID.  Keep track to the UUID for the new USB you want to use to boot up with in the future because you once again specify the physical boot up device to consider when you modify the boot folder by specifying the UUID.  Important to remember is that because you tell the boot folder to boot from a very specific piece of hardware in future if this corrupts you will have to recover first by starting with a fresh flashed USB and then booting into Ubuntu and following this tutorial again.  You can go straight to the step where you modify the boot folder to point to the backup USB you created post all of these setup steps were done.

Clone the software from this repo:
**git clone https://github.com/JetsonHacksNano/rootOnUSB.git**

Change directories to get into the repo with CD command
**cd rootOnUSB**
Watch the tutorial for the remaining steps:
**https://www.youtube.com/watch?v=J9EJ52Za7IE**
Below is the README steps described from GIT Hub
rootOnUSB
Set rootfs to be on a USB drive
**Original article on JetsonHacks: https://wp.me/p7ZgI9-317**
WARNING: This is a low level system change. You may have issues which are not easily solved. You should do this working on a freshly flashed micro SD card, and certainly do not attempt this with valuable data on the card itself. Assume that if this does not work, you may have to flash the micro SD card again. A serial debug console is useful if things go wrong.
The scripts in this repository will setup a NVIDIA Jetson Nano Developer Kit to set the rootfs to a USB drive. This involves four steps. NOTE: This procedure is significantly different than the previous release of this repository. This version does not require the kernel to be recompiled, saving about 40 minutes of install time.
Step 1
Build the initramfs with USB support, so that USB is available early in the boot process. A convenience script named addUSBToInitramfs.sh provides this functionality.
$ ./addUSBToInitramfs.sh
Step 2
The second step does not have representation here. The user must prepare a USB drive (preferably USB 3.0, SSD, HDD, or SATA->USB) by formatting the disk as ext4 with a partition. It is easier if you only plug in one USB drive during this procedure. When finished, the disk should show as /dev/sda1 or similar. Note: Make sure that the partition is ext4, as NTSF will appear to copy correctly but cause issues later on. Typically it is easiest to set the volume label for later use during this process.
Step 3
Copy the application area of the micro SD card to the USB drive. copyRootToUSB.sh copies the contents of the entire system micro SD card to the USB drive. Naturally, the USB drive storage should be larger than the micro SD card. Note: Make sure that the USB drive is mounted before running the script. In order to copyRootToUSB:
usage: ./copyRootToUSB.sh [OPTIONS]

  -d | --directory     Directory path to parent of kernel

  -v | --volume_label  Label of Volume to lookup

  -p | --path          Device Path to USB drive (e.g. /dev/sda1)

  -h | --help  This message
Step 4
Modify the /boot/extlinux/extlinux.conf file. An entry should be added to point to the new rootfs (typically this is /dev/sda1). There is a sample configuration file: sample-extlinux.conf
You should make a backup of the original extlinux.conf file. Also, when you edit the file you should make a backup of the original configuration and relabel the backup. This will allow you to access an alternate boot method from the serial console in case something goes sideways.
Then you should changed the INITRD line to:
INITRD /boot/initrd-xusb.img
So that the system uses the initramfs that we built that includes the USB firmware. Then set the root to the USB drive.
Here are some examples. You can set the drive by the UUID of the disk drive, the volume label of the drive, or the device path:
APPEND ${cbootargs} root=UUID=0e437280-bea0-42a2-967f-a240dd3075eb rootwait rootfstype=ext4
APPEND ${cbootargs} root=LABEL=JetsonNanoSSD500 rootwait rootfstype=ext4
APPEND ${cbootargs} root=/dev/sda1 rootwait rootfstype=ext4
The first entry is most specific, the last most generic. Note that you are not guaranteed that a USB device is enumerated in a certain order and will always have the same device path. That is, if you leave another USB drive plugged in along with your root disk when you boot the Jetson, the root disk may have a different path than originally, such as /dev/sdb1.
Also, there is a convenience file: diskUUID.sh which will determine the UUID of a given device. This is useful for example to determine the UUID of the USB drive. Note: If the UUID returned is not similar in length to the above example, then it is likely that the device is not formatted as ext4.
$ ./diskUUID.sh
While this defaults to sda1 (/dev/sda1), you can also determine other drive UUIDs. The /dev/ is assumed, use the -d flag. For example:
$ ./diskUUID.sh -d sdb1
You may find this information useful for setting up the extlinux.conf file

Upgrade Jetpack to 4.4.1
The following steps are described to upgrade Jetpack to any version.  It is important to note however that Ubuntu 18.04 LS can only support up to a certain Jetpack level so do not go beyond that you will corrupt your install as Ubuntu 20.04 will be needed which the Jetson Nano cannot run (well at least not at the time of me doing all this).  Compatibility can be checked at the following link:
https://www.stereolabs.com/blog/nvidia-jetson-l4t-and-jetpack-support/
As reference I followed the steps below but not I could go to 32.6 as the version to modify in nvidia repo per below but have not tried this at the time of making this document.  
The tutorial for this can be found at the following link:
https://code.luasoftware.com/tutorials/jetson-nano/upgrade-jetson-jetpack-to-44
Screen Grabs as follows:
 
 

Install Visual Studio Code (Not Code-OSS)

A note to remember here is after you have installed Visual Studio you must add a python3 framework and the interpreter to it.  Not listing those steps here you can figure it out quite easily the package prompts you on first boot.  The link to install VS Code is found at the following location:
https://github.com/JetsonHacksNano/installVSCode
Clone the following git repo using this command:
git clone https://github.com/JetsonHacksNano/installVSCode.git

The README content as follows:
installVSCode
Shell scripts to install Microsoft Visual Studio Code on ARM 64 machines (e.g. NVIDIA Jetson Developer Kits). There are two scripts here, one which installs Visual Studio Code and another that installs Visual Studio Code with the Python extension enabled.
Install Visual Studio Code
Install with Python support
To install Visual Studio Code on the Jetson, with the Python extension enabled: ``` $ ./installVSCodeWithPython.sh ``` This will install Visual Studio Code and the Python extension. Also, python3-pip, pylint and black will be installed to support linting and file formatting.
Install Visual Studio Code
Installs Visual Studio Code on the Jetson, with no other extensions enabled:
$ ./installVSCode.sh
Running Visual Studio Code
There are two ways to run Visual Studio Code. To run Visual Studio Code after installation open it from the application launcher or open a Terminal and exectute:
$ code

Install Matplot Lib and Numpy
Sudo apt-get install python-matplotlib
Sudo apt-get install python3-numpy
Note here that I think installing Matplot lib first automatically installs Numpy so if you get errors don’t worry you can check the versions that are installed and figure it out.  

Create backup of all the above
Once you have completed all the above run the usual sudo update and upgrade and clean commands to ensure a nice up to date environment.  You will get some warnings on nvidia lt4 but you can ignore them.  They relate to wanting UBUNTU 20.04 to run better with.  Ensure you date and time regions are set to RSA (sorry just me being OCD) and only backup once you know you have done all the little other tweaks you would want done so next time you recover from the backup USB you don’t have to start from ground zero again.

Good luck hope this helps.cd
