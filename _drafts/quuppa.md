---
layout: post
title: 'No more hide-and-seek? Impressions of the Quuppa-Indoor-Location System'
author: <a href="https://github.com/elRadish/">Andreas Rettig</a>
excerpt_separator: <!--end-of-excerpt-->
---

Indoor navigation is still a undecided game. 
Outside of buildings GPS, Glonass and Galileo are the big players, all of them based roughly on the same principles, often assisted by technologies like A-GPS or Googles Geolocation API, which merely aim on speeding up the technology for the user. 
But as soon as we enter a building and get out of reach of the sattelites' signals, things are becoming surprisingly complicated.
We evaluated and installed a indoor positioning system called "Quuppa". Here's a summary of our experiences.
<!--end-of-excerpt-->

# Indoor location technologies
The most indoor location technologies can be categorized into the following groups. This gives a very rough bird's eye view over these categories.

## RSSI based
Received Signal Strength Indicator is a general term for the strength of a received wireless signal. Since signal strength becomes lower with higher distance from the sender, it can be and indicator of distance. But there are many other factors influencing RSSI, like obstacles, people, humidity etc.
Most system use the signal strength of multiple WiFi or Bluetooth senders with known location and so some triliteration. Since RSSI is not enough for calculating a precise location, they often use more advanced approaches like fingerpriting the RSSIs of a location, combine it with data from other sensors (magnetometer, movement...) etc.

## Time based
A much more precise indicator for the distance between sender and receiver is the time, the signal takes from sender to receiver or for the whole round trip. Not all radio signals have optimal characterisitcs for that technique. There is noise, there are reflections etc. which makes is hard to detect the precise arrival of a signal. Roughly speaking, narrow band signals like WiFi and Bluetooth are less suitable than wide band signals like UWB. The wireless technologies that are used in smartphones and computers currently are transmitting on a narrow band and thus aren't ideal for time based location estimation.
These techniques are catgorized as "Time-of-flight" or "Time-of-arrival" techniques.

## Angle based
Another approach is to measure the angle from which a radio signal is "seen". This is called "Angle-of-arrival". The angle is much less influenced by obstacles (but it is) and independent from RSSI. This approach needs specialized antenna arrays for measuring the angle.

# Quuppa 
[Quuppa](http://quuppa.com) is developed by the Finland-based company "Quuppa Oy". They're providing an Angle-of-Arrival based system that uses Bluetooth Low Energy signals.
It can locate specialized "Beacons" as well as mobile devices that can be programmed to send custom BLE advertising packets - like nearly all current Android and iOS smartphones.

## Quuppa Ecosystem
### QPE
The Quuppa Positioning Engine is a server component running in Apache Tomcat that does the processing of the raw data and provides positioning data in various formats.

### Locator
Locators are specizalized antennas that measure the angle of arrival of BLE devices. They must be mounted under the ceiling, free from nearby obstacles.
They are connected via ethernet and powered through PoE.

### Focusing Locator
The focusing locator contains the same hardware as the locators but another software. It is used to determine the mounting angle of the locators.

### Site Planner
The Quuppa Site Planner is the central planning and deployment tool. It is used for planning the distribution of the locators, for identifying the locators on location, for determining their mounting position and finally for deployment of the final project.
It's a Java Swing Application which feels a little oldschool, but it just runs on every operating system we tried and is stable and pretty ergonomic.

## Deployment

For deployment, a precise site plan is essential. A laser-meter should be used to determine or verify all dimensions.
Distribution of the locators can be planned beforehand in the site-planner which provides a rough estimate of the locator range and gives a good starting point. If there are special areas where location quality is more important, this should be considered when planning the site. The closer the locators are to these locations, the better the quality of the position can be expected. 
We experienced that locator distances between 6 and 10 meters are convenient for a good coverage.
 
The most time consuming step during deployment is the mounting of the locators and the determination of their position and orientation.
The determination of the orientation (called "focusing") is done by aiming with the focusing locator towards the locators from two or more known positions in the site-plan.
After at least two measurements per locator, the site-planner gives feedback about the quality of the measurements. It needs some experience to find the right points and distances for the focusing. This step is essential for the quality of the positioning and should be done properly. 

## Experiences



Further reading:
[Basic principles of the RF technologies for location estimation](http://www.electronicdesign.com/communications/what-s-difference-between-measuring-location-uwb-wi-fi-and-bluetooth
)