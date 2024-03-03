# Getting Started

## This useguide provide a comprehensive explaination on how to install `Gazebo` and `STL` package used for drone Simulation

In my case, i've installed `ros2-humble` gazebo package. Please refer to my `ROS` Git and `Userguide` on how to install ROS packages.  I assume you've install
Gazebo in your ros installation.


# ✅ Gazebo Arducopter Plugin Installation
### 1. Install Arducopter Plugin For Gazebo

Run below command and execute it by sequence.
```
git clone https://github.com/khancyr/ardupilot_gazebo --> Original one without Aruco file.
git clone  https://github.com/dronedojo/ardupilot_gazebo.git --> From drone dojo with Aruco
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
## Important !
if you copy following file from `drone dojo` GIThub
```
model
world
media
```
Please ensure to add following line to enable the `sudo make install` copy these directories automatically into `/usr/share/gazebo-11/media`
```
install(DIRECTORY models DESTINATION ${GAZEBO_MODEL_PATH}/..)
install(DIRECTORY worlds DESTINATION ${GAZEBO_MODEL_PATH}/..)
install(DIRECTORY media  DESTINATION ${GAZEBO_MODEL_PATH}/..) <-------- Add media
```
Remember, to copy only the new files, don't overwrite the same file `name` that reside in the existing directory. For example,
if `drone dojo` already have `iris_with_standoffs`. Don't copy this file into your existing `world`

### 2. Run `Gazebo` Arducopter world

After you complete with installation above, run following command to invoke our `arducopter world` which will be used in the simulation
```
gazebo --verbose ~/ardupilot_gazebo/worlds/iris_arducopter_runway.world 
```

# ✅ SITL Arducopter Installation
## 1. For my setup, i'm using below spec:

My Ubuntu Version
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
python version
```
Python 3.10.12
```

### 2. Install python 2.7 package and PIP
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

### 3. Install python 3 pip
```
sudo apt-get update
sudo apt install python3-pip
```

Once completed run python to check if pip installed pip is refer to python 3.10 using below command
```
whereis pip
```

### 4. Install Ardupilot and relevant packages
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install git
sudo pip install pexpect
sudo pip install future
sudo pip install pyserial
sudo pip install dronekit
sudo pip install MAVProxy
sudo pip install keyboard

git clone https://github.com/ArduPilot/ardupilot

cd ardupilot
git submodule update --init --recursive

./Tools/environment_install/install_prereqs_ubuntu.sh -y
```

### 5. Load the `profile`
```
. ~/.profile (Space in between . and ~ - reload this profile if sim_vehicle not found)
```

### 6. Start the Simulator
```
cd ardupilot/Arducopter
```

### 7. First run to write the virtual EEPROM. After default parameters are loaded then can start simulator normally
```
sim_vehicle.py -w
```

# ✅ SITL and Gazebo Connection setup.

### 1. To launch `SITL` environment using internal connection

To test if connection is working. Run below command using `Mavproxy`. You should see `hearbeat` data from Flight Controller.
Here i'm using internal `IP adress` that communicate internally.

## Using Internal IP address
```
mavproxy.py --master=127.0.0.1:14550
```

Open new terminal and run below command
```
cd ~/ardupilot/ArduCopter/
sim_vehicle.py -v ArduCopter -f gazebo-iris --console
```

### 2. To launch `SITL` environment using forwarded addess from Companion Computer

## Using IP address
To test if connection is working. Run below command using `Mavproxy`. You should see `hearbeat` data from Flight Controller.
Here i'm using internal `Companion IP address` that communicate externally.

## Using Companion Computer IP address
```
mavproxy.py --master=192.168.195.204:14553
```

## Using UART
```
mavproxy.py --master=/dev/ttyUSB0,57600
```

To forward RPI or Jetson Nano IP address to connect with SITL use below command
```
sim_vehicle.py -v ArduCopter -f gazebo-iris --console --out 192.168.195.204:14553
```

From companion computer run `connect_drone.py` script from Drone_kit repository to test the connection. Before run, please use below connection_string which is referring to RPI IP address
```
connection_string = '192.168.195.204:14553'
```

The script should output `virtual copter is ready`

# ✅ Run our `Drone kit` script
In this example, we're going to test our python3 script to control our drone in `Gazebo` simulation


## Step 1. Clone below packages
```
git clone https://github.com/develtechmon/Dronekit_Projects.git
```

## Step 2. Go to following directory and edit the config
```
cd Dronekit_Projects/
cd Keyboard_Wireless_drone_with_RPI/
```

Open `send_keyboard_data_to_RPI.py` and edit following file
```
'''RPI'''
#self.connection_string = '/dev/ttyAMA0,921600'

'''Gazebo'''
#self.connection_string = '192.168.8.140:14553'

'''SiTL'''
self.connection_string = '127.0.0.1:14550' <------------- Enable this line because we're using internal SITL connection

'''ZeroTier'''
#self.connection_string = '192.168.8.141:14553'

#self.buzzer  = Buzzer(23)
self.vehicle = connect(self.connection_string, wait_ready=True)
```

and `disable` all the buzzer relevant line in both scripts file

### Step 3. Problem !

When you run below script
```
sudo python3 send_keyboard_data_to_RPI.py 
```

You will see following error in `dronekit` packages
```
AttributeError: module `collections` has no attribute `MutableMapping`.
```

To solve this problem go to following line and change the name respectively as follow
```
sudo vi /usr/local/lib/python3.10/dist-packages/dronekit/__init__.py 
```

Go to line `2690`. Change following line as follow. Here we change to `collections.abc.MutableMapping`
```
      #class Parameters(collections.MutableMapping, HasObservers): <----------- old
2690 class Parameters(collections.abc.MutableMapping, HasObservers): <------------- new
```
save and quit !

I refer to this link to solve the problem
```
https://itsourcecode.com/attributeerror/module-collections-has-no-attribute-mutablemapping/
```

### Step 4. At this point you should be able to run the script
```
sudo python3 send_keyboard_data_to_RPI.py 
```


