# OpenPose : Realtime Multi-person Pose Estimation using PAFs [2020]
[openpose](https://github.com/CMU-Perceptual-Computing-Lab/openpose) represents the first real-time multi-person system to jointly detect human body, hand, facial, and foot keypoints (in total 135 keypoints) on single images. This project was deployed on a Jetson Nano card (NVIDIA). But, the same steps can be followed to deploy the project on a laptop.
## Outline :
- System Configuration
- Installing Prerequisites
- Building OpenPose
- Running OpenPose
- Resources
## 1.System Configuration :
#### 1.1.Increasing Swap Size :
This step is highly recommended if you're facing errors caused by lack of RAM. If it's not the case, you can skip this step.
First we have to turn off all swap processes :
```sh
sudo swapoff -a
```
Now, we will define the swap size:
```sh
sudo dd if=/dev/zero of=/swapfile bs=1G count=3
```
In my case, I chose the block size (bs) to be equal to 1Gb and the number of blocks(count) to be equal to 3. So, the swap size is defined to be 3Gb. You can define those 2 attributes as you like.
Then, we have to change the permissions of the swapfile :
```sh
sudo chmod 600 /swapfile
```
Make the file usable as swap: 
 ```sh
sudo mkswap /swapfile
```
Then, activate the swapfile :
```sh
sudo swapon /swapfile
```
Open the file /etc/fstab: 
```sh
sudo gedit /etc/fstab
```
and add the new swapfile if it isn’t already there :
```sh
/swapfile none swap sw 0 0
```
To be sure that everything went right, run this command to check the swap :
```sh
grep SwapTotal /proc/meminfo
```
#### 1.2.Anaconda :
Anaconda should NOT be installed on your system because it uses a protobuf version that is not compatible with Caffe. So, we have to remove Anaconda and all its files :
```sh
rm -rf anaconda3/
```
#### 1.3.Install CUDA 10.0 :
First, we have to download the .deb file from this [Link](https://developer.nvidia.com/cuda-10.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal). Then, run these commands :
```sh
cd Downloads
sudo dpkg -i cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64.deb
sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda
```
Now, we open the bashrc file :
```sh
sudo gedit ~/.bashrc
```
Then, we add these lines at the bottom of the file :
```sh
# Add this to your .bashrc file
export CUDA_HOME=/usr/local/cuda
# Adds the CUDA compiler to the PATH
export PATH=$CUDA_HOME/bin:$PATH
# Adds the libraries
export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
```
To make sure that there's no errors occured, type these commands :
```sh
source ~/.bashrc
nvcc --version
```
Now, you should see something like this :
```sh
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2018 NVIDIA Corporation
Built on ...
Cuda compilation tools, release 10.0, Vxxxxx
```
#### 1.4.Install CuDNN 7.5 :
First, we have to download CuDNN version 7.5 from this [Link](https://developer.nvidia.com/rdp/cudnn-archive) (CuDNN Library for Linux). Then, unzip it and copy its content in the cuda folder (default /usr/local/cuda/). You can use the cp command to do so :
```sh
sudo cp -r <source_path> <destination_path>
```
## 2.Installing Prerequisites :
#### 2.1.Install Protobuf :
```sh
sudo apt-get install autoconf automake libtool curl make g++ unzip -y
git clone https://github.com/google/protobuf.git
cd protobuf
git submodule update --init --recursive
./autogen.sh
./configure
make
make check
sudo make install
sudo ldconfig
```
#### 2.2.Install pip3 :
```sh
sudo apt update
sudo apt install python3-pip
pip3 –version 
```
#### 2.3.Install Numpy :
```sh
sudo apt-get install python3-numpy
```
#### 2.4.Install Caffe Prerequisites :
```sh
cd path/to/openpose/root/folder
sudo bash ./scripts/ubuntu/install_deps.sh ## Prerequisites
sudo apt install caffe-cuda
```
#### 2.5.Install CMake :
Uninstall the current CMake version :
```sh
sudo apt purge cmake-qt-gui 
```
Install OpenSSL :
```sh
sudo apt install libssl-dev
```
Install the following package :
```sh
sudo apt-get install qtbase5-dev
```
Download the latest release of CMake from this [Link](https://cmake.org/download/) (called cmake-X.X.X.tar.gz). Unzip it , go inside that folder and open a terminal :
```sh
./configure –qt-gui   #make sure no errors occurred
./bootstrap && make -j`nproc` && sudo make install -j`nproc`
```
#### 2.6.Install OpenCV :
```sh
sudo apt-get install libopencv-dev
pkg-config --modversion opencv #check the version
```
#### 2.7.Install Atlas :
```sh
sudo apt-get update -y
sudo apt-get install -y libatlas-base-dev
```
## 3.Building OpenPose :
#### 3.1.Clone OpenPose Repository :
```sh
git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose
```
#### 3.2.Clone Caffe Repository :
```sh
cd 3rdparty
git clone https://github.com/CMU-Perceptual-Computing-Lab/caffe.git
```
#### 3.3.Clone Pybind11 Repository :
```sh
git clone https://github.com/pybind/pybind11.git
```
#### 3.4.Models :
The authors of OpenPose provided pretrained models. You can find them in this [Link](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/prerequisites.md). In this demo we will use the [Body_25](http://posefs1.perception.cs.cmu.edu/OpenPose/models/pose/body_25/pose_iter_584000.caffemodel) pretrained model. So, you have to download it then place it into models/pose/body_25.
#### 3.5.Build OpenPose using CMake :
Open CMake , specify the source directory path and the build directory path , then click "configure" (enable BUILD_PYTHON Flag) and finally click "generate". Run the make command in the build directory :
```sh
cd build 
make -j`nproc`
```
## 4.Running OpenPose :
#### 4.1.Running on Videos :
Go to the OpenPose root directory and open a terminal :
```sh
./build/examples/openpose/openpose.bin --video examples/media/video.avi
```
#### 4.2.Running on WebCam :
Go to the OpenPose root directory and open a terminal :
```sh
./build/examples/openpose/openpose.bin
```
## 5.Resources :
[OpenPose official implemetation (Caffe)](https://github.com/CMU-Perceptual-Computing-Lab/openpose)

[Guide for installing OpenPose by Erica Zheng](https://medium.com/@erica.z.zheng/installing-openpose-on-ubuntu-18-04-cuda-10-ebb371cf3442)

[Tutorial for increasing swap size](https://bogdancornianu.com/change-swap-size-in-ubuntu/)

[Commands for installing CUDA 10.0](https://developer.nvidia.com/cuda-10.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)

### Contact me:
Email: mohamedrekik1997@gmail.com
Linkedin: https://linkedin.com/in/mohamedrekik
