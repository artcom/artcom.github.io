---
layout: post
title: 'Travels in low-power-land - Part 1'
excerpt_separator: <!--end-of-excerpt-->
tags:
- draft
---
At Cologne, we're evaluating some nice wireless hardware stuff. Here are some first insights.
<!--end-of-excerpt-->

# Part 1
Currently, we're evaluating wireless technologies for building small, coin cell-driven modules that carry serveral useful sensors like:

* Accelerometer
* Compass
* Brightness / RGB
* Capacitive touch

They are going be produced in small amounts and can be configured and used instantly to create small user interactions for several media exhibits.

**Ideas:**

* Phygital (Physical + Digital) devices like the Phygital Magazine and Book
* Things that interact with exhibits when moved, rotated, tilted
* Hidden mechanical or capacitive touch buttons

In standby mode, batteries should last several months. Since they will be used only a few times a day, wake-up duration will probably be less than one minute per day.
They should be as small as possible. Data rate is no concern since only small amounts of status data will be transferred.

We evaluated two wireless technolgies:

![image](/images/2016-7-12-low-power-land/esp_rfm_size.jpg)

*RFM69HCW (left) and ESP8266 ESP-12-E (right)*

### Espressif ESP8266

[Esp8266](http://www.espressif.com/sites/default/files/9b-esp8266-low_power_solutions_en_0.pdf) is a tiny SoC with Wifi and several GPIO-Pins. It's available on a couple of breakout boards, starting at about 2 € per piece. Many of them are made by AiThinker or Espressif There is a gcc-compiler available and the Arduino community supports the tiny module pretty well. It has only one analogue input and just a few GPIO pins. We tested a ESP8266 ESP12E Module. They have a printed antenna on the pcb itself.

#### Results 
* Very fast WiFi connection speed. After waking up from deepsleep, it connects to WPA2-Wifi with a static IP and publishes the first message in about 200ms
* Low deep sleep current of 22 uA
* Waking up from deepsleep resets RAM. Program starts from the beginning. The only way to store values between deepsleep periods is the real time clock memory.
* Very high peak current of 380mA while transmitting
* No USB interface - USB-to-Serial adapter needed for programming
 
#### Additional Features

* Over the air updates are possible, even via a tiny embedded webserver: [https://github.com/esp8266/Arduino/blob/master/doc/ota_updates/](https://github.com/esp8266/Arduino/blob/master/doc/ota_updates/)
* Bootstrapping and configuration might be done through tiny embedded webserver directly 
* Many modules have 1-4MB flash memory on board with a complete file system implementation that can be used for persisting data

Due to the high peak current, powering the ESP8266 through a Lithium coin cell is not possible - these cells are made for low 
constant currents and their voltage drops heavily when higher currents are drawn. 

##### Links

[Video: Ideal battery technology for ESP8266](https://youtu.be/heD1zw3bMhw?list=PL3XBzmAj53Rlu3Byy_GkqG6b-nwEpWku0)

[Video playlist about ESP8266](https://www.youtube.com/playlist?list=PL3XBzmAj53Rlu3Byy_GkqG6b-nwEpWku0)

[ESP8266 power consumption](http://bbs.espressif.com/viewtopic.php?f=6&t=133)


### Hope RF RFM69HCW + AtMega 

[RFM69HCW](http://www.hoperf.com/upload/rf/RFM69CW-V1.1.pdf) is a 433Mhz transmitter with integrated protocol and acknowledge handling. It can be combined with any microcontroller with SPI interface. We'll propably going to take a AtMega 32u4 that has an integrated USB stack for easy deployment and debugging.
Deep sleep current is mainly dependent on the current consumption of the microcontroller itself. The RFM module's standby current is negligible.
There are ready-made Arduino modules called Moteino with RFM69HCW as well that might be worth trying.

#### Results
* Good range throughout the whole area with 17cm wire antenna
* Adjustable power output
* Maximum current when transmitting ca. 75mA
* Fast transmission without noitceable delay
* Very reliable transmission 
* RSSI output for debugging and range estimation
* Adjustable transmitting power

![image](/images/2016-7-12-low-power-land/rfm69_power_levels.jpg)

*Current draw of different transmit power settings*

### Current measurements

To confirm the data sheets of the manufacturers, we did some own power measurements.
The current was measured with a INA129 I2C current sensor and a small Arduino sketch to read out the values. The sensor is much faster than a multimeter which isn't suitable for measuring such short peaks.
 
#### Current peaks

The ESP8266 has been sent to deepsleep - and awakes every 3 seconds. Every 8 times, Wifi is switched on, the other times, it remains off.

The RFM69HCW sends every 10 seconds a short string message with 2 retries at full transmission power. 

![image](/images/2016-7-12-low-power-land/esp_vs_rfm.jpg)

*Current draw comparison*

#### Powering that stuff

Especially the current peaks are interesting to choose the battery type. Typical lithium coin cells can't handle high current draws of several hundred milli-amperes. For higher currents, coin cell shaped Li-Ion batteries are available which have only about half of the capacity of lithium cells. The LIR2450 is much larger than the CR2032, but has less capacitiy (210mAh vs. 120mAh). The ESP8266 can only be powered through a LiIon-cell, while the RFM69HCW might be powered through a CR2032 when it's not transmitting at full power. Thus a sensor with the RFM69HCW will very likely have longer standby time while being smaller because in can be powered through a battery type with a much higher energy density.

![image](/images/2016-7-12-low-power-land/coincells.jpg)

*Size comparsion of CR2032 and LR2450 cells*


In the next part, we'll build two prototypes for testing and comparison and we'll see how they will behave in real life.

...to be continued.
