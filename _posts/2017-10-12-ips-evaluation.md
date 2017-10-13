---
layout: post
title: 'Evaluating indoor positioning systems'
author: <a href="https://github.com/globz-eu/">Stefan Dieterle</a>
excerpt_separator: <!--end-of-excerpt-->
---

Indoor positioning systems take over where satellite-based systems' are becoming too inaccurate due to signal attenuation by building structures. There is a plethora of indoor positioning systems on the market and choosing the most appropriate one can be challenging. Those systems use a wide range of technical solutions and differ in:
- ease of use
- reactivity
- power usage
- accuracy
- price

Furthermore, due to the physical properties used for the positioning systems their performance depends on the environment in which they operate. It is therefore recommended to assess the usefulness of indoor positioning systems for each particular environment and use case. Here we present an experimental set up to determine the static accuracy of positioning systems. We have used this set up to compare the [Quuppa](http://quuppa.com/) and the [GoIndoor](https://www.goindoor.co/) positioning systems.

![Position errors](https://artcom.github.io/images/2017-10-12-ips-evaluation/position_errors.png)
<!--end-of-excerpt-->

# Set up
Two indoor positioning systems were set up in the lab: the Quuppa system and the GoIndoor system. 5 Quuppa locators and 5 GoIndoor beacons where placed in a ca. 11.5 X 9m room and set up and calibrated according to manufacturer's instructions.

To be able to evaluate and compare the accuracy of different indoor positioning systems, true coordinates were compared to estimated coordinates. The coordinates (true coordinates) of 18 points on a grid with a grid distance of 1m were determined in our office using a laser-meter (Fig. 1).

![Fig. 1](https://artcom.github.io/images/2017-10-12-ips-evaluation/ehrenfeld.png)

*Fig. 1. Locations of experimental points in the office environment.*

Nodes to be positioned (mainly smart phones) were placed at the different points and the position provided by the different positioning systems  (estimated coordinates) were recorded.

In order to automate the evaluation a software solution consisting of a [backend](https://github.com/artcom/eppsa-ips-evaluation) and a [frontend](https://github.com/artcom/eppsa-ips-evaluation-frontend) has been developed. This software solution allows to store true coordinates of experimental points, and create zones for zone recognition accuracy measurements. When the true position of nodes is set, experiments can be run and the estimated positions are recorded. Global and per point metrics for the experiments can then be visualized.

# Results
Two experiments were performed, using the Quuppa and the GoIndoor systems respectively. For each measurement the 2D error was calculated as the distance between the estimated coordinates and the true coordinates of the position of the node, taking into account only horizontal coordinates(x and y). The Average 2D error was found to be significantly lower for the Quuppa system (0.69m) than for the GoIndoor system (2.59m) in our experimental conditions (Fig. 2). Furthermore the variance of the 2D errors was also lower for the Quuppa system (0.20m) than for the GoIndoor system (1.66m). Globally the Quuppa system allows a significantly higher positioning accuracy in static conditions.

<div>
  <iframe width="400" height="300" frameborder="0" scrolling="yes" src="https://plot.ly/~stefand/9.embed?link=false&modebar=false&autosize=True"></iframe>
</div>

*Fig. 2. 2D accuracy average.*

2D errors were also analyzed per point for the Quuppa (Fig. 3A) and the GoIndoor (Fig. 3B) systems.

<div>
    <iframe width="1200" height="600" frameborder="0" scrolling="yes" src="https://plot.ly/~stefand/5.embed?link=false&modebar=false"></iframe>
</div>

*A*

<div>
    <iframe width="1200" height="600" frameborder="0" scrolling="yes" src="https://plot.ly/~stefand/7.embed?link=false&modebar=false"></iframe>
</div>

*B*

*Fig. 3. GoIndoor per point position errors. The 2D error is plotted on the z axis and the corresponding point position is plotted on the x and y axes. The 2D error is color coded from blue for an error of 0m to red for an error of 4m and above.*

The per point results confirm the global results and allow a more fine grained analysis of the positioning accuracy in the experimental space.

# And now?
Our preliminary results suggest that the Quuppa system has a much higher accuracy than the GoIndoor system in theset up conditions. It would be interesting to gather more data in more positions to cover more of the office space. Furthermore it might be possible to optimize the accuracy of the different systems by varying the position and number of the positioning beacons (for the GoIndoor system) or antennas (for the Quuppa system).

The software solution used to perform the experiments greatly facilitates the acquisition and analysis of experimental data and could be used to assess the performance of positioning systems in a wide variety of environments. It could be improved to provide a more friendly interface and analysis tools for zone recognition accuracy.

Other indoor positioning systems could be assessed to broaden the range of options and better understand which ones are best suited for which use cases.

# Further reading
[Evaluation of RF-Based Indoor Localization Solutions](http://www.evarilos.eu/index.php)

[Performance analysis of multiple Indoor Positioning Systems in a healthcare environment](https://ij-healthgeographics.biomedcentral.com/articles/10.1186/s12942-016-0034-z)
