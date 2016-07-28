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

`sudo apt install freeglut3-dev build-essential libxmu-dev libxi-dev`

`sudo apt install libc6:i386`

`sudo apt install libgl1-mesa-glx-lts-trusty:i386`


