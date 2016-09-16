---
layout: post
title: One Year of MQTT
---
We have been using MQTT in production for almost a year now. It's been a pleasant experience so far, thanks to MQTT itself and a small addition we built ourselves.

In the following, we'll assume that you have basic knowledge of MQTT. If you don't, have a look at the [MQTT Essentials](http://www.hivemq.com/blog/mqtt-essentials-part-1-introducing-mqtt) series for an introduction.

## The Good

Right from the start, we were impressed by MQTT, because it provided a lot of useful and convenient features. As a pub/sub protocol, it is a natural fit to keep multiple apps and exhibits in sync. Using WebSocket transport and the excellent [MQTT.js](https://github.com/mqttjs/MQTT.js) library, we can publish messages and subscribe to topics directly from our HTML apps. We also rely heavily on retained messages to make sure that individual apps can be started at any time and still retrieve the latest data.

## The Bad

However, when working with retained messages, we felt that using pub/sub wasn't always the right tool for the job: Sometimes, we only wanted to know the current retained message for a certain topic, not any future changes. To get the current retained message using MQTT, we had to subscribe to the topic, wait for the retained message to arrive, then unsubscribe again.

Apart from being a little cumbersome, there is a more serious problem to this approach: We cannot find out whether there is retained topic for a given topic. If we do not receive a retained message after subscribing, it's either because there is none, or because we have not waited long enough yet. After a few seconds we would be relatively confident, but still not completely sure.

There also is variation of this problem when we want to find out which topics "exist", i.e. which topics have retained messages stored in the broker. While we can subscribe to a wildcard topic like `devices/#`, we cannot tell exactly when all the retained messages have arrived.

## The Plugin

The obvious solution to the problems above is to use some kind of request/response mechanism. So we decided to build an HTTP API that would allow us to query retained messages. Luckily, the broker we use, [HiveMQ](http://www.hivemq.com), has a powerful [plugin interface](http://www.hivemq.com/docs/plugins/latest/) including support for HTTP. That made it easy to build a plugin to provide such an HTTP API.

Today, we're open-sourcing this plugin, the **Retained Message Query Plugin** for HiveMQ. You can find the source code on [GitHub](https://github.com/artcom/hivemq-retained-message-query-plugin) with ready-to-use `.jar` files in the [releases section](https://github.com/artcom/hivemq-retained-message-query-plugin/releases). For a documentation of the actual API, please refer to the [README](https://github.com/artcom/hivemq-retained-message-query-plugin#http-api).

We hope that you'll find our plugin useful. Feel free to open an issue on GitHub in case you have feedback, questions or want to contribute!

## Conclusion

For the sake of completeness, we should also mention that there are limitations, which can be summed up as "MQTT is not a database": Even with QoS 2, order is only guaranteed for messages to the same topic. The query plugin is no replacement for a query language like SQL. And most importantly, there is no support for atomic operations or transactions, i.e. publishing retained messages to multiple topics "at once". So if you need any of that, you should reach for a proper database.

That being said, MQTT and the Retained Message Query Plugin have proven to be a good fit for our needs. We'll probably use it again in future projects.
