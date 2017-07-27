---
layout: post
title: 'Creating a Mobile Audio Experience at Changi Airport'
author: <a href="https://github.com/jkrumow/">Julian Krumow</a>
excerpt_separator: <!--end-of-excerpt-->
---
## Introduction

Recently a new kinetic installation went live in Terminal 4 at Changi Airport, Singapur. This installation is quite special, not only because of its sheer size but because it offers an audio experience too. In this post I want to discuss what how this mobile audio experience was implemented.
<!--end-of-excerpt-->

## Choreography and Music

When the kinetic installation performs its choreography by moving its golden aluminium rings across the ceiling of the airport terminal it will do this to a music composed by the islandic composer Ólafur Arnalds only for this purpose. In the terminal are multiple vantage points, equipped with speakers where travelers can rest and listen to the sound.

> !! Show an image or video of the kinetic

But there is also the possibility to listen to the music in quiet over your mobile phone using the official Changi Airport App or the web browser.

> !! Show screenshots

## Synchronization

The system was split up into three components, each provided by one partner in the project. The kinetic and its control application was providid by the builder of the kinetic MKT and the choreography running on it was programmed by ART+COM. On the other end are the apps provided by [WeesWares](http://www.weeswares.com) - a studio fom Singapur. In between sits a audio synchronization server which synchronizes the music running on the mobile devices with the choreogaphy of the kinetic installation.

![]({{site.url}}/images/changi_petal_clouds/system_setup.png)

The kinetic sends UDP packets to the audio sync server. Those packets contain the current playback position within the choreography in milliseconds. The audio sync server relays the timestamp to the mobile clients through a websocket. The mobile clients receive the timestamp and adjust the playback position of the audio material accordingly.

## Finding a Synchronization Protocol

Of course the best approach would have been to synchronize ausio sync server and clients via ntp to cope with the nondeterministic timing of data packets transmitted over the internet. Since NTP is based on UDP and most mobile network carriers block UDP traffic this would not have worked. Even the handling of regular websockets can be very difficult because of the problematic proxy configurations of the carriers.

So we decided to use websockets and a heuristic approach to deal with timing jitter. To make the websocket connection as robust as possible we used socket.io. This framework wraps websocket communication inside an own protocol, which enables a client to constantly up- or downgrade from websocket to long polling etc.

To eliminate jitter at least a bit the server sends a `sync` message to the client:

```json
{
  "time": 12347435,
  "position": 120000,
  "offset": 800
}
```

The field `position` is a timestamp in milliseconds that represents the current point in the choreography.
The field `time` describes the actual time on the server.
The field `offset` is the approximated latency between server and client. The sum of these two values is the current playback position on the client.

The client immediately acknowledges the message by returning the `time` value:

```json
{
  "time": 12347435
}
```

## Dealing with jitter

The server calculates the time it took to get the timestamp back and buffers about 10 of those lag values. To calculate the current average lag of the system first sorts all lag values and denoises them by determining the `standard deviation` and removing all values which are above `median + standard deviation`. Then all remaining values are simply averaged. This makes the system react a little slower to sudden changes in timing, but more stable.

## Building the Synchronization Server and a Demonstrator

The server was build quite quickly using node.js. The provide WeesWares with a working example I also wrote a simple iOS app to demonstrate the synchronization concept:

> !! show video of multiple demonstrators running in parallel

{% include vimeo.html id="217337340" %}

> !! Maybe an english native speaker should proof read this post?
