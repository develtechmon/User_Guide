# Getting Started

This is userguide on how to create a `bin` file for `ESP32 Ardupilot Firmware`. In this case,
i'm using `WSL` windows.
Ubuntu version as follow
```
* Ubuntu 20.04.6 LTS on Windows 10 x86_64
* Kernel 6.6.87.1-microsoft-standard-WSL2
```
## Part 1 : Download Ubuntu

Go to `Microsoft Store` and download `Ubuntu 20.04.6` into your machine

## Part 2 : Open Ubuntu Machine

Open windows terminal and switch to `Ubuntu` using following command
```
wsl --list
wsl -d Ubuntu-20.04
cd ~
```
## Part 3 : Install Packages

Please copy, paste and install following packages into Linux machine:

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install git
sudo pip install pexpect 
sudo apt-get install python3.8-venv
sudo apt-get install git wget flex bison gperf cmake ninja-build ccache libffi-dev libssl-dev dfu-util
sudo apt-get install python3 python3-pip python3-setuptools
```

## Part 4 : Install WAF Ardupilot

To create `bin` file for ESP32 Ardupilot, it's really important to create a `WAF` environment. This step is crucial. Please
follow step as follow:

```
git clone https://github.com/ArduPilot/ardupilot
cd ardupilot
git submodule update --init --recursive
./Tools/environment_install/install-prereqs-ubuntu.sh
. ~/.profile (Space in between . and ~ - reload this profile if sim_vehicle not found)

cd ardupilot/Arducopter
```

Create a `WAF` environment by running following command. This will launch a terminal of our copter.
```
sim_vehicle.py -w
```

If you encounter error, please understand the error and solve it from there.

## Part 5 : Install ESP-IDF environment

To build the `bin` file, we have to setup a `ESP-IDF` environment first as follow

```
cd ardupilot
./Tools/scripts/esp32_get_idf.sh -y
cd modules/esp_idf
./install.sh

unset IDF_PATH
source ./export.sh
cd ../../..
```

## Part 6 : Configure and run the build

To create a bin for our `ESP32`, we have to modify `esp32diy.h` and `esp32buzz.h` file to match our ESP32 configuration. For this setup, i'm
using `Node MCU ESP32 38 Pin` as follow:

