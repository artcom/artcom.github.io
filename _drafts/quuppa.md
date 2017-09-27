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
[Quuppa](http://quuppa.com) is developed by the Finland-based company "Quuppa Oy". It is an Angle-of-Arrival based system that uses Bluetooth Low Energy advertising signals.
It can locate specialized "Beacons" as well as mobile devices that can be programmed to send custom BLE advertising packets - like nearly all current Android and iOS smartphones.
Depending on the locator density, Quuppa provides 2D or 3D indoor positioning.
They claim to reach an average position accuracy of 0.5m, under special circumstances even less. 
![Error](http://quuppa.com/wp/wp-content/uploads/2015/04/Location-error.jpg)

## Quuppa Ecosystem
### QPE
The Quuppa Positioning Engine is a server component running in Apache Tomcat that does the processing of the raw data and provides positioning data in various formats via http, UDP and file-sharing. Data output can be configured by the user (JSON, CSV)
In addition to plain posititing data, zones can be defined.
![Moving beacons](https://artcom.github.io/images/2017-09-27-quuppa/qpe.jpg)

### Locator
Locators are specizalized antennas that measure the angle of arrival of BLE devices. They must be mounted under the ceiling, free from nearby obstacles.
They are connected via ethernet and powered through PoE.
![Locator](https://artcom.github.io/images/2017-09-27-quuppa/locator.jpg)

### Focusing Locator
The focusing locator contains the same hardware as the locators but another software. It is used to determine the mounting angle of the locators.

![Focusing locator](https://artcom.github.io/images/2017-09-27-quuppa/focusing.jpg)

### Site Planner
The Quuppa Site Planner is the central planning and deployment tool. It is used for planning the distribution of the locators, for identifying the locators on location, for determining their mounting position and finally for deployment of the final project.
It's a Java Swing Application which feels a little oldschool, but it just runs on every operating system we tried and is stable and pretty ergonomic.
![Moving beacons](https://artcom.github.io/images/2017-09-27-quuppa/siteplanner.jpg)

## Deployment
For deployment, a precise site plan is essential. A laser-meter should be used to determine or verify all dimensions.
Distribution of the locators can be planned beforehand in the site-planner which provides a rough estimate of the locator range and gives a good starting point. If there are special areas where location quality is more important, this should be considered when planning the site. The closer the locators are to these locations, the better the quality of the position can be expected. 
We experienced that locator distances between 6 and 10 meters are convenient for a good coverage.
 
The most time consuming step during deployment is the mounting of the locators and the determination of their position and orientation.
The determination of the orientation (called "focusing") is done by aiming with the focusing locator towards the locators from two or more known positions in the site-plan.
After at least two measurements per locator, the site-planner gives feedback about the quality of the measurements. It needs some experience to find the right points and distances for the focusing. This step is essential for the quality of the positioning and should be done properly. 

## Experiences and measurements

Grid distance in all drawings: 1m

### Moving beacons
A beacon with highest advertising rate is moved along a defined line multiple times. The positions are recorded and drawn with Quuppa Data Player. The position measurement is best in the middle of the area where there are no obstacles between locators and beacons.
Especially at the lower end of the area, the beacon coverage is bad which results in less precision.

![Moving beacons](https://artcom.github.io/images/2017-09-27-quuppa/movingOnLine.png)

### Static beacons
Beacons with highest advertising rate (ca. 8 Hz) are placed at a static position for 15 minutes. Position is recorded an drawn with Quuppa Data Player.
The first beacon is placed between two locators. It's position is varying around 1m. The variaton is much larger along the line between the locators. This shows that vertical angle measurement seems to be less precise than horizontal angle measurement.
For the second measurment, the beacon is placed very close to the locator. The variation of position is much lower.

![Static beacons](https://artcom.github.io/images/2017-09-27-quuppa/static.png)

## Summary
Quuppa is a robust indoor positioning system which gives quiet accurate results under good conditions. It needs some experience during installation but provides reliable results in a range between 0,2 - 1m, depending on the conditions. Under good circumstances, this is close to the 0,5m claimed by Quuppa on their website. The closer to the locator, the better the results. 

### Pros
* Measurement workflow is well-guided
* Many BLE-enabled devices can act as beacon

### Cons
* Needs many locators
* Expensive hardware investments
* Locators are visible at the ceiling

## Further reading:
[Basic principles of the RF technologies for location estimation](http://www.electronicdesign.com/communications/what-s-difference-between-measuring-location-uwb-wi-fi-and-bluetooth
)

[Master Thesis - Quuppa in Hospital](https://repository.tudelft.nl/islandora/object/uuid:31da4059-5dac-4e60-93d7-36cad2d8ab46?collection=education)