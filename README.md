# Node.red-poolController - Version 0.0.1 - (initial build)
Pool Controller to replace Aqualink RS Controller, works with Jandy rs-485 pumps and Century V-Green rs-485 pumps.  Designed to be able to control pumps via rs-485 EPC Modbus Communication Protocol.  This is a basic Node-red implementation to control your pool and still needs significant refinement and should be used at your own risk. 

First, I have to give credit to other folks on the internet for finding reference documents contained here in detailing the "GEN3 EPC Modbus Communication Protocol" and how to use it to communicate to Century vgreen pumps and other re-branded Century pumps such as Jandy and others.  I also need to give credit to the users from the "troublefreepool.com" forum for posting how to convert a Century vgreen to accept the Jandy rs-485 protocol.  


## Equipment Supported:
  - Jandy Flopro vs 1.65 pumps
  - Century vgreen 0.85/1.65 pumps
  - Relay Control Cleaner Booster pump

## Requirements:
  - Computer running Node-red with TCP/IP to rtu available to connect to rs-485 network (good application for a SBC like Raspberry PI)
  - TCP/IP to rs-485 converter or any method to communicate rs-485 to pumps
  - Relay control module 

## Variable Speed Pump Controls:
    
Below are the following commands used to control the pump, this is not a complete list of what is possible just what is currently being used.  All commands assume the Modbus address is 0x15 (default) but this can be changed per the attached document.  The pump will require the "go cmd" be sent continuously without out 60 seconds passing between commands or the pump will go into fault and stop.  The pump only requires the return of the "go cmd" to continue operating again.  This Node-red flow currently only uses the commands listed below but more are available per the attached document.

  - Start/Stop Commands:
 
![cmd-go-table](https://user-images.githubusercontent.com/104328486/220181947-189d1e18-ca7b-4f9b-a57f-ce6e74df8244.png)

  - Run Status Commands:
 
![cmd-status-table](https://user-images.githubusercontent.com/104328486/220181960-a7a79241-fe4f-4a53-a0b8-79bc7b55ba94.png)

  - Speed Demand Commands:
 
![cmd-speed-table](https://user-images.githubusercontent.com/104328486/220181978-7ce8fd86-f68a-4b9b-878e-0e29ee20520d.png)

  - Read Sensors from Unit:
  
![cmd-sensors-table](https://user-images.githubusercontent.com/104328486/220202703-0fd5c7f1-c8d2-467a-9e9e-c8e811f50ee6.png)


For Modbus communication the dip switches on the pump should be set per below with all switches off unless you need 12v for your rs485 device.  

![pump dip switch](https://user-images.githubusercontent.com/104328486/220182459-9658c7fa-7820-4331-b1cf-4885da9468cc.png)
![dip-switch-table](https://user-images.githubusercontent.com/104328486/220189544-5e4f9789-2fe1-46ec-b84c-08379fd7505a.png)

## SBC configuration (Raspberry Pi or other):
The below implementation I have verified to work for my installation.  This should work great with a Raspberry PI but due to current costs I have implemented with a libre SBC.  I have added links to the equipment used.  

  - Libre Computer Board AML-S905X-CC
  https://www.amazon.com/dp/B074P6BNGZ?psc=1&ref=ppx_yo2ov_dt_b_product_details
  - PiRelay EXPANSION BOARD
  https://www.amazon.com/dp/B077LV4F1B?psc=1&ref=ppx_yo2ov_dt_b_product_details
  - Analog to Digital Converter Module 16 Bit ADS1115 
  https://www.amazon.com/dp/B07GGY7WX7?psc=1&ref=ppx_yo2ov_dt_b_product_details
  - DEVMO 2PCS CH340 chip USB to RS485
  https://www.amazon.com/dp/B07TB5WVF4?psc=1&ref=ppx_yo2ov_dt_b_product_details
  - GeeekPi 2x20 40 Pin Stacking Female Header Kit
  https://www.amazon.com/gp/product/B08GC18NMK/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1
  - 4-20ma to 3.3v converter
  https://www.amazon.com/dp/B07K5M4VFR?psc=1&ref=ppx_yo2ov_dt_b_product_details
  - 24V DC 4-20mA -50C to 150C PT100 Temperature Sensor Transmitter 
  https://www.amazon.com/dp/B00GN6X25U?psc=1&ref=ppx_yo2ov_dt_b_product_details
  - Waterproof RTD PT100 Temperature Sensor
  https://www.amazon.com/dp/B07DP3LYPX?psc=1&ref=ppx_yo2ov_dt_b_product_details
  
Flash a copy of Raspberry PI Raspian image per the Libre website and install Node-red per Raspberry/Debian script from the Node-red website.  https://nodered.org/docs/getting-started/raspberrypi

    $ bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)

    $ sudo systemctl enable nodered.service

    $ sudo systemctl start nodered.service

Add context storage ability to Node-red

    $ cd ~.node-red
    
    $ nano settings.js
    
Uncomment contextStorage and add storeInFile per below:

```
contextStorage:{
    storeInFile: {module:"localfilesystem"},
    default: {module:"memory"},
},
```
Save and close file.  After making these changes its easiest to reboot to make changes take effect.  

    $ sudo reboot

Add gpiod for controlling relay board through 40 pin header

    $ sudo apt install gpiod -y

Enable i2C-ao to start at boot up.  i2c is needed to communicate with the ADS1115.  This should be easier with a Raspberry PI and can be done through raspi-config; but the Libre Le Potato does not use this system.  I am sure there is a better way to do this but this worked for me.  

    $ sudo ldto enable i2c-ao

    $ sudo i2detect -y 1

You should see address 0x49 or the address you assigned to the ADS1115 module via the jumpers on the board.  If you see the board there then next you need to make a service to enable the overlay at boot and make sure Node-red starts after that service.  

    $ cd /lib/systemd/system

    $ sudo nano i2c.service

Paste the following into the new file, save and exit.  

```
[Unit]
Description=enable i2c-ao

[Service]
# Before=nodered.service
RemainAfterExit=yes
ExecStart=/bin/bash ldto enable i2c-ao

[Install]
WantedBy=multi-user.target
```
Now you need to enable the service, so it will run next time at reboot.  

    $ sudo systemctl enable i2c.service

Now you need to add a line to the Node-red service so it waits for the i2c.service to start before starting up.  

    $ sudo nano nodered.service

Add the following line below [Service]

    After=i2c.service

Save file and close.  You should now have all modules needed for the 40 pin header to control the relay hat and the analog hat.  

The Node-red flow uses the following palettes.  
  - node-red-dashboard
  - node-red-contrib-ui-multistate-switch
  - node-red-contrib-eztimer
  - node-red-contrib-ui-digital-display
  - node-red-contrib-ui-led
  - node-red-contrib-moment
  - node-red-contrib-ads1x15_i2c
  
The flow assumes the following dashboard setup but is not limited to the setup.  
  - Home
    - Main Pump
    - Waterfall Pump
    - Aux Equipment
    - Temp Trends
  - Light Control
    - Light Control Panel
  - Runtime Schedule
    - Main Pump
    - Waterfall Pump
    - Cleaner Pump
  - Set Points
    - Temp Setpoints
    - Pump Setpoints
    - Ramp Up Setpoints
    

# To Do items

  - Still need to fine tune and rework lighting control
  - Still need to develop Heater control
  - Bug search
  
