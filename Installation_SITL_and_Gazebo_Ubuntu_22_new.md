# Getting Started

## This useguide provide a comprehensive explaination on how to install `Gazebo` and `STL` package used for drone Simulation

In my case, i've installed `ros2-humble` gazebo package. Please refer to my `ROS` Git and `Userguide` on how to install ROS packages.  I assume you've install
Gazebo in your ros installation.


## Gazebo Arducopter Plugin Installation
### 1. Install Arducopter Plugin For Gazebo

Run below command and execute it by sequence.
```
git clone https://github.com/khancyr/ardupilot_gazebo
cd ardupilot_gazebo
mkdir build
cd build
cmake ..
make -j4
sudo make install
echo 'source /usr/share/gazebo/setup.sh' >> ~/.bashrc
echo 'export GAZEBO_MODEL_PATH=~/ardupilot_gazebo/models' >> ~/.bashrc
. ~/.bashrc
```

### 2. Run `Gazebo` Arducopter world

After you complete with installation above, run following command to invoke our `arducopter world` which will be used in the simulation
```
gazebo --verbose ~/ardupilot_gazebo/worlds/iris_arducopter_runway.world 
```

## SITL Arducopter Installation

For my setup, i'm using below spec:

* My Ubuntu Version
  ```
  lsb_release -a
  ```
  ```
  No LSB modules are available.
  Distributor ID:	Ubuntu
  Description:	Ubuntu 22.04.4 LTS
  Release:	22.04
  Codename:	jammy
  ```
* python version
  ```
  Python 3.10.12
  ```
