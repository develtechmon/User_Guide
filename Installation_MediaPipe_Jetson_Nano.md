# Getting Started

## This userguide explain how to integrate Mateksys Optical Flow and Lidar module with Pixhawk flight controller

1. Solder Mateksys module with Male conenctor
2. Use below Hardware and Pin Configuration between Mateksys module and Pixhawk
```
Mateksys    Pixhawk
RX      ---  Tx
TX      ---  RX
GND     ---  GND
VCC     ---  VCC
```
3. Open Ardupilot and make a backup of existing configuration. Save into desktop
4. In Ardupilot open "Full Parameter List" and use below configuration
```
SERIALn_PROTOCOL = 32 'n' is serial number of UART in ArduPilot. In my case i'm using SERIAL1_PROTOCOL
SERIAL1_BAUD = 115
RNGFND1_TYPE = 32
FLOW_TYPE = 7

Below is setting for Lidar in this module
RNGFND1_MIN_CM = 20cm
RNGFND1_MAX_CM = 800cm

Next measure the distance between the lidar to ground. For example 3cm. Then use this value for configuration below
RNGFIND1_GNDCLEAR = 3cm

To change orientation dowward, set parameter below
RNGFND1_ORIENT = 25 (Down Facing Lidar)
```

5. From Ardupilot main page, find and observe if `Sonarrange" show some changes. It should display 3cm to ground by default
6. To fly: Set ARM and Change to Altitude hold
