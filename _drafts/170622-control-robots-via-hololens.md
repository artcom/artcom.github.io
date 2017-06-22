---
layout: post
title: EV3ControllAR - Control robots via the Microsoft HoloLens
author: <a href="https://github.com/KReguieg/">Khaled Reguieg</a>
excerpt_separator: <!--end-of-excerpt-->
---

The near future creates a need of a more natural interaction with robots.
Interacting with gestures and voice commands is a difficult topic in the
industry in general. So we tried to tackle this issue in the our holodeck.

![]({{site.url}}/images/2017-06-22-control-robots-via-hololens/lego-prosthetic-arm.jpg)

<!--end-of-excerpt-->

Interacting with robots should fit the general terms of human computer interaction standards:
- tolerance in failure
- fallbacks for actions
- support the user understanding the controls
- help and documentation
- aesthetic and minimalist design
- consistency and standards
- error prevention
- match between system and the real world

You can reread these heuristics [here](https://www.nngroup.com/articles/ten-usability-heuristics/).
The controls of the Microsoft HoloLens might not fit those needs
so far. But nevertheless I jumped into Unity and designed and application
for my bachelors degree.




# Idea

I asked my self: How will humans work together with robots in the near future?
Will there be touchpads and controlsticks on remotes like today
or will the way of interacting change completely with the rise of
new technologies? And will humans be able to to adapt to this new way
of interacting with machines?

So I thought let's determine this by creating a prototype and test the application
with users in a thinkaloud test.


# Tools

### Unity

A great game engine any dev knows and the tool of choice when
creating applications for the HoloLens. (Haha it is so far the only tool)

### MonoBrick Communication Library

> The [MonoBrick](http://www.monobrick.dk/) communication library is a LEGO 
> Mindstorms communication library written in C# that works with the standard firmware 
> on both the EV3 and NXT. 
> MonoBrick communication library runs on a wide variety of operating systems 
> including Linux, Mac OS and Windows. The library can communicate with the brick 
> using either USB, Bluetooth or WiFi(only EV3).

### LEGO Mindstorms EV3

> The LEGO Mindstorms series of kits contain software and hardware to create 
> customizable, programmable robots. They include an intelligent brick computer
> that controls the system, a set of modular sensors and motors, and LEGO
> parts from the Technic line to create the mechanical systems.

In my case it was the substitution for a big industry machine.

### Setup
- First setup your Unity so you can create and build projects for the Microsoft HoloLens
- You can find all the information needed [here](https://www.microsoft.com/de-de/hololens/developers)
- Then download and import the MonoBrick Communication Library into your Unity projects
- Create a augmented interface for your robot
- Jump into code and assemble all parts:

### Design and information flow

Think about how the information flows in the application before jumping into the code.

![]({{site.url}}/images/2017-06-22-control-robots-via-hololens/pipeline.png)

How should the UI look and what needs to be shown in AR?
![]({{site.url}}/images/2017-06-22-control-robots-via-hololens/interface-wireframe.png)

Create the UI from the wireframe.

![]({{site.url}}/images/2017-06-22-control-robots-via-hololens/holo-interface.jpg)

Next jump into the code and try to build an app based on the information flow.

### Implementation
- Handle the voice and gesture inputs from the HoloLens
#### HandsManager.cs
```csharp
1 [...]
2 InteractionManager.SourceDetected += InteractionManager_SourceDetected;
11 [...]
12 private void InteractionManager_SourceDetected(InteractionSourceState hand) {
13   HandDetected = true;
14 }
```
- Detect hands and sources pressed
#### GestureManager.cs
```csharp
1 /// Instantiate the NavigationRecognizer.
2 NavigationRecognizer = new GestureRecognizer();
3
4 /// Add Tap and NavigationX GestureSettings to the 
  /// NavigationRecognizer ’s RecognizableGestures .
5 NavigationRecognizer.SetRecognizableGestures(GestureSettings.Tap | GestureSettings.NavigationX);
```
- Define which gestures should be recognized
#### GestureAction.cs
```csharp
1 [...]
2 public float TranslationSensitivity = 10.0 f;
3 private float translationFactor ;
4 [...]
5
6 void Update (){
7   PerformTranslation ();
8 }
9
10 private void PerformTranslation () {
11   if(GestureManager.Instance.IsNavigating && HandsManager.Instance.FocusedGameObject == gameObject) {
12     // Calculate translationFactor based on GestureManager ’s NavigationPosition .X and multiply by TranslationSensitivity.
13     // This will help control the amount of translation.
14     translationFactor = GestureManager.Instance.NavigationPosition.x;
15
16     // Restrict the translationFactor to a value between -1 and 1, so the robot doesn ’t get to fast.
17     float clampTranslation = Mathf.Clamp(translationFactor , -1f, 1f);
18     positionManager.SetPosition(clampTranslation / 2 + 0.5f);
19     ev3.MoveRobot((sbyte)( clampTranslation * 10));
20   } else {
21     positionManager.SetPosition (0.5 f);
22     ev3.StopRobot ();
23   }
24 }
```
- Define what should happend if a gesture is recognized
#### VoiceManager.cs
```csharp
1 [...]
2 /// Initialize ...
3 KeywordRecognizer keywordRecognizer ;
4 Dictionary <string , System.Action > keywords = new Dictionary<string , System.Action>();
5 [...]
6 /// Create keywords and add them to dictionary ...
7 keywords.Add("grab", () => {
8   ev3.GrabObject();
9 });
10 keywords.Add("place", () => {
11   ev3.PlaceObject();
12 });
13 keywords.Add("stop", () => {
14   ev3.Stop();
15 });
16 [...]
17 /// Tell KeywordRecognizer which words to recognize.
18 keywordRecognizer = new KeywordRecognizer(keywords.Keys.ToArray());
19 /// Register for OnPhraseRecognized event
20 keywordRecognizer.OnPhraseRecognized += KeywordRecognizer_OnPhraseRecognized ;
21 /// Start recognizing ...
22 keywordRecognizer.Start ();
23
24 private void KeywordRecognizer_OnPhraseRecognized(PhraseRecognizedEventArgs args) {
25   Debug.Log("Keyword Recognized ..." + args.text);
26   System.Action keywordAction 
27   /// Fire action depending on which keyword was recognized .
28   if(keywords.TryGetValue(args.text , out keywordAction)) {
29     keywordAction.Invoke ();
30   }
31 }
```
- Recognize voice commands and trigger actions

#### EV3Manager.cs

```csharp
1 using UnityEgine ;
2 using MonoBrick.EV3;
3
4 public class EV3Manager : MonoBehaviour {
5
6 public Brick <TouchSensor, Sensor, Sensor, Sensor> ev3;
7 [...]
8
9 void Start () {
10   ev3 = new Brick<TouchSensor, Sensor, Sensor, Sensor>("WiFi");
11   ev3.Connection.Open();
12 [...]
13 }
14 [...]
15 public void MoveRobot(sbyte speed) {
16   ev3.MotorA.On( speed );
17 }
18 [...]
19 public void GrabObject() {
20   WaitForMotorToStop();
21   ev3.MotorB.Off();
22   WaitForMotorToStop();
23   ev3.MotorC.MoveTo(10, 80, true);
24   WaitForMotorToStop();
25   ev3.MotorB.On(-25);
26   MotorBlocked = true;
27 }
```
- Wrap the controls of the robot in a manager so you can call the API from everywhere with defined robot movements

### Result

Place Video HERE

Made with :heart: at ART+COM AG in Berlin.