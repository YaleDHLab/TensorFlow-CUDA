#BIOS
Configure for Discrete, not Hybrid GPU.

# Base OS
Start with Ubuntu [14.04 LTS](http://releases.ubuntu.com/14.04/).  Newer releases have  more recent versions of GCC and other tools, which requires a whole bunch of hacks to get TensorFlow and its constituent elements compiled correctly -- it's basically not worth it.

##Edit GRUB install params
On first boot from the installer, highlight the "Install Ubuntu" line but hit `E` to edit the command.
Suffix `nouveau.modeset=0` to the very end of the line that begins `linux`.
Press `F10` to comtinue.


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

Download CUDA 7.5 from [https://developer.nvidia.com/cuda-downloads].

Choose **Linux** > **x86_64** > **Ubuntu** > **14.04** > **deb (local)**

```
 sudo dpkg -i <filename>
 sudo apt update
 sudo apt install cuda
```
##Post-installation
Add to `.bashrc`:

```
export PATH=/usr/local/cuda-7.5/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-7.5/lib64:$LD_LIBRARY_PATH
```

##Fix broken Unity (X server)
Reboot machine via GUI. X will be broken.
Hit Control-Alt-F1 to get console. Login.
sudo apt-get remove --purge nvidia-*
sudo apt-get remove --purge xserver-xorg-video-nouveau xserver-xorg-video-nv
#CUDAnn

[https://developer.nvidia.com/cudnn]

Make sure you get version 4.
Move the files into `/usr/local/cuda/include` and `/usr/local/cuda/lib64`.
Set permissions correctly.
`sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*`

#TensorFlow
Existing binary builds of TensorFlow have very bad memory management when run on GPUs (which is the whole point here.) So we'll need to build TensorFlow from source.


##Bazel
First we'll need JDK8:
###JDK8
```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```
###Bazel package source
```
echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list
curl https://storage.googleapis.com/bazel-apt/doc/apt-key.pub.gpg | sudo apt-key add -
```
###Install bazel
The latest installer should be [here](https://github.com/bazelbuild/bazel/releases), ie `bazel-0.3.1-installer-linux-x86_64.sh`.

```
chmod +x bazel-0.3.1-installer-linux-x86_64.sh
./bazel-0.3.1-installer-linux-x86_64.sh --user
```
Remember to add bazel to your path as per instructions.

###Bazel-specific python dependencies

`sudo apt-get install python-numpy swig python-dev python-wheel`

##Compiling TensorFlow

```
git clone https://github.com/tensorflow/tensorflow
cd tensorflow
./configure
```
Default location of pyton is ok.
No to Google Cloud Platform
YES to GUP
default gcc
Declare Cuda to be 7.5
declare CUDNN to be 4.

##Compilation
`bazel build -c opt --config=cuda //tensorflow/cc:tutorials_example_trainer`
NB if your path is not configured, `/home/pleonard/bin/bazel build`...

###Compliation time
24 minutes on a octacore XEON with Samsung 950 SSD

#Monitor GPU usage
`watch -n 1 nvidia-smi`
