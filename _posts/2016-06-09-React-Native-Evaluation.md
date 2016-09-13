---
layout: post
title: React Native Evaluation @ ART+COM
---

Development Journey, JUN 2016

A basic knowledge about React, Redux and React Native is assumed.

[https://github.com/artcom/running-app](https://github.com/artcom/running-app)

[https://play.google.com/store/apps/details?id=com.artcom.runwalk](https://play.google.com/store/apps/details?id=com.artcom.runwalk)

## Preface

In our daily work we are experimenting with exhibits that are accompanied by mobile experiences. We have ample expertise with iOS and Android native development. But our recent technological focus on the React/Redux front-end stack made us curious to see if React Native can be an alternative for us.

### Goal

We started by sketching the idea of a small interval timer app which consists of two screens:

1. Configuration screen
	* The user can set the overall number and duration of alternating run and walk phases
	* A start button activates the timer screen
2. Timer screen
	* Shows the elapsed time of the current phase in a circular timer
	* Has an interval counter
	* Has the option to add intervals
	* Shows total time
	* A vibration notifies the user about phase changes, even when the mobile is switched off.

We wanted to find out how many code of the application could actually be shared. We did that by building a basic version of the app that ran on both platforms without specific adaptations. After that we focused on Android, because it is less supported than iOS. We skipped iOS polishing and publishing, since it would not add significant insights to our evaluation.

We expected React Native to provide us with:

* cross-platform abstractions for common UI widgets like text, slider or image
* wrapper for platform specific UI widgets like Android floating action buttons

#### Final Android Screens

<img style="width: 32%;" src="{{ site.baseurl }}/images/2016-6-9-react-native-evaluation/rn_android_configuration_final.png"/>
<img style="width: 32%;" src="{{ site.baseurl }}/images/2016-6-9-react-native-evaluation/rn_android_configuration_picker_final.png"/>
<img style="width: 32%;" src="{{ site.baseurl }}/images/2016-6-9-react-native-evaluation/rn_android_timer_final.png"/>

## Setup & First Steps

The project setup worked smoothly and we could run a "Hello World!" app on iOS and Android emulators and devices within an hour. The next step was to build the business logic and a basic UI which runs on both platforms out of the box.

### Model

We implemented the business logic with Redux. The experience to use our well understood Redux data modeling in this context was very rewarding.

### Views

The basic version of the app was comprised of the following UI widgets: text, picker, button and a way to navigate between screens. Our initial expectation was that React Native would show its potential and provide UI abstractions or JavaScript wrapper for the UI widgets we needed.

The `<Text>` component could be used as expected but there was no appropriate support for picker widgets. Instead of the picker, we decided to fall back to a `<Slider>` component to support both platforms with a single abstraction. To our surprise it also turned out that there was no generic button abstraction. Eventually we used a clickable text as button:

```
TouchableHighlight onPress={ () => dispatch(start()) }>
  <Text>Start</Text>
</TouchableHighlight>
```

#### Navigation

There is also no silver bullet for screen navigation with React Native. We worked our way through the provided core modules `<NavigatorIOS>`, `<Navigator>` (both outdated) and `<NavigatorExperimental>`. Additionally, there were a couple of community libraries trying to fix the issues with the core modules. None of the solutions seemed promising to us. `<NavigatorExperimental>` might fill the gap, but the implementation is ongoing. In the end we decided to wait for a single mature solution. For our purpose it was acceptable to avoid navigation at all and finialize without navigation bar and transition animations.

#### Styling

React Native provides a CSS flexbox JavaScript abstraction to style views. It supports styling for different screen resolutions and form factors. While it was generally comfortable to style the views on this abstraction level, it was cumbersome to get the respective documentation. E.g. the official documentation lists the supported properties without further explanation or examples.

### Result

The app was running with full functionality and a basic unified UI on both platforms.

#### Android & iOS Screens

<img style="width: 24%;" src="{{ site.baseurl }}/images/2016-6-9-react-native-evaluation/rn_android_basic_configuration.png"/>
<img style="width: 24%;" src="{{ site.baseurl }}/images/2016-6-9-react-native-evaluation/rn_android_basic_timer.png"/>
<img style="width: 24%;" src="{{ site.baseurl }}/images/2016-6-9-react-native-evaluation/rn_ios_basic_configuration.png"/>
<img style="width: 24%;" src="{{ site.baseurl }}/images/2016-6-9-react-native-evaluation/rn_ios_basic_timer.png"/>

## Android Native Look & Feel

The source code started to diverge with the realization of Material Design for Android.

### Configuration Screen

Since React Native does not provide an appropriate picker component, we decided to implement a wrapper to access the native Android picker. We found it quite convenient to bridge methods and access Java code from JavaScript with the `NativeModules.js` / `ReactContextBaseJavaModule.java` possibility. A bridged method opens an Android `DialogFragment` in front of all other views. The selected values are returned nicely to the JavaScript side via a `com.facebook.react.bridge.Promise` object.

Alternatively we could have re-implemented the Material Design picker in JavaScript using basic view components. We did exactly that for the Material Design start button, though we skipped the tedious work to adapt the design in detail. We had hoped for a usable React Native abstraction or a wrapper, but were disappointed.

### Timer Screen

The timer screen consists of a number of texts and a stop-watch like circular timer. We realized the latter with a native view which uses Androids canvas module to draw primitives, because there was no appropriate abstraction in React Native. As with the picker, we had to do the main part of the coding in Java. Since the timer would be integrated into the layout, it needed to be implemented as React Native Component to be part of the view hierarchy.

Bridging the Java code with the `requireNativeComponent.js` / `SimpleViewManager.java` option was also refreshing. You can specify expected props and work with them on the Java side.

```
<NativeTimerCircle { ...timerCircleProps } />
```

### Vibration in the Background

Since React Native builds upon the `ReactNativeActivity` the React Native timers invoking the JavaScriptCore engine pause when the app is put to background or the screen locks. To achieve the background vibration feature, we also had to implement an Android Java solution. We created a combination of `AlarmManager`, `WakefulBroadcastReceiver` and `IntentService` that we bridged with the `ReactContextBaseJavaModule.java` option. Eventually the service invoked the vibration even if the main activity was paused.

## Summary

Compared to native development we appreciated live reloading when working on the JavaScript side. Hot reloading seems unstable though.

The Documentation is quite sparse, so we often traced the React Native source code.

The community is vivid and many small libraries pop up. However, a lot are outdated or have few contributors. We also got the impression that the maintainers often had a strong JavaScript background. This leads to libraries which were focusing on recreating native UI elements using basic views in javascript instead of using the available native modules.

Besides apps of Facebook, there are no top React Native apps in production.

React Native provides only a few cross-platform UI abstractions and platform specific UI wrapper. We had to write most views in Android Java and bridge them to JavaScript. The bridging had little overhead and worked conveniently.

We find that React Native is a constantly changing, rapidly moving target, with little best practices at hand. We were most comfortable with the implementation of the business logic in solid Redux. However, "Learn once - write anywhere" was not true for us, because most views and the background handling were implemented in Java and bridged to JavaScript.

We also discussed if it is reasonable to expect equal UI abstractions for iOS and Android. Because of the two different native UI paradigms, we doubt that can be managed. That means UI source code will diverge heavily as it comes to platform specfifc requirements. We had not expected that, when we started the journey; but it is pretty obvious now.

It seems the common assessment that React Native is very promising, and we agree. But right now, it's not mature enough for our taste. When more abstractions and  wrapper for native UI widgets appear, best practices emerge and the code stabilizes, we will have a look again.
