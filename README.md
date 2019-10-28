# AMS <-> MQTT Bridge
 This project may help you build your own reader for HAN data streamed from the new AMS electrical meters installed in Norway in 2017/2018. Data is read using an Arduino-programmed ESP8266 (in an ESP12 module from AI Thinker), then pushed to MQTT over WiFi. You can have full control over this data, and you can basically do anything with it. 

 This project does not include any fancy UI setup or even storage for the AMS data.

 *Building this project will require some skills in ordering and assembling  electronic circuits as well as programming, and I have not included detailed instructions to take a beginner through the steps. I still hope some still find information here useful, maybe also for other projects.*

## Self powered HAN reader -- Work in progress

This is an update for a new self powered HAN [PCB](/PCB/KiCAD/HAN_SELF_POWERED) as originally conceived by ArnieO on the home automation [thread](https://www.hjemmeautomasjon.no/forums/topic/4933-lesing-av-amshan-uten-spenningsforsyning-the-complicated-way/).  Details on the hardware change is on the PCB [README](/PCB/README.md).  This version does not need a USB power connection.  Instead energy is taken from the HAN port connection and stored for use on the board.  A DC-DC buck converter based on the LTC3642 provides 3.3V at ~35ma from the 24V supply.  

The design has been tested and running successfully on an Aidon meter for the last 3 months.  Currently testing is pending on Kamstrup and Kafia style meters.

#### Open items / Limitations:
* The current that the buck can provide is limited to the maximum power supported by the meter HAN port.  The maximum value varies based on the meter type.  More information can be found [here](/Debugging/Documentation).  Currently the Aidon has the highest power available, but also has the fastest list frequency of 2.5 seconds.  Kamstrup and Kafia are at 10 second lists which may be beneficial as more time can be spent in low power idling.
* The most critical time for energy starvation is during the inital startup.  If the wifi or MQTT connection takes too long, the device will drop below the 2.65V cutoff for the voltage supervisor and will restart.  
* Due to this, the following edits in the [sketch](/Arduino Code/AmsToMqttBridge/AmsToMqttBridge.ino) were made:
  * Elimination of the 5 second waiting for AP time
  * Checking of the Vcc level at bootup and deepsleeping if insufficient voltage
  * Checking of the Vcc before entering the read message loop
* Fine tuning of the peak current trip threshold on the buck converter to balance average power draw with minimizing peak currents.
* Fine tuning of input current surge limiters for different meter types.  To be tested/verified soon.
  * Currently the design uses an NSI45020 as a 20 ma current clamp.  Below is a screenshot of the input surge with discharged input capacitors.  Yellow is the input current with 0.1V per ma scaling.  Cyan is input voltage.  Note the peak current clamped at ~27ma.
![NSI45020_Inrush](/Images/Inrush_current_voltage_NSI45020.png)
  * Alternatively an NPN current clamp could be used.  Such an example is shown below with the same measurements and scaling as above.  Note the peak current clamped at ~8.5ma.
  ![NPN_Inrush](/Images/Inrush_current_voltage_NPNclamp.png)
* OTA updates will likely not be possible with this design, but will need testing
* Inital configuration of Wifi credentials and meter type will need to be done when connected to an external 3.3V supply.
* The auto reset funtionality was implemented based on the circuit and testing of Kevin Darrah and explained [here](https://youtu.be/HdHzxM6fEig).  This funtionality has not been tested on the board.  Manually entering bootloader mode via the PROG and RST buttons is working.

#### Next Steps:
* Test and verify on other meter types
* Respin PCB design to correct changes as needed
* Reduce cost of BOM and limited value components
* Share project on PCBWay
* Design optional case for 3d printing



## The previous hardware
![The HAN Reader Hardware](./Images/HanReaderInEnclosure.PNG)

*The completed board mounted in a [3D printed enclosure](/Electrical/HAN_ESP_TSS721/enclosure)*

![The HAN Reader Installed](./Images/HanReaderConnected.PNG)

*...installed with the electrical meter...*

![Data from MQTT displayed on a Node Red Dashboard](./Images/NodeRedScreen.PNG)

*...and, showing data on a [Node Red](https://nodered.org/) dashboard*


## Background
The purpose of this project is to collect information and build a simplified bridge for reading serial DLSM/M-bus information from electrical power meters (AMS), provided over the HAN port, and publishing to some IoT friendly target.

Components will be ESP8622, Arduino code and an M-bus <-> 3.3V serial interface

As a start, we should try to get information from the three types of AMS meters currently being installed in Norway. Some details about these are available here: [NVE_Info_kunder_HANgrensesnitt.pdf](./Debugging/Documentation/NVE_Info_kunder_HANgrensesnitt.pdf)

Quite some work was put into parse and guess how the data protocol was defined. Unfortunately, I did not have access to the (rather expensive) standardization documentation, but with some collaborative effort we solved most details.

### This project includes

- [X] [Circuit to read MBus data, parse the DSLM and report to MQTT over WiFi](./PCB/KiCAD/HAN_ESP_TSS721)
- [X] [Code to capture and analyze data from a PC](./Debugging/Code/HanDebugger)
- [X] [Code to capture and analyze data from Arduino](./Debugging/Code/ESPDebugger)
- [X] [Sample data from various meters](./Debugging/Samples)
- [X] [Documentation on HAN / MBus / DLMS/COSEM](./Debugging/Documentation)
- [X] [Code to parse DLMS data into a structure](./Arduino%20Code/Arduino%20Libraries/HanReader/src)
- [X] [Real schematics, including ESP8266](./PCB/KiCAD/HAN_ESP_TSS721#schematics)
- [X] [PCB layout](./PCB/KiCAD/HAN_ESP_TSS721#pcb)
- [X] [Component list](./PCB/KiCAD/HAN_ESP_TSS721#componenet-list)
- [X] [Arduino library](./Arduino%20Code)
- [X] [Complete Arduino sketch to read values and report to MQTT server](./Arduino%20Code/AmsToMqttBridge)

[The completed project](./Arduino%20Code/AmsToMqttBridge) also includes:

- Boot as Access Point and web server to allow config from a web browser. This allows you to set SSID, password, MQTT server and port, as well as topics for publish and subscribe
- On-board temperature sensor, also reported in json to MQTT

## Electrical design

The [most recent design](/PCB/KiCAD/HAN_ESP_TSS721) uses an [ESP8266](http://esp8266.net/) in an ESP12 module from [AI Thinker](https://www.ai-thinker.com) for micro processing and the [TSS721](http://www.ti.com/product/TSS721A) from Texas Instruments for level conversion from M-Bus to TTL (3.3V). An earlier design solved the level conversion using an op-amp, which might be an easier and more available solution, but also more error-prone. See more details in [electrical design](./Electrical).
