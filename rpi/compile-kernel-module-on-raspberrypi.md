# Compiling a kernel module on the Raspberry Pi

Here I'll describe the steps needed to get the right kernel sources and setup your Raspberry Pi so you can compile your own kernel modules.
In this article you'll find a number of commands and they are prefixed by '$' or '#'. You don't type those characters, but it's an indication as whom you need to execute it. The '$' character means to execute it as a normal user, while '#' means to execute it as root or with sudo.  
No matter what part of this article is applicable to you, there are a number of programs you need to have installed. You get them through
```
# apt-get install build-essential
```

## Compiling a module for the raspbian.org kernel


## Compiling a module for the raspberrypi.org kernel

### Step 1: Get the git_hash from the firmware

How you get the firmware commit hash depends on how you got your (updated) firmware.  

If you're using the `raspberrypi-bootloader` package (as you should), you can get the relevant git commit id with the following command:
```
zgrep '* firmware as of' /usr/share/doc/raspberrypi-bootloader/changelog.Debian.gz | head -1
```
For the 1.20150421-1 release of `raspberrypi-bootloader` package, this would give you  
`* firmware as of 6cc27e02dde4f1e02cfddb3d0f23d54be87680cb`  
With that git commit id (short version is `6cc27e0`) in mind, you go to https://github.com/raspberrypi/firmware/commits/master and look up that commit id and click on the link with the title _Browse the repository at this point in the history_ and then you go into the `extra` folder.
When you're there, you should see a file named `git_hash`. Open that file and write down the git commit id you see there.  
Also download the `Module.symvers` and `Module7.symvers` files.

If you're using `rpi-update`, the procedure is a bit different.  
When you do `cat /boot/.firmware_revision` you directly see a commit id.  
With that git commit id in mind, you go to https://github.com/Hexxeh/rpi-firmware/commits/master (this is a **different** repository!) and look up that commit id and click on the link with the title _Browse the repository at this point in the history_.
There you'll directly see a file named `git_hash`. Open that file and write down the git commit id you see there.  
Also download the `Module.symvers` and `Module7.symvers` files.

### Step 2: Prepare the kernel for module compilation

Normally you would only need the kernel headers package installed on a Debian based system to compile a kernel module, but the kernels from the Raspberry Pi Foundation (rpf) don't have that. So in this case we need the full kernel source. Luckily you can find them on the rpf github pages, namely at https://github.com/raspberrypi/linux.
You get the kernel source by cloning their git repository, like this:
```
$ git clone https://github.com/raspberrypi/linux rpf-linux-kernel
```
This will put the rpf linux repository into the `rpf-linux-kernel` subdirectory of your current directory. Feel free to do other things while it downloads 1.2GB+ of data and then processes it. In some examples I've seen the use of the `--depth=XXX` parameter to `git clone`, but then you need to know how many commits you need to go back, which is not easy to determine. On 2015-07-10 there were more then 25000 commits since `bb6b4b6b331680bed807605685572d727638bb51`.  

Downloading the kernel source alone is not sufficient to compile a kernel module. There are a couple of things we need to get the kernel source at the time of the kernel compilation and do some other preparations before we can compile a kernel module.  
Below I'll show you the steps for the earlier mentioned release of the `raspberrypi-bootloader` package.

```
$ cd rpf-linux-kernel
$ git checkout -b rpi-bootloader-20150421-1 bb6b4b6b33
```
The key point here is to check out the source at the commit from which we looked up the id before. Since I like to do all modifications/experimentations in a new branch, I've checked the source out into a new branch (`rpi-bootloader-20150421-1`).
If you get an error message saying _fatal: reference is not a tree: <git-hash-found-earlier>_ then you need to start over again and increase the value of `--depth=XXXX` or leave that parameter out alltogether, so that it will contain the git commit we need.
```
$ make mrproper               #clean everything up
$ make bcm2709_defconfig      #for the Raspberry Pi 2
```
If you want to compile a module for the Raspberry Pi 1 kernel, you need to substitute `bcm2709_defconfig` with `bcmrpi_defconfig`.
```
$ make modules_prepare
$ cp ../Module7.symvers Module.symvers  #for the Raspberry Pi 2
```
If instead you're compiling for a Raspberry Pi 1 kernel, you need to do `cp ../Module.symvers Module.symvers`.  
No matter for which version of the Raspberry Pi you'll compile a kernel module, the file needs to be named `Module.symvers` and needs to be in the root directory of the kernel sources.

