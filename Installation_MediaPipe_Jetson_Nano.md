# Getting Started

## This userguide explain how to install Media Pip from Source into Jetson Nano

1. The starting point is a flashed new SD card with Jetpack 4.6

2. Next we need to increase swp for more swap ram using below command
```
git clone https://github.com/JetsonHacksNano/installSwapfile.git  
cd installSwapfile

./installSwapfile.sh
```

3. Setup - pre install which include Python and Pip
```
sudo apt update
sudo apt-get update
sudo apt-get install python3-pip
sudo pip3 install -U pip testresources setuptools==49.6.0
```

4. Python libraries for Tensorflow
```
sudo apt-get install libhdf5-serial-dev hdf5-tools libhdf5-dev zlib1g-dev zip libjpeg8-dev liblapack-dev libblas-dev gfortran
```

5. Install more Python libraries
```
sudo pip3 install -U --no-deps numpy==1.19.4 future==0.18.2 mock==3.0.5 keras_preprocessing==1.1.2 keras_applications==1.0.8 gast==0.4.0 protobuf pybind11 cython pkgconfig
```

6. part of Numpy -> store large amount of data in binary format
```
sudo env H5PY_SETUP_REQUIRES=0 pip3 install -U h5py==3.1.0
sudo apt-get install python3-opencv
```

7. Install media pipe from the source code
```
git clone https://github.com/google/mediapipe.git
cd mediapipe
sudo apt-get install -y libopencv-core-dev  libopencv-highgui-dev libopencv-calib3d-dev libopencv-features2d-dev libopencv-imgproc-dev libopencv-video-dev
```

8. set permissions for the setup script file
```
sudo chmod 744 setup_opencv.sh
```

9. Run installation from source code : about 30 minutes
```
./setup_opencv.sh
```

10. Final Step
```
sudo pip3 install opencv_contrib_python
git clone https://github.com/PINTO0309/mediapipe-bin
cd mediapipe-bin

sudo apt install curl

./v0.8.5/numpy119x/mediapipe-0.8.5_cuda102-cp36-cp36m-linux_aarch64_numpy119x_jetsonnano_L4T32.5.1_download.sh

sudo pip3 install numpy-1.19.4-cp36-none-manylinux2014_aarch64.whl
sudo pip3 install mediapipe-0.8.5_cuda102-cp36-none-linux_aarch64.whl
pip3 install dataclasses
```

11. Download example here
```
https://github.com/feitgemel/BodyPos/tree/master/MediaPipe/Demo

Another example
git clone https://github.com/feitgemel/BodyPos.git
cd BodyPos
cd MediaPipe
cd Demo
python3 MediaPipe-Holistic.py
```
