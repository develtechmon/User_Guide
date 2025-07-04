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
sudo apt-get install python3-pip
sudo pip install pexpect 
sudo apt-get install python3.8-venv
sudo apt-get install git wget flex bison gperf cmake ninja-build ccache libffi-dev libssl-dev dfu-util
sudo apt-get install python3 python3-pip python3-setuptools

python3 -m pip install empy==3.3.4
python3 -m pip install pexpect
python3 -m pip install future
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
## This is `schematic` for this build if you're using `mpu6050`, `bmp280` and `QMCL` sensors

![Screenshot from 2025-06-09 15-41-07](https://github.com/user-attachments/assets/58c32001-523d-461d-949a-f5979469268a)
![Screenshot from 2025-06-09 15-42-41](https://github.com/user-attachments/assets/4bf6267d-d539-4d56-a40a-8e68cb7ada89)
![Screenshot from 2025-06-09 15-43-23](https://github.com/user-attachments/assets/24767f65-a04e-41e9-be50-5ae34a287e0e)


## Part 6 : Configure and run the build

To create a bin for our `ESP32`, we have to modify `esp32diy.h` and `esp32buzz.h` file to match our ESP32 GPIO configuration. For this setup, i'm
using `Node MCU ESP32 38 Pin` as follow:
```
https://cdn.shopify.com/s/files/1/0903/0218/5736/files/Node_MCU_ESP32_38Pin_pinout.webp?v=1735563613
```
![image](https://github.com/user-attachments/assets/eac488d6-e4f7-4650-8559-12dd1a8e3dba)

We have to modify this file which can be found at this path
```
cd ardupilot/libraries/AP_HAL_ESP32/boards
```

## For MPU6050, BMP and Compass
This is an updated `esp32buzz.h` that work with MPU6050

```
/*
 * This file is free software: you can redistribute it and/or modify it
 * under the terms of the GNU General Public License as published by the
 * Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This file is distributed in the hope that it will be useful, but
 * WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
 * See the GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License along
 * with this program.  If not, see <http://www.gnu.org/licenses/>.
 */
#pragma once

#define HAL_ESP32_BOARD_NAME "esp32-buzz"

// make sensor selection clearer
#define PROBE_IMU_I2C(driver, bus, addr, args ...) ADD_BACKEND(AP_InertialSensor_ ## driver::probe(*this,GET_I2C_DEVICE(bus, addr),##args))
// #define PROBE_IMU_SPI(driver, devname, args ...) ADD_BACKEND(AP_InertialSensor_ ## driver::probe(*this,hal.spi->get_device(devname),##args))
// #define PROBE_IMU_SPI2(driver, devname1, devname2, args ...) ADD_BACKEND(AP_InertialSensor_ ## driver::probe(*this,hal.spi->get_device(devname1),hal.spi->get_device(devname2),##args))

#define PROBE_BARO_I2C(driver, bus, addr, args ...) ADD_BACKEND(AP_Baro_ ## driver::probe(*this,std::move(GET_I2C_DEVICE(bus, addr)),##args))
// #define PROBE_BARO_SPI(driver, devname, args ...) ADD_BACKEND(AP_Baro_ ## driver::probe(*this,std::move(hal.spi->get_device(devname)),##args))

#define PROBE_MAG_I2C(driver, bus, addr, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe(GET_I2C_DEVICE(bus, addr),##args))
// #define PROBE_MAG_SPI(driver, devname, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe(hal.spi->get_device(devname),##args))
// #define PROBE_MAG_IMU(driver, imudev, imu_instance, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe_ ## imudev(imu_instance,##args))
// #define PROBE_MAG_IMU_I2C(driver, imudev, bus, addr, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe_ ## imudev(GET_I2C_DEVICE(bus,addr),##args))
//------------------------------------


//#define CONFIG_HAL_BOARD 12
//#define HAL_BOARD_ESP32 12

//INS choices:
//#define HAL_INS_DEFAULT HAL_INS_MPU9250_SPI
//#define HAL_INS_MPU9250_NAME "MPU9250"

//ref - #define HAL_INS_DEFAULT HAL_INS_INVENSENSE_I2C

// or this:
//#define HAL_INS_DEFAULT HAL_INS_ICM20XXX_I2C
//#define HAL_INS_ICM20XXX_I2C_BUS 0
//#define HAL_INS_ICM20XXX_I2C_ADDR (0x68)

// MAG/COMPASS choices:
// or others:
//#define HAL_COMPASS_ICM20948_I2C_ADDR (0x68)
//#define HAL_COMPASS_AK09916_I2C_BUS 0
//#define HAL_COMPASS_AK09916_I2C_ADDR (0x0C)
//#define HAL_COMPASS_MAX_SENSORS 3

#define HAL_INS_DEFAULT HAL_INS_INVENSENSE_I2C
#define HAL_INS_INVENSENSE_I2C_BUS  0
#define HAL_INS_INVENSENSE_I2C_ADDR  (0x68)
#define HAL_INS_PROBE_LIST PROBE_IMU_I2C(Invensense, HAL_INS_INVENSENSE_I2C_BUS, HAL_INS_INVENSENSE_I2C_ADDR, ROTATION_NONE)

// IMU probing:
//#define HAL_INS_PROBE_LIST PROBE_IMU_I2C(Invensensev2, 0, 0x68, ROTATION_YAW_270)
// MAG/COMPASS probing:
//#define HAL_MAG_PROBE_LIST ADD_BACKEND(DRIVER_ICM20948, AP_Compass_AK09916::probe_ICM20948_I2C(0, ROTATION_NONE));
// BARO probing:
//#define HAL_BARO_PROBE_LIST PROBE_BARO_I2C(BMP280, 0, 0x77)

// no airspeed sensor
#define AP_AIRSPEED_MS4525_ENABLED 0
#define AP_AIRSPEED_ENABLED 0
#define AP_AIRSPEED_ANALOG_ENABLED 0
#define AP_AIRSPEED_BACKEND_DEFAULT_ENABLED 0

// allow boot without a baro
// BAROMETER - DISABLED
#define AP_BARO_BACKEND_DEFAULT_ENABLED 0
#define HAL_BARO_PROBE_LIST
#define HAL_BARO_ALLOW_INIT_NO_BARO 1

// COMPASS - DISABLED (MPU6050 has no compass)
#define AP_COMPASS_ENABLE_DEFAULT 0
#define ALLOW_ARM_NO_COMPASS
#define HAL_MAG_PROBE_LIST

// ADC is available on lots of pints on the esp32, but adc2 can't co-exist with wifi we choose to allow ADC on :
//#define HAL_DISABLE_ADC_DRIVER 1
#define TRUE 1
#define HAL_USE_ADC TRUE

// the pin number, the gain/multiplier associated with it, the ardupilot name for the pin in parameter/s.
//
// two different pin numbering schemes, both are ok, but only one at a time:
#define HAL_ESP32_ADC_PINS_OPTION1 {\
	{ADC1_GPIO35_CHANNEL, 11, 1},\
	{ADC1_GPIO34_CHANNEL, 11, 2},\
	{ADC1_GPIO39_CHANNEL, 11, 3},\
	{ADC1_GPIO36_CHANNEL, 11, 4}\
}
#define HAL_ESP32_ADC_PINS_OPTION2 {\
	{ADC1_GPIO35_CHANNEL, 11, 35},\
	{ADC1_GPIO34_CHANNEL, 11, 34},\
	{ADC1_GPIO39_CHANNEL, 11, 39},\
	{ADC1_GPIO36_CHANNEL, 11, 36}\
}
// pick one:
//#define HAL_ESP32_ADC_PINS HAL_ESP32_ADC_PINS_OPTION1
#define HAL_ESP32_ADC_PINS HAL_ESP32_ADC_PINS_OPTION2



// #define HAL_PROBE_EXTERNAL_I2C_COMPASSES 1


//#define HAL_INS_MPU9250_NAME "mpu9250"

// uncommenting one or more of these will give more console debug in certain areas.. ... 
// ...however all teh extra printf's use a lot of stack, so best to limit yourself to only uncommenting one at a time
//#define STORAGEDEBUG 1
//#define SCHEDDEBUG 1
//#define FSDEBUG 1
//#define BUSDEBUG 1 //ok
//#define WIFIDEBUG 1 //uses a lot?
//#define INS_TIMING_DEBUG 1 //define this to see all the imu-resets and temp resets and imu timing info on the console.

//#define HAL_INS_PROBE_LIST PROBE_IMU_SPI( Invensense, HAL_INS_MPU9250_NAME, ROTATION_NONE)
//#define HAL_INS_PROBE_LIST PROBE_IMU_SPI( Invensense, HAL_INS_MPU9250_NAME, ROTATION_ROLL_180)


// #define HAL_BARO_PROBE_LIST PROBE_BARO_SPI(BMP280, "bmp280")
// #define HAL_BARO_PROBE_LIST

// 2 use udp, 1 use tcp...  for udp,client needs to connect as UDPCL in missionplanner etc to 192.168.4.1 port 14550
#define HAL_ESP32_WIFI 1

// tip: if u are ok getting mavlink-over-tcp or mavlink-over-udp and want to disable mavlink-over-serial-usb
//then set ardupilot parameter SERIAL0_PROTOCOL = 0 and reboot.
// u also will/may want..
//LOG_BACKEND_TYPE 1
//LOG_DISARMED 1
//SERIAL0_PROTOCOL 0


// see boards.py
#ifndef ENABLE_HEAP
#define ENABLE_HEAP 1
#endif

#define WIFI_SSID "ardupilot123"
#define WIFI_PWD "ardupilot123"

// *** SCHEDULER CONFIGURATION ***
// Set main loop frequency - 200Hz is good balance for ESP32
#define AP_SCHEDULER_DEFAULTS_MAX_LOOP_HZ 200
#define HAL_SCHEDULER_LOOP_RATE_HZ 200

//RCOUT which pins are used?

#define HAL_ESP32_RCOUT { GPIO_NUM_25,GPIO_NUM_27, GPIO_NUM_33, GPIO_NUM_32 }

// SPI BUS setup, including gpio, dma, etc
// note... we use 'vspi' for the bmp280 and mpu9250
// #define HAL_ESP32_SPI_BUSES \{.host=VSPI_HOST, .dma_ch=1, .mosi=GPIO_NUM_23, .miso=GPIO_NUM_19, .sclk=GPIO_NUM_18}
// tip:  VSPI_HOST  is an alternative name for esp's SPI3
//#define HAL_ESP32_SPI_BUSES {}

// SPI per-device setup, including speeds, etc.
// #define HAL_ESP32_SPI_DEVICES \ {.name= "bmp280", .bus=0, .device=0, .cs=GPIO_NUM_26, .mode = 3, .lspeed=1*MHZ, .hspeed=1*MHZ}
//    {.name="mpu9250", .bus=0, .device=1, .cs=GPIO_NUM_5,  .mode = 0, .lspeed=2*MHZ, .hspeed=8*MHZ}
//#define HAL_ESP32_SPI_DEVICES {}

// SPI - DISABLED (not using SPI sensors for now)
#define HAL_ESP32_SPI_BUSES {}
#define HAL_ESP32_SPI_DEVICES {}

//I2C bus list
#define HAL_ESP32_I2C_BUSES \
	{.port=I2C_NUM_0, .sda=GPIO_NUM_21, .scl=GPIO_NUM_22, .speed=400*KHZ, .internal=true}
//#define HAL_ESP32_I2C_BUSES {} // using this embty block appears to cause crashes?


// rcin on what pin?
#define HAL_ESP32_RCIN GPIO_NUM_4


//HARDWARE UARTS
#define HAL_ESP32_UART_DEVICES \
  {.port=UART_NUM_0, .rx=GPIO_NUM_3, .tx=GPIO_NUM_1 },{.port=UART_NUM_1, .rx=GPIO_NUM_16, .tx=GPIO_NUM_17 }

#define AP_FILESYSTEM_ESP32_ENABLED 1

// Do u want to use mmc or spi mode for the sd card, this is board specific ,
//  as mmc uses specific pins but is quicker,
#define HAL_ESP32_SDMMC 1
// and spi is more flexible pinouts....  dont forget vspi/hspi should be selected to NOT conflict with SPI_BUSES above
//#define HAL_ESP32_SDSPI {.host=VSPI_HOST, .dma_ch=2, .mosi=GPIO_NUM_2, .miso=GPIO_NUM_15, .sclk=GPIO_NUM_14, .cs=GPIO_NUM_21}

#define HAL_ESP32_SDCARD 1
#define LOGGER_MAVLINK_SUPPORT 1
#define HAL_BOARD_LOG_DIRECTORY "/SDCARD/APM/LOGS"
#define HAL_BOARD_TERRAIN_DIRECTORY "/SDCARD/APM/TERRAIN"
#define HAL_BOARD_STORAGE_DIRECTORY "/SDCARD/APM/STORAGE"

// this becomes the default value for the ardupilot param LOG_BACKEND_TYPE, which most ppl want to be 1, for log-to-flash
// setting to 2 means log-over-mavlink to a companion computer etc.
#define HAL_LOGGING_BACKENDS_DEFAULT 1

#define HAL_ESP32_RMT_RX_PIN_NUMBER 4

```

This is `esp32buzz.h` that support `MPU6050` and `bmp` using `i2c`
```
/*
 * This file is free software: you can redistribute it and/or modify it
 * under the terms of the GNU General Public License as published by the
 * Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This file is distributed in the hope that it will be useful, but
 * WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
 * See the GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License along
 * with this program.  If not, see <http://www.gnu.org/licenses/>.
 */
#pragma once

#define HAL_ESP32_BOARD_NAME "esp32-buzz"

// make sensor selection clearer
#define PROBE_IMU_I2C(driver, bus, addr, args ...) ADD_BACKEND(AP_InertialSensor_ ## driver::probe(*this,GET_I2C_DEVICE(bus, addr),##args))
//#define PROBE_IMU_SPI(driver, devname, args ...) ADD_BACKEND(AP_InertialSensor_ ## driver::probe(*this,hal.spi->get_device(devname),##args))
//#define PROBE_IMU_SPI2(driver, devname1, devname2, args ...) ADD_BACKEND(AP_InertialSensor_ ## driver::probe(*this,hal.spi->get_device(devname1),hal.spi->get_device(devname2),##args))

#define PROBE_BARO_I2C(driver, bus, addr, args ...) ADD_BACKEND(AP_Baro_ ## driver::probe(*this,std::move(GET_I2C_DEVICE(bus, addr)),##args))
//#define PROBE_BARO_SPI(driver, devname, args ...) ADD_BACKEND(AP_Baro_ ## driver::probe(*this,std::move(hal.spi->get_device(devname)),##args))

#define PROBE_MAG_I2C(driver, bus, addr, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe(GET_I2C_DEVICE(bus, addr),##args))
//#define PROBE_MAG_SPI(driver, devname, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe(hal.spi->get_device(devname),##args))
//#define PROBE_MAG_IMU(driver, imudev, imu_instance, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe_ ## imudev(imu_instance,##args))
//#define PROBE_MAG_IMU_I2C(driver, imudev, bus, addr, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe_ ## imudev(GET_I2C_DEVICE(bus,addr),##args))
//------------------------------------


//#define CONFIG_HAL_BOARD 12
//#define HAL_BOARD_ESP32 12

//INS choices:
//#define HAL_INS_DEFAULT HAL_INS_MPU9250_SPI
//#define HAL_INS_MPU9250_NAME "MPU9250"

// or this:
//#define HAL_INS_DEFAULT HAL_INS_ICM20XXX_I2C
//#define HAL_INS_ICM20XXX_I2C_BUS 0
//#define HAL_INS_ICM20XXX_I2C_ADDR (0x68)

// MAG/COMPASS choices:
// or others:
//#define HAL_COMPASS_ICM20948_I2C_ADDR (0x68)
//#define HAL_COMPASS_AK09916_I2C_BUS 0
//#define HAL_COMPASS_AK09916_I2C_ADDR (0x0C)
//#define HAL_COMPASS_MAX_SENSORS 3

// IMU probing:
//#define HAL_INS_PROBE_LIST PROBE_IMU_I2C(Invensensev2, 0, 0x68, ROTATION_YAW_270)
// MAG/COMPASS probing:
//#define HAL_MAG_PROBE_LIST ADD_BACKEND(DRIVER_ICM20948, AP_Compass_AK09916::probe_ICM20948_I2C(0, ROTATION_NONE));
// BARO probing:
//#define HAL_BARO_PROBE_LIST PROBE_BARO_I2C(BMP280, 0, 0x77)

// *** MPU6050 IMU CONFIGURATION (I2C) ***
#define HAL_INS_DEFAULT HAL_INS_INVENSENSE_I2C
#define HAL_INS_INVENSENSE_I2C_BUS  0
#define HAL_INS_INVENSENSE_I2C_ADDR  (0x68)
#define HAL_INS_PROBE_LIST PROBE_IMU_I2C(Invensense, HAL_INS_INVENSENSE_I2C_BUS, HAL_INS_INVENSENSE_I2C_ADDR, ROTATION_YAW_270)

// no airspeed sensor
#define AP_AIRSPEED_MS4525_ENABLED 0
#define AP_AIRSPEED_ENABLED 0
#define AP_AIRSPEED_ANALOG_ENABLED 0
#define AP_AIRSPEED_BACKEND_DEFAULT_ENABLED 0

// *** BMP280 BAROMETER CONFIGURATION (I2C) ***
#define HAL_BARO_DEFAULT HAL_BARO_BMP280_I2C
#define HAL_BARO_BMP280_I2C_BUS 0
#define HAL_BARO_BMP280_I2C_ADDR (0x76)  // or 0x77 depending on your module
#define HAL_BARO_PROBE_LIST PROBE_BARO_I2C(BMP280, HAL_BARO_BMP280_I2C_BUS, HAL_BARO_BMP280_I2C_ADDR)

// allow boot without a baro
#define HAL_BARO_ALLOW_INIT_NO_BARO 1

// *** COMPASS - DISABLED (neither sensor has compass) ***
#define AP_COMPASS_ENABLE_DEFAULT 0
#define ALLOW_ARM_NO_COMPASS
#define HAL_MAG_PROBE_LIST

// ADC is available on lots of pints on the esp32, but adc2 can't co-exist with wifi we choose to allow ADC on :
//#define HAL_DISABLE_ADC_DRIVER 1
#define TRUE 1
#define HAL_USE_ADC TRUE

// the pin number, the gain/multiplier associated with it, the ardupilot name for the pin in parameter/s.
//
// two different pin numbering schemes, both are ok, but only one at a time:
#define HAL_ESP32_ADC_PINS_OPTION1 {\
	{ADC1_GPIO35_CHANNEL, 11, 1},\
	{ADC1_GPIO34_CHANNEL, 11, 2},\
	{ADC1_GPIO39_CHANNEL, 11, 3},\
	{ADC1_GPIO36_CHANNEL, 11, 4}\
}
#define HAL_ESP32_ADC_PINS_OPTION2 {\
	{ADC1_GPIO35_CHANNEL, 11, 35},\
	{ADC1_GPIO34_CHANNEL, 11, 34},\
	{ADC1_GPIO39_CHANNEL, 11, 39},\
	{ADC1_GPIO36_CHANNEL, 11, 36}\
}
// pick one:
//#define HAL_ESP32_ADC_PINS HAL_ESP32_ADC_PINS_OPTION1
#define HAL_ESP32_ADC_PINS HAL_ESP32_ADC_PINS_OPTION2



//#define HAL_PROBE_EXTERNAL_I2C_COMPASSES 1


//#define HAL_INS_MPU9250_NAME "mpu9250"

// uncommenting one or more of these will give more console debug in certain areas.. ... 
// ...however all teh extra printf's use a lot of stack, so best to limit yourself to only uncommenting one at a time
//#define STORAGEDEBUG 1
//#define SCHEDDEBUG 1
//#define FSDEBUG 1
#define BUSDEBUG 1 //ok
//#define WIFIDEBUG 1 //uses a lot?
//#define INS_TIMING_DEBUG 1 //define this to see all the imu-resets and temp resets and imu timing info on the console.

//#define HAL_INS_PROBE_LIST PROBE_IMU_SPI( Invensense, HAL_INS_MPU9250_NAME, ROTATION_NONE)
//#define HAL_INS_PROBE_LIST PROBE_IMU_SPI( Invensense, HAL_INS_MPU9250_NAME, ROTATION_ROLL_180)


//#define HAL_BARO_PROBE_LIST PROBE_BARO_SPI(BMP280, "bmp280")

// 2 use udp, 1 use tcp...  for udp,client needs to connect as UDPCL in missionplanner etc to 192.168.4.1 port 14550
#define HAL_ESP32_WIFI 1

// tip: if u are ok getting mavlink-over-tcp or mavlink-over-udp and want to disable mavlink-over-serial-usb
//then set ardupilot parameter SERIAL0_PROTOCOL = 0 and reboot.
// u also will/may want..
//LOG_BACKEND_TYPE 1
//LOG_DISARMED 1
//SERIAL0_PROTOCOL 0


// see boards.py
#ifndef ENABLE_HEAP
#define ENABLE_HEAP 1
#endif

#define WIFI_SSID "ardupilot123"
#define WIFI_PWD "ardupilot123"

// *** SCHEDULER CONFIGURATION ***
// Set main loop frequency - 200Hz is good balance for ESP32
#define AP_SCHEDULER_DEFAULTS_MAX_LOOP_HZ 100
#define HAL_SCHEDULER_LOOP_RATE_HZ 100

//RCOUT which pins are used?

#define HAL_ESP32_RCOUT { GPIO_NUM_25, GPIO_NUM_27, GPIO_NUM_33, GPIO_NUM_32 }

// SPI BUS setup, including gpio, dma, etc
// note... we use 'vspi' for the bmp280 and mpu9250
// #define HAL_ESP32_SPI_BUSES \/
//     {.host=VSPI_HOST, .dma_ch=1, .mosi=GPIO_NUM_23, .miso=GPIO_NUM_19, .sclk=GPIO_NUM_18}
// tip:  VSPI_HOST  is an alternative name for esp's SPI3
//#define HAL_ESP32_SPI_BUSES {}

// SPI per-device setup, including speeds, etc.
// #define HAL_ESP32_SPI_DEVICES \/
//     {.name= "bmp280", .bus=0, .device=0, .cs=GPIO_NUM_26, .mode = 3, .lspeed=1*MHZ, .hspeed=1*MHZ}, \/
//     {.name="mpu9250", .bus=0, .device=1, .cs=GPIO_NUM_5,  .mode = 0, .lspeed=2*MHZ, .hspeed=8*MHZ}
//#define HAL_ESP32_SPI_DEVICES {}

// *** SPI - DISABLED (using I2C sensors only) ***
#define HAL_ESP32_SPI_BUSES {}
#define HAL_ESP32_SPI_DEVICES {}

//I2C bus list
#define HAL_ESP32_I2C_BUSES \
	{.port=I2C_NUM_0, .sda=GPIO_NUM_21, .scl=GPIO_NUM_22, .speed=400*KHZ, .internal=true}
//#define HAL_ESP32_I2C_BUSES {} // using this embty block appears to cause crashes?


// rcin on what pin?
#define HAL_ESP32_RCIN GPIO_NUM_4


//HARDWARE UARTS
#define HAL_ESP32_UART_DEVICES \
  {.port=UART_NUM_0, .rx=GPIO_NUM_3, .tx=GPIO_NUM_1 },{.port=UART_NUM_1, .rx=GPIO_NUM_16, .tx=GPIO_NUM_17 }

#define AP_FILESYSTEM_ESP32_ENABLED 1

// Do u want to use mmc or spi mode for the sd card, this is board specific ,
//  as mmc uses specific pins but is quicker,
#define HAL_ESP32_SDMMC 1
// and spi is more flexible pinouts....  dont forget vspi/hspi should be selected to NOT conflict with SPI_BUSES above
//#define HAL_ESP32_SDSPI {.host=VSPI_HOST, .dma_ch=2, .mosi=GPIO_NUM_2, .miso=GPIO_NUM_15, .sclk=GPIO_NUM_14, .cs=GPIO_NUM_21}

#define HAL_ESP32_SDCARD 1
#define LOGGER_MAVLINK_SUPPORT 1
#define HAL_BOARD_LOG_DIRECTORY "/SDCARD/APM/LOGS"
#define HAL_BOARD_TERRAIN_DIRECTORY "/SDCARD/APM/TERRAIN"
#define HAL_BOARD_STORAGE_DIRECTORY "/SDCARD/APM/STORAGE"

// this becomes the default value for the ardupilot param LOG_BACKEND_TYPE, which most ppl want to be 1, for log-to-flash
// setting to 2 means log-over-mavlink to a companion computer etc.
#define HAL_LOGGING_BACKENDS_DEFAULT 1

#define HAL_ESP32_RMT_RX_PIN_NUMBER 4

```

This is `esp32buzz.h` that support `MPU6050`, `bmp280` and `compassQMC5883L` using `i2c`
```
/*
 * This file is free software: you can redistribute it and/or modify it
 * under the terms of the GNU General Public License as published by the
 * Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This file is distributed in the hope that it will be useful, but
 * WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
 * See the GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License along
 * with this program.  If not, see <http://www.gnu.org/licenses/>.
 */
#pragma once

#define HAL_ESP32_BOARD_NAME "esp32-buzz"

//=============================================================================
// SENSOR PROBE MACROS
//=============================================================================
#define PROBE_IMU_I2C(driver, bus, addr, args ...) ADD_BACKEND(AP_InertialSensor_ ## driver::probe(*this,GET_I2C_DEVICE(bus, addr),##args))
#define PROBE_BARO_I2C(driver, bus, addr, args ...) ADD_BACKEND(AP_Baro_ ## driver::probe(*this,std::move(GET_I2C_DEVICE(bus, addr)),##args))
#define PROBE_MAG_I2C(driver, bus, addr, args ...) ADD_BACKEND(DRIVER_ ##driver, AP_Compass_ ## driver::probe(GET_I2C_DEVICE(bus, addr),##args))

//=============================================================================
// IMU CONFIGURATION - MPU6050 (I2C)
//=============================================================================
// Alternative orientations if needed:
// #define HAL_INS_PROBE_LIST PROBE_IMU_I2C(Invensense, 0, 0x68, ROTATION_ROLL_180)
// #define HAL_INS_PROBE_LIST PROBE_IMU_I2C(Invensense, 0, 0x68, ROTATION_YAW_90)

#define HAL_INS_DEFAULT HAL_INS_INVENSENSE_I2C
#define HAL_INS_INVENSENSE_I2C_BUS 0
#define HAL_INS_INVENSENSE_I2C_ADDR (0x68)
#define HAL_INS_PROBE_LIST PROBE_IMU_I2C(Invensense, HAL_INS_INVENSENSE_I2C_BUS, HAL_INS_INVENSENSE_I2C_ADDR, ROTATION_YAW_270)

//=============================================================================
// AIRSPEED SENSOR - DISABLED
//=============================================================================
#define AP_AIRSPEED_MS4525_ENABLED 0
#define AP_AIRSPEED_ENABLED 0
#define AP_AIRSPEED_ANALOG_ENABLED 0
#define AP_AIRSPEED_BACKEND_DEFAULT_ENABLED 0

//=============================================================================
// BAROMETER CONFIGURATION - BMP280 (I2C)
//=============================================================================
#define HAL_BARO_DEFAULT HAL_BARO_BMP280_I2C
#define HAL_BARO_BMP280_I2C_BUS 0
#define HAL_BARO_BMP280_I2C_ADDR (0x76)  // or 0x77 depending on your module
#define HAL_BARO_PROBE_LIST PROBE_BARO_I2C(BMP280, HAL_BARO_BMP280_I2C_BUS, HAL_BARO_BMP280_I2C_ADDR)

// Allow boot without barometer (for testing)
#define HAL_BARO_ALLOW_INIT_NO_BARO 1

//=============================================================================
// COMPASS CONFIGURATION - GY-271 HMC5883L (I2C)
//=============================================================================
#define AP_COMPASS_ENABLE_DEFAULT 1
#define ALLOW_ARM_NO_COMPASS

// HMC5883L specific configuration - try both common addresses
#define HAL_COMPASS_HMC5843_I2C_BUS 0
#define HAL_COMPASS_HMC5843_I2C_ADDR (0x1E)  // Primary address for HMC5883L
#define HAL_COMPASS_HMC5843_I2C_ADDR_ALT (0x0D)  // Alternative address
#define HAL_PROBE_EXTERNAL_I2C_COMPASSES 1

// Probe list for HMC5883L (try both addresses and both drivers)
#define HAL_MAG_PROBE_LIST \
    ADD_BACKEND(DRIVER_HMC5843, AP_Compass_HMC5843::probe(GET_I2C_DEVICE(HAL_COMPASS_HMC5843_I2C_BUS, HAL_COMPASS_HMC5843_I2C_ADDR), false, ROTATION_NONE)); \
    ADD_BACKEND(DRIVER_HMC5843, AP_Compass_HMC5843::probe(GET_I2C_DEVICE(HAL_COMPASS_HMC5843_I2C_BUS, HAL_COMPASS_HMC5843_I2C_ADDR_ALT), false, ROTATION_NONE)); \
    ADD_BACKEND(DRIVER_QMC5883L, AP_Compass_QMC5883L::probe(GET_I2C_DEVICE(HAL_COMPASS_HMC5843_I2C_BUS, HAL_COMPASS_HMC5843_I2C_ADDR), false, ROTATION_NONE)); \
    ADD_BACKEND(DRIVER_QMC5883L, AP_Compass_QMC5883L::probe(GET_I2C_DEVICE(HAL_COMPASS_HMC5843_I2C_BUS, HAL_COMPASS_HMC5843_I2C_ADDR_ALT), false, ROTATION_NONE))

// Enable specific compass backends
#define AP_COMPASS_HMC5843_ENABLED 1
#define AP_COMPASS_QMC5883L_ENABLED 1
#define AP_COMPASS_BACKEND_DEFAULT_ENABLED 1

//=============================================================================
// ADC CONFIGURATION
//=============================================================================
#define TRUE 1
#define HAL_USE_ADC TRUE

// ADC pin options
#define HAL_ESP32_ADC_PINS_OPTION1 {\
	{ADC1_GPIO35_CHANNEL, 11, 1},\
	{ADC1_GPIO34_CHANNEL, 11, 2},\
	{ADC1_GPIO39_CHANNEL, 11, 3},\
	{ADC1_GPIO36_CHANNEL, 11, 4}\
}
#define HAL_ESP32_ADC_PINS_OPTION2 {\
	{ADC1_GPIO35_CHANNEL, 11, 35},\
	{ADC1_GPIO34_CHANNEL, 11, 34},\
	{ADC1_GPIO39_CHANNEL, 11, 39},\
	{ADC1_GPIO36_CHANNEL, 11, 36}\
}
// pick one:
#define HAL_ESP32_ADC_PINS HAL_ESP32_ADC_PINS_OPTION2

// Debug options (enable one at a time)
//#define STORAGEDEBUG 1
//#define SCHEDDEBUG 1
//#define FSDEBUG 1
#define BUSDEBUG 1 //ok
//#define WIFIDEBUG 1 //uses a lot?
//#define INS_TIMING_DEBUG 1
#define HAL_I2C_INTERNAL_DEBUG 1  // Enable I2C debugging

//=============================================================================
// WIFI CONFIGURATION
//=============================================================================
#define HAL_ESP32_WIFI 1
#define WIFI_SSID "ardupilot123"
#define WIFI_PWD "ardupilot123"

//=============================================================================
// SCHEDULER CONFIGURATION - 100Hz is good balance for ESP32 with I2C sensors
//=============================================================================

#ifndef ENABLE_HEAP
#define ENABLE_HEAP 1
#endif

#define AP_SCHEDULER_DEFAULTS_MAX_LOOP_HZ 100
#define HAL_SCHEDULER_LOOP_RATE_HZ 100

//=============================================================================
// MOTOR OUTPUT CONFIGURATION
// Motor outputs (4 channels for quadcopter)
//=============================================================================
#define HAL_ESP32_RCOUT { GPIO_NUM_25, GPIO_NUM_27, GPIO_NUM_33, GPIO_NUM_32 }

//=============================================================================
// SPI CONFIGURATION - DISABLED
// SPI disabled - using I2C for all sensors
//=============================================================================
#define HAL_ESP32_SPI_BUSES {}
#define HAL_ESP32_SPI_DEVICES {}

//=============================================================================
// I2C BUS CONFIGURATION
// All three sensors share the same I2C bus
//=============================================================================
#define HAL_ESP32_I2C_BUSES \
	{.port=I2C_NUM_0, .sda=GPIO_NUM_21, .scl=GPIO_NUM_22, .speed=400*KHZ, .internal=true}

//=============================================================================
// RC RECEIVER INPUT
//=============================================================================
#define HAL_ESP32_RCIN GPIO_NUM_4

//=============================================================================
// UART CONFIGURATION
//=============================================================================
#define HAL_ESP32_UART_DEVICES \
  {.port=UART_NUM_0, .rx=GPIO_NUM_3, .tx=GPIO_NUM_1 },\
  {.port=UART_NUM_1, .rx=GPIO_NUM_16, .tx=GPIO_NUM_17 }

// Enable Filesystem support
#define AP_FILESYSTEM_ESP32_ENABLED 1

// SD Card configuration (MMC mode)
#define HAL_ESP32_SDMMC 1

// No SD card logging directories needed
#define HAL_ESP32_SDCARD 1
#define LOGGER_MAVLINK_SUPPORT 1
#define HAL_BOARD_LOG_DIRECTORY "/SDCARD/APM/LOGS"
#define HAL_BOARD_TERRAIN_DIRECTORY "/SDCARD/APM/TERRAIN"
#define HAL_BOARD_STORAGE_DIRECTORY "/SDCARD/APM/STORAGE"

// Logging configuration - Default
//#define HAL_LOGGING_BACKENDS_DEFAULT 1

// Log backend: 2=MAVLink streaming only (no SD card)
#define HAL_LOGGING_BACKENDS_DEFAULT 1

// RMT configuration
#define HAL_ESP32_RMT_RX_PIN_NUMBER 4
```

## Part 7 : Build the bin file

Now let's build our ESP32 Ardupilot bin file as follow and lets wait until the build finish
```
cd ardupilot
./waf configure
./waf configure --board=esp32buzz --debug
[ or ./waf configure --board=esp32diy --debug ]
./waf plane
or
./waf copter
```

If you encounter an error, just install the python packages such as `pexpect`, `future` and etc if any. 

## Part 8 : Crucial File.

These are important bin file that we have to flash later using `ESPtool`.
```
ardupilot.bin
bootloader.bin
partition-table.bin
```

This file can be found inside the following directory
```
cd /home/jlukas/ardupilot/build/esp32diy/esp-idf_build

../esp-idf_build/ardupilot.bin
../esp-idf_build/bootloader/bootloader.bin
../esp-idf_build/partition_table/partition-table.bin
```

## Part 9 : Go to Windows and Not in WSL

Open your terminal and install the following `esptool` packages to flash our file
```
py -3.8 -m pip install esptool
```

## Part 10 : Flash Our Bin to ESP32

This is address and config of our `bin` file obtained from `flash_args`
```
--flash_mode dio --flash_freq 80m --flash_size 4MB
0x1000 bootloader/bootloader.bin
0x10000 ardupilot.bin
0x8000 partition_table/partition-table.bin
```

Please run following command to flash our `bin` to `ESP32`. 
```
py -3.8 -m esptool --chip esp32 --port COM3 --baud 115200 write_flash --flash_mode dio --flash_freq 80m --flash_size 4MB 0x1000 D:\Shared_Folder\ESP32_Ardupilot_Bin\v13\bootloader.bin 0x8000 D:\Shared_Folder\ESP32_Ardupilot_Bin\v13\partition-table.bin 0x10000 D:\Shared_Folder\ESP32_Ardupilot_Bin\v13\ardupilot.bin
```

For G14 Laptop
```
py -m esptool --chip esp32 --port COM11 --baud 115200 write_flash --flash_mode dio --flash_freq 80m --flash_size 4MB 0x1000 bootloader.bin 0x8000 partition-table.bin 0x10000 ardupilot.bin
```

## Part 11 : Connect To Mission Planner

Open your `Mission Planner` and connect to port com

