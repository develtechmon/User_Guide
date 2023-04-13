# Getting Started

## This userguide explain how to install SITL in Ubuntu.

## Jetson Nano

1. Install below packages and follow the subsequent command.
   if you have an issues with PyGame installation. Please refer to user guide "Install_PyGame_JetsonNano" or find forum title "install pygame on Jetson Nano from NVIDIA forum". Follow the guide there

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

2. Then load the profiles
```
. ~/.profile (Space in between . and ~ - reload this profile if sim_vehicle not found)
```

3. Start the simulator
```
cd ardupilot/Arducopter
```

4. First run to write the virtual EEPROM. After default parameters are loaded then can start simulator normally
```
sim_vehicle.py -w
```

5. Below are list of  `sim_vehicle` command to run `SITL` simulator. `console` command should show the `mavproxy` terminal
```
sim_vehicle.py --console --map (To invoke default map and console)
sim_vehicle.py --out=udpout:127.0.0.1:14551 --console --map (to connect into mission planner)
sim_vehicle.py --out=/dev/ttyACM0 (to connect into Pixhawk via USB connection)
sim_vehicle.py --console --map --out 192.168.195.204:14553 (to connect into RPI/Jetson IP address)
```

6. However, if you don't want to use `SITL` simulator but to play around with `Copter` different mode. Please Run `mavproxy` command to verify the successful connection. List of command as follow
```
mavproxy.py --master=/dev/ttyACM0 --out=udp:10.60.216.198 (using RPI/Jetson Nano USB connection)
mavproxy.py --master=/dev/ttyTHS1,921600 --out=udp:10.60.216.198:14550 (using Jetson Nano UART)
mavproxy.py --master=/dev/ttyAMA0,921600 (using RPI UART)

Jetson Nano USB
mavproxy.py --master=/dev/ttyACM0 --baudrate 57600 --out 192.168.137.1:14550 --aircraft MyCopter (Using Jetson Nano and Pixhawak USB and connect it into mission planner)
mavproxy.py --master=/dev/ttyACM0 --baudrate 57600 --aircraft Mycopter                           (Using Jetson Nano and Pixhawk USB without port forwarding)
mavproxy.py --master=/dev/ttyACM0 --baudrate 57600 --out=udp:10.60.216.198:14550                 (Using Jetson Nano and Pixhawk USB and port forward data from Mavproxy (Ground Station) to Copter (IP address). 10.60.** address is from ifconfig of the connected wifi.
mavproxy.py --master=/dev/ttyACM0,921600 --out=udp:10.60.216.198:14550                           (Using Jetson Nano and Pixhawk USB. This is to ignore the "baudrate" statement. This approach will work too)
mavproxy.py --master=/dev/ttyACM0 --out=udp:10.60.216.198:14550                                  (Using Jetson Nano and PIxhawk USB. Baudrate is not required as we have defined default serial rate as SERIAL2 = 57600)

Jetson Nano UART
mavproxy.py --master=/dev/ttyTHS1,921600                                                         (Using Jetson Nano UART and UART of Tx RX of Pixhawk without port forwarding)
mavproxy.py --master=/dev/ttyTHS1,921600 --out=udp:10.60.216.198:14550                           (Using Jetson Nano UART and UART of Tx RX of Pixhawk with port forwarding)

Raspberry PI
mavproxy.py --master=/dev/ttyAMA0 --baudrate 57600 --out 192.168.137.1:14550 --aircraft MyCopter (Using RPI and Pixhawak USB and connect it into mission planner)
mavproxy.py --master=/dev/ttyAMA0 --baudrate 57600 --aircraft Mycopter                           (Using RPI and Pixhawk USB without port forwarding)
mavproxy.py --master=/dev/ttyAMA0 --baudrate 57600 --out=udp:10.60.216.198:14550                 (Using RPI and Pixhawk USB and port forward data from Mavproxy (Ground Station) to Copter (IP address). 10.60.** address is from ifconfig of the connected wifi.
mavproxy.py --master=/dev/ttyAMA0,921600 --out=udp:10.60.216.198:14550                           (Using RPI and Pixhawk USB. This is to ignore the "baudrate" statement. This approach will work too)
mavproxy.py --master=/dev/ttyAMA0 --out=udp:10.60.216.198:14550                                  (Using RPI and PIxhawk USB. Baudrate is not required as we have defined default serial rate as SERIAL2 = 57600)

To connect to remote IP address, the udpout or tcpout arguments should be used:
mavproxy.py --master=udpout:10.10.1.1:14550
mavproxy.py --master=tcpout:10.10.1.1:14550

USB and COM Connection
mavproxy.py --master=/dev/ttyUSB0
mavproxy.py --master=/dev/ttyUSB0,57600
mavproxy.py --master=udp:192.168.1.1:14550 --master=/dev/ttyUSB0
mavproxy.py --master="com14"

TCP and UDP Connection
mavproxy.py --master=tcp:192.168.1.1:14550
mavproxy.py --master=udp:127.0.0.1:14550
mavproxy.py --master=tcp:0.0.0.0:14550

```

## Note
I've spend a lot of time to debug this on how to connect `MAVPROXY` to `Mission Planner`. Please use below command to establish the connection.
```
mavproxy.exe --master tcp:127.0.0.1:5760 --out udp:127.0.0.1:14550

Here we enable tcp connection in cygwin and then forward Mavlink data to mission planner using UDP protocol
```

To `run` python script use port forwarded `udp` above. Modify the `connection_string` as follow
```
from dronekit import *
''' Using SITL connection '''
connection_string = '127.0.0.1:14550'
```

If you don't want to use `127.0.0.1:14550` but instead to use IP adress from `mavproxy` as follow
```
mavproxy.py --master=/dev/ttyAMA0,921600 --out=udp:10.60.216.198:14550
```

Then modify `connection_string` as follow
```
'''Using port forwarding from MavProxy'''
#connection_string = '10.60.216.198:14550'
```
>Tips : Refer to page ardupilot.org/mavproxy/docs/gettingstarted/quickstart.html

## Ubuntu Desktop, Virtual Box, WISL (with X-launch)

1. Install below packages and follow the subsequent command
```
sudo apt-get update
sudo apt-get upgrade

Next we're going to install python2.7. To install python2.7, run following command:
sudo apt-get update
sudo apt-get upgrade
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt install python2 --> Which will download python2.7. 

However in WSL, python2 is not recognized. To download python2.7 with pip. Use this command
sudo apt-get install python-pip
  
Once completed run python to check if python2.7 is installed
python --version
```

2. `Skip` this step if you install `python 2.7` using `install python-pip` command. This step is need for `apt install python2` only
Next we're going to install python 2 pip. To install pip, use following command
```
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py # Fetch get-pip.py for python 2.7 
sudo python get-pip.py
pip --version

Once completed run velow command to to determine if installed pip is refer to python 2.7 using below command
whereis pip 
```

3. Next we're going to install python 3 pip. To install pip, use following command
```
sudo apt-get update
sudo apt install python3-pip

Once completed run python to check if pip installed pip is refer to python 3.6 using below command
whereis pip
```

4. `Repeat` step `1` in `Jetson Nano` section above
5.  Remeber, before installing `SITL`, modify following line accordingly
```
vi ./Tools/environment_install/install-prereqs-ubuntu.sh

change pymavlink version to 2.0.6, and dronecan to 1.0.14

   Modify as follow:
   pymavlink==2.4.33
   dronecan==1.0.14
   
 Once this step completed you're safe to run SITL installation *.sh file
```

