# Getting Started

## This userguide explain how to install Gazebo used for Drone simulation in Ubuntu 18

1. To start refer to this [link](https://github.com/punkypankaj/Installing-Gazebo-and-ArduPilot-Plugin/blob/main/Docs.md)
2. Open new terminal and use below command to install `Gazebo`
```
sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
sudo apt update
sudo apt install gazebo9 libgazebo9-dev
gazebo --verbose
```

3. After installation is complete. We will need to install Arducopter Plugin as follow
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

4. To run `Gazebo` environment open new terminal and run below command
```
gazebo --verbose ~/ardupilot_gazebo/worlds/iris_arducopter_runway.world 
```

5. To launch `SITL` environment open new terminal and run below command
```
cd ~/ardupilot/ArduCopter/
sim_vehicle.py -v ArduCopter -f gazebo-iris --console
```

6. You can also forward RPI or Jetson Nano IP address to connect with `SITL` using below command
```
sim_vehicle.py -v ArduCopter -f gazebo-iris --console --out 192.168.195.204:14553
```
7. From companion computer run `connect_drone.py` script from `Drone_kit` repository to test the connection. Before run, please use below connection_string which is referring to RPI IP address
```
connection_string = '192.168.195.204:14553'
```

8. The script should output `virtual copter is ready`
