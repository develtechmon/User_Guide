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

### 1. Install python 2.7 package and PIP
```
sudo apt-get update
sudo apt-get upgrade
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt install python2
python2 --version
```

```
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
sudo python get-pip.py
pip --version
```

Once completed run velow command to to determine if installed pip is refer to python 2.7 using below command
```
whereis pip
```

### 2. Install python 3 pip
```
sudo apt-get update
sudo apt install python3-pip
```

Once completed run python to check if pip installed pip is refer to python 3.10 using below command
```
whereis pip
```

### 3. Install Ardupilot and relevant packages
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install git
sudo pip install pexpect 
git clone https://github.com/ArduPilot/ardupilot

cd ardupilot
git submodule update --init --recursive

./Tools/environment_install/install_prereqs_ubuntu.sh -y
```

### 4. Load the `profile`
```
. ~/.profile (Space in between . and ~ - reload this profile if sim_vehicle not found)
```

### 5. Start the Simulator
```
cd ardupilot/Arducopter
```

### 6. First run to write the virtual EEPROM. After default parameters are loaded then can start simulator normally
```
sim_vehicle.py -w
```

## SITL and Gazebo Connection setup.

### 1. To launch `SITL` environment using internal connection

Open new terminal and run below command
```
cd ~/ardupilot/ArduCopter/
sim_vehicle.py -v ArduCopter -f gazebo-iris --console
```

### 2. To launch `SITL` environment using forwarded addess from Companion Computer

You can also forward RPI or Jetson Nano IP address to connect with SITL using below command
```
sim_vehicle.py -v ArduCopter -f gazebo-iris --console --out 192.168.195.204:14553
```
From companion computer run `connect_drone.py` script from Drone_kit repository to test the connection. Before run, please use below connection_string which is referring to RPI IP address
```
connection_string = '192.168.195.204:14553'
```

The script should output `virtual copter is ready`



