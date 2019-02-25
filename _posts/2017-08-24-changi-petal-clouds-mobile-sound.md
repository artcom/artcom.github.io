---
layout: post
title: 'Creating a Mobile Audio Experience at Changi Airport'
author: <a href="https://github.com/jkrumow/">Julian Krumow</a>
image: /images/changi_petal_clouds_mobile_sound/2017_ArtCom_Petalclouds_ChangiT4_08954_web-1360x765.png
excerpt_separator: <!--end-of-excerpt-->
---
Recently a new kinetic installation went live in Terminal 4 at Changi Airport, Singapore. This installation is quite special, not only because of its mechatronic complexity and sheer size but because it offers an audio experience too. In this post I want to describe how this mobile audio experience was implemented.

![]({{site.url}}/images/changi_petal_clouds_mobile_sound/2017_ArtCom_Petalclouds_ChangiT4_08954_web-1360x765.png)

<!--end-of-excerpt-->

For the artistic, non-technical description of the project please see the official [artcom project page] and [Changi Airport Video]. The post focuses on the audio sync features of the installation.

[artcom project page]: https://artcom.de/en/project/petalclouds/
[Changi Airport Video]: https://www.youtube.com/watch?v=OgMo6ZBc5lw

## Choreography and Music

When the kinetic installation performs its choreography by moving its golden aluminium rings across the ceiling it will do this to a music composed only for this purpose by islandic composer [Ólafur Arnalds]. On the terminal floor are multiple vantage points - areas equipped with speakers where travelers can watch the choreography and listen to the music.

[Ólafur Arnalds]: http://olafurarnalds.com/

Aside from the fixed locations, travellers can also listen to the music in quiet over their mobile phone from within the iChangi App([iOS], [Android]) anywhere in the terminal. 

[Android]: https://play.google.com/store/apps/details?id=com.changiairport.cagapp&hl=en
[iOS]: https://itunes.apple.com/us/app/ichangi/id391730848?mt=8

## Synchronization

The system was split up into three components, each provided by one partner in the project. The kinetic and its control application was provided by the builder of the kinetic [MKT]. All choreography running on the kinetic was programmed by ART+COM. On the receiving side are the mobile apps provided by [WeesWares] - a studio fom Singapore. In between sits the audio synchronization server by [artcom] which synchronizes the music running on the mobile devices with the choreography of the kinetic installation.

[artcom]: http://artcom.github.io/
[MKT]: http://www.mkt-ag.de/
[WeesWares]: http://www.weeswares.com
![]({{site.url}}/images/changi_petal_clouds_mobile_sound/system_setup.png)

The kinetic sends UDP packets to the audio sync server. Those packets contain the current playback position within the choreography in milliseconds. The audio sync server relays the timestamp to the mobile clients through a websocket. The mobile clients receive the timestamp and adjust the playback position of the audio material accordingly.

## Finding a Synchronization Protocol

One obvious and optimal approach could have been to synchronize audio sync server and clients via [ntp] to cope with the nondeterministic timing of data packets transmitted over the internet. Since NTP is based on UDP and most mobile network carriers block UDP traffic this would not have worked. Even the handling of regular websockets can be very difficult because of the sometimes problematic proxy configurations of some carriers.

[ntp]: https://en.wikipedia.org/wiki/Network_Time_Protocol

So we decided to use websockets and a heuristic approach to deal with timing jitter. To make the websocket connection as robust as possible we used [Socket.IO](https://github.com/socketio). This framework wraps websocket communication inside an own protocol, which enables a client to easily up- or downgrade from websocket to long polling etc.

To eliminate jitter at least a bit the server sends a `sync` message to the client:

```json
{
  "time": 1477994400,
  "position": 120000,
  "offset": 800
}
```

The field `time` describes the actual time on the server.
The field `position` is a timestamp in milliseconds that represents the current point in the choreography.
The field `offset` is the approximated latency between server and client. The sum of these two values is the current playback position on the client.

The client immediately acknowledges the message by returning the `time` value:

```json
{
  "time": 1477994400
}
```

## Dealing with Jitter

The server calculates the time it took to get the timestamp back and buffers about 10 of those lag values. To calculate the current average lag of the system it first sorts all lag values and denoises them by determining the `standard deviation` and removing all values which are above `median + standard deviation`. Then all remaining values are simply averaged. This makes the system react a little slower to sudden changes in timing, but more stable.

## Building the Synchronization Server and a Demonstrator

The server was built quite quickly using [Node.js]. To provide [WeesWares] with a working example I also wrote a simple iOS app to demonstrate the synchronization concept:

{% include youtube.html id="FOerLjsKbfQ" %}

_In case you're have further questions about this app or are in general interested in our kinetic programming and otherwise please do not hesitate to [mail us]._

have fun

[Node.js]: https://nodejs.org/en/
[mail us]: mailto:dev@artcom.de
