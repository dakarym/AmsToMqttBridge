
# Overview

Here are links to the different board designs together with a short summary.

## HAN_SELF_POWERED (Self contained and HAN Powered)

This [board](HAN_SELF_POWERED) contains a built in power supply to run without any external connection.  It is an evolution of the design proposed by ArnieO on the home automation forum [thread](https://www.hjemmeautomasjon.no/forums/topic/4933-lesing-av-amshan-uten-spenningsforsyning-the-complicated-way/).

Noteable changes from the below boards are:
* Uses [LTC3642](https://www.analog.com/media/en/technical-documentation/data-sheets/3642fc.pdf) for power delivery from the HAN signals
* Current limiter for preventing inrush tripping of overcurrent on HAN reader
* Voltage supervisor circuit via [TPS3808](http://www.ti.com/lit/ds/symlink/tps3808.pdf) for controlled ESP startup
* New layout with surface mount components and reduced size

Building notes:
* JP2 must be closed to enable recovery from deep sleep mode.  This is required.
* The EPS Prog/Reset circuitry is optional as the board can be programmed via the PROG and RST buttons.
* MRST button is optional, it forces a clean delay of the ESP reset.
* C7 is shown as a capacitor, but the latest version is a resistor value instead.  Next version will move to a resistor symbol.
* C6 can be omitted if desired.
* R1, R2 and R6 build custom hystersis for the TPS3808 based on [this](http://www.ti.com/lit/an/slva360/slva360.pdf) application note.

A (mostly) assembled prototype board is shown here:
![Board Front](/Images/POE_G1_assembled_front.jpg)
![Board_Back](/Images/POE_G1_assembled_back.jpg)

## HAN_ESP_TSS721 (USB Powered)

This is the current and most robust design for the [main board](HAN_ESP_TSS721). There's been great help in creating this, both here on GitHub, but also at the Norwegian home automation forum, [www.hjemmeautomasjon.no](https://www.hjemmeautomasjon.no/forums/topic/1982-lesing-av-ams-data-amshan-iot/)

The board

* uses TSS721 for MBus to TTL conversion
* holds ESP8266 for processing and transmit HAN data to WiFi / MQTT
* full schematics and PCB in editable [KiCad](http://www.kicad-pcb.org/) source files
* features a temperature sensor (DS18B20)
* uses a modular design, leaving these features optional
  * temperature sensor
  * M-bus TX
  * WiFi (Leave ESP, power supply and use for only M-bus to TTL)

### Status

At the time of this writing, this has never been tried as a whole, but all parts has been prototyped individually. First PCB prototypes are very soon in the order.

## HAN_ESP_Simple (Was: Board 1)

The project's original [board design](HAN_ESP_Simple). It

* is based on the ESP8266 chip
* is powered by USB
* uses a very simple voltage divider to demodulate the M-bus signal
* has shematic and pcb design only available as finished pdf/png files

### Status

Prototypes have been made and some people have started using them(?).

## HAN_TTL_TSS721 (Was: Board 2)

This [board design](HAN_TTL_TSS721) is a newer alternative to the original. It

* is an Arduino shield.
* uses the industry standard TSS721 chip to interface the M-bus.
* is optically isolate
* has shematic and pcb design available in editable [KiCad](http://www.kicad-pcb.org/) source files

### Status

Unfinished, just started.

## MBUS_Simulator (Was: Board 3)

This [board](MBUS_Simulator) is a M-bus master simulator to be able to develop and
test the other boards without being dependent on having and using a real AMS unit.

### Status

Implementation done.

Please also see [Getting started building or modifying](GETTING_STARTED.md)
