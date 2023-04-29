# Getting Started

This userguide explain on how to establish a connection between `RPI` and `Jetson Nano` with `Pixhawk` flight controller 

## Jetson Nano

## UART
1. `Hardware` Connection using `UART` interface between `Jetson Nano` and `Pixhawk` as follow
```
Jetson Nano Pin
Pin            Pin Number    Pin
(3.3V)             1:2      (5V)
(SDA1 I2C)         3:4      (5V)
(SCL1 I2C)         5:6      (GND)
(AUDIO -MCLK)      7:8      (TXD0)
(GND)              9:10     (RXD0)

Pihxawk Pin
Telemetry2 Pin
5V -- TX -- RX -- Null -- Null -- GND

Connect as follow
Jetson Nano Pin Number      Pixhawk
(GND)            6     ---   (GND)
(TXD0)           8     ---    (RX)
(RXD0)           10    ---    (TX)
```

2. Then open `Terminal` from Jetson Nano and follow below command to authorized the `port`
```
ls /devttyTHS1 (Port from Pixhawk, by default this port is not authorized for read and write access. You will encounter permission denied)

To enable this, simpy run
sudo chmod 666 /dev/ttyTHS1

Or Insert this line in `bashrc` script to automatic source and enable this port after reboot everytime
echo 2328 | sudo -S chmod 666 /dev/ttyTHS1
```

3. Connect `Pixhawk` to `Mission Planner` using `USB` or `Telemetry`
4. From `Mission Planner` go to `Full parameter list` and set following parameter. Here i use `Telemetry2 port`. Once done, `write` the parameters to `Pixhawk`
```
SERIAL2_PROTOCOL = 2 (To enable MAVLINK2 on serial port)
SERIAL2_BAUD = 921 (So flight controller can communicate with RPI or Jetson at 921600 Baud Rate)
LOG_BACKEND_TYPE = 3 (If APSYNC is used - Normally i'll ignore this)
```

5. Install following packages in Jetson Nano before installing `Dronekit` package
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install git cmake
sudo apt-get install libatlas-base-dev gfortran
sudo apt-get install libhdf5-serial-dev hdf5-tools
sudo apt-get install python3-dev
sudo apt-get install nano locate
sudo apt-get install libfreetype6-dev python3-setuptools
sudo apt-get install protobuf-compiler libprotobuf-dev openssl
sudo apt-get install libssl-dev libcurl4-openssl-dev
sudo apt-get install cython3
sudo apt-get install libxml2-dev libxslt1-dev
sudo apt-get install build-essential pkg-config
sudo apt-get install libtbb2 libtbb-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install libxvidcore-dev libavresample-dev
sudo apt-get install libtiff-dev libjpeg-dev libpng-dev
sudo apt-get install python-tk libgtk-3-dev
sudo apt-get install libcanberra-gtk-module libcanberra-gtk3-module
sudo apt-get install libv4l-dev libdc1394-22-dev

sudo apt-get update
sudo apt-get upgrade
sudo apt-get install python-dev
sudo apt-get install screen python-wxgtk4.0 python-lxml
sudo pip install future
sudo pip install pyserial
sudo pip install dronekit
sudo pip install MAVProxy
```

6. To test if connection is working. Run below `command` using `mavproxy`. You should see `hearbeart` data from flight controller.
```
mavproxy.py --master=/dev/ttyTHS1,921600 
```

## USB

1. This is very easy and simple configuration.  You just need to connect `USB` from `Pixhawk` to `Jetson Nano` usb port.
2. Install above package as in `Step 5`.
3. Open `Terminal` from Jetson Nano and run below command to list down the `port`
```
ls /dev/ttyACM0
```
4. To test if connection is working. Run below `command` using `mavproxy`. You should see `hearbeart` data from flight controller.
```
mavproxy.py --master=/dev/ttyACM0,921600
```


## Raspberry Pi (Zero, 4)
