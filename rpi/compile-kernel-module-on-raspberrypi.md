# Compiling a kernel module on the Raspberry Pi

Here I'll describe the steps needed to download the right kernel sources and setup your Raspberry Pi so you can compile your own kernel module.

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
