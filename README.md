# Node.red-poolController - Version 0.0.1
Pool Controller to replace Aqualink RS Controller, works with Jandy rs-485 pumps and Century V-Green rs-485 pumps.  Designed to be able to control pumps via rs-485 EPC Modbus Communication Protocol.  This is a basic Node-red implementation to control your pool and still needs significant refinement and should be used at your own risk. 

First, I have to give credit to other folks on the internet for finding reference documents contained here in detailing the "GEN3 EPC Modbus Communication Protocol" and how to use it to communicate to Century vgreen pumps and other re-branded Century pumps such as Jandy and others.  I also need to give credit to users from the "troublefreepool.com" forum for posting how to convert a Century vgreen to accept the Jandy rs-485 protocol.  


Equipment Supported:
  - Jandy Flopro vs 1.65 pumps
  - Century vgreen 0.85/1.65 pumps
  - Relay Control Cleaner Booster pump


Requirements:
  - Computer running Node-red (good application for a SBC like Raspberry PI) w/ Paletes:
    - Node-red
    - node-red-contrib-eztimer
    - node-red-contrib-ui-digital-display
    - node-red-contrib-ui-led
    - node-red-contrib-ui-multistate-switch
    - node-red-dashboard
  - TCP/IP to rs-485 converter or any method to communicate rs-485 to pumps
  - Relay control module

Variable Speed Pump Commands:
Below are the following commands used to control the pump, this is not a complete list of what is possiable just what is currently being used.  All commands assmue the modbus address is 0x15 (default) but this can be changed per the attached document.  The pump will require the "go cmd" be sent continuosly without out 60 seconds passing between commands or the pump will go into fault and stop.  The pump only requires the return of the "go cmd" to continue operating again.  This flow currently only uses the commands listed below but more are avaiable per the attached document.  

