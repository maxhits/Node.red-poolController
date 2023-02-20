# Node.red-poolController - Version 0.0.1 - (initial build)
Pool Controller to replace Aqualink RS Controller, works with Jandy rs-485 pumps and Century V-Green rs-485 pumps.  Designed to be able to control pumps via rs-485 EPC Modbus Communication Protocol.  This is a basic Node-red implementation to control your pool and still needs significant refinement and should be used at your own risk. 

First, I have to give credit to other folks on the internet for finding reference documents contained here in detailing the "GEN3 EPC Modbus Communication Protocol" and how to use it to communicate to Century vgreen pumps and other re-branded Century pumps such as Jandy and others.  I also need to give credit to the users from the "troublefreepool.com" forum for posting how to convert a Century vgreen to accept the Jandy rs-485 protocol.  


Equipment Supported:
  - Jandy Flopro vs 1.65 pumps
  - Century vgreen 0.85/1.65 pumps
  - Relay Control Cleaner Booster pump


Requirements:
  - Computer running Node-red with TCP/IP to rtu avaiable to connect to rs-485 network (good application for a SBC like Raspberry PI)
  - Node-red Paletes used:
    - Node-red
    - node-red-contrib-eztimer
    - node-red-contrib-ui-digital-display
    - node-red-contrib-ui-led
    - node-red-contrib-ui-multistate-switch
    - node-red-dashboard
  - TCP/IP to rs-485 converter or any method to communicate rs-485 to pumps
  - Relay control module (using Automation Direct CLICK PLC for Remote I/O)

Variable Speed Pump Controls:
Below are the following commands used to control the pump, this is not a complete list of what is possible just what is currently being used.  All commands assume the Modbus address is 0x15 (default) but this can be changed per the attached document.  The pump will require the "go cmd" be sent continuously without out 60 seconds passing between commands or the pump will go into fault and stop.  The pump only requires the return of the "go cmd" to continue operating again.  This Node-red flow currently only uses the commands listed below but more are available per the attached document.

  - Start/Stop Commands:
 
![cmd-go-table](https://user-images.githubusercontent.com/104328486/220181947-189d1e18-ca7b-4f9b-a57f-ce6e74df8244.png)

  - Run Status Commands:
 
![cmd-status-table](https://user-images.githubusercontent.com/104328486/220181960-a7a79241-fe4f-4a53-a0b8-79bc7b55ba94.png)

  - Speed Demand Commands:
 
![cmd-speed-table](https://user-images.githubusercontent.com/104328486/220181978-7ce8fd86-f68a-4b9b-878e-0e29ee20520d.png)

For Modbus communication the dip switches on the pump should be set per below with all switches off unless you need 12v for your rs485 device.  

![pump dip switch](https://user-images.githubusercontent.com/104328486/220182459-9658c7fa-7820-4331-b1cf-4885da9468cc.png)
![dip-switch-table](https://user-images.githubusercontent.com/104328486/220189544-5e4f9789-2fe1-46ec-b84c-08379fd7505a.png)

Node-red flows:
Coming Soon!

Screen Shot Home Page and Setting Page:
![homeScreen](https://user-images.githubusercontent.com/104328486/220188778-a0641e7f-ad91-459f-8a55-d76ca2c1f07f.png)
![setpointScreen](https://user-images.githubusercontent.com/104328486/220188787-5ff211b0-641d-4718-bb86-3bf16220a008.png)
