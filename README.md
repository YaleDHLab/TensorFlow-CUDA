# Base OS
Start with Ubuntu [14.04 LTS](http://releases.ubuntu.com/14.04/).  Newer versions of Ubuntu have much more recent versions of GCC and other tools.  This requires a whole bunch of hacks to get TensorFlow and its constituent elements compiled correctly -- it's basically not worth it.

During installation, do NOT check the “Install this third-party software,” which will add an NVIDIA binary  driver. We want to use the one from the CUDA installer, so there’s no need to confuse things.

# Post-installation OS setup

`sudo apt install tcsh`

##For HiDPI/Retina screens

`sudo dpkg-reconfigure console-setup`

Accept all the defaults till you get to the Fonts screen. Optionally change the font to Terminus, but be sure to set the size to 32x16 to ensure readability on the console. This is important because several steps have to be done with X/lightdm disabled.

##CUDA Samples Dependencies
There are a few tricky dependencies for some of the dependencies in CUDA. These may only apply to the Samples, but for completeness' sake:

```
sudo apt install freeglut3-dev build-essential libxmu-dev libxi-dev
sudo apt install libc6:i386
sudo apt install libgl1-mesa-glx-lts-trusty:i386
```

##Python

`sudo apt install python-pip python-dev`

#CUDA Toolkit

The magic steps to avoid GUI lockout when using binary NVIDIA display drivers were cribbed form [this thread](https://devtalk.nvidia.com/default/topic/878117/-solved-titan-x-for-cuda-7-5-login-loop-error-ubuntu-14-04-/
).

##Blacklisting the non-NVIDIA driver

We need to prevent the open, non-NVIDIA driver from contending control of hardware that the binary blob will now handle. Create a new file:

`sudo nano /etc/modprobe.d/blacklist-nouveau.conf`
Add two lines:
```
blacklist nouveau
options nouveau modeset=0
```

Now apply these changes to the RAM file system that is used as an intital root file system:

`sudo update-initramfs -u`

