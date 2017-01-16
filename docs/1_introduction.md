#### Chapter 1
## Introduction
***
#### 1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Overview

The purpose of this project is to provide an alternative cooling and heating script for [*OMSI 2*](http://omnibussimulator.de) buses, satisfying—given both my personal as well as the game's limitations—to the extent practically feasible the following requirements:
* Its functionality / "behavior" has to *feel realistic* to the end user.
* It must be *generic*, thus capable of addressing the apposite needs of a large number of preexistent vehicles. In other words, the script has to be vehicle-independent, that is, its integration with any given vehicle must be possible without altering either the vehicle and/or (the core / business logic) of the script.
* It must be *configurable* / *customizable* without requiring code modification, so as to be easily tailorable to meet individual vehicles' characteristics.
* It should be (easily) *extensible*, in order to fulfill emergent or special / "edge" cases.

#### 1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Motivation / Background

Upon testing the wonderful *MAN Stadtbusfamilie* add-on after its release in September 2016, I noticed that the vehicles' cooling / heating script suffered from a bug: Its driver's A/C function would cool the bus almost with the intensity of a refrigerator :stuck_out_tongue:. Since it was not the first time I was witnessing the particular bug, I decided to do myself a favor and fix it; *"this shouldn't take more than an hour"*, I thought. Little did I know that I would end up wasting months rewriting the entire damned thing, polishing and testing it over and over, until it felt good enough to me. Once that "milestone" had finally been reached, I wondered whether it would be meaningful to contribute the resulting modification to the add-on's developers directly, or to just publish it for everyone to use; eventually I settled for the latter path.

Following its initial release, the script ceased targeting specifically the *MAN Stadtbusfamilie* add-on's vehicles, morphing into the "universal" version it now is.

#### 1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Compatible vehicles

As it currently stands, *UCHill* is compatible with any bus having *manual*, i.e., *not* featuring fully digital / automatic (klimatronic-like), cooling / heating controls / panels. Integration is believed to be seamless in the case of the typical, VDV dashboard-equipped (inter-)city buses; integrating with older buses might be just a tad more involved.

#### 1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Features

#### 1.4.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Function enhancements

The effectiveness and the start / stop preconditions of all major functions (air-conditioning, cabin heaters, etc.) have been reworked so as to feel more realistic.

#### 1.4.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Sound hooks

Sound hooks pertaining to all major functions have been introduced. Note that you will still have to install sound files of your choosing yourself and couple them with the provided hooks in the sound configuration file of the vehicle you are modding, in order for them to become audible.

#### 1.4.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Window misting hooks

Hooks expressing the degree of window fogging on the outside and inside, based on temperature and humidity, have been added. Like the sound-related variables, these are merely hooks, meaning that you will have to provide and integrate your own fog textures to see the feature in action.

#### 1.4.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Greenhouse-like effect

The cabin can get considerably warmer than its environment, depending on factors such as the solar elevation angle.

#### 1.4.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Humidity management

Vehicles featuring passenger air-conditioning now provide semi-automated humidity management facilities assisting in maintaining the cabin's level of humidity within (marginally) comfortable limits.

#### 1.4.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Structural script changes

The script has received a major refactoring in an effort to increase its modularity. The documentation coverage has slightly improved as well. This is of course more of a developer aid, than a tangible end-user feature.

#### 1.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Credits

Thank you...
- [M+R Software](http://m-r-software.de/), for giving us the game, scripting engine and the original version of this very script; also for the two *MAN NL/NG* sound files of theirs referenced herein<sup>[1](#footnote_1)</sup>.
- [Chrizzly92](http://www.omnibussimulator.de/forum/index.php?page=User&userID=15380) and everyone having contributed to the [MAN Stadtbusfamilie](http://man-stadtbus.de) add-on, for the awesome buses in general and the derived version of the script in particular.
- [Morphi](http://www.omnibussimulator.de/forum/index.php?page=User&userID=531), for all the years of tireless contributing he has invested into the community on so many levels in general, the two of his *MB O530(G)* mod pack's sound files referenced herein<sup>[1](#footnote_1)</sup>, some ideas as well as a few lines of code that were taken / adopted from his aforementioned mod's respective script.
- [Carl R. Nave](http://hyperphysics.phy-astr.gsu.edu/hbase/Kinetic/relhum.html#c4), [Steve Scanlon](http://www.ringbell.co.uk/info/humid.htm) and [Wolfgang Kühn](http://www.decatur.de/javascript/dew/): the humidity- and dew point-related "curves" employed by the script were derived from output data that were generated from utilities authored and/or maintained by these people.
- AM Watson and David E. Watson of [The Flying Turtle Company](http://www.ftexploring.com), for their [Table of Horizontal Surface Insolation](http://www.ftexploring.com/solar-energy/sun-angle-and-insolation2.htm) used by the script to calculate the greenhouse-like effect's solar elevation angle factor.

#### 1.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Disclaimers

#### 1.6.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Performance consideration

This is a somewhat "performance-intensive" script, partially due to its complexity, but most importantly because of the extensive floating point arithmetic it performs. Therefore, if you decide to use it, you should expect experiencing a *mild* performance degradation (< 5 FPS). As this greatly depends on your hardware and environment (and formal benchmarking is hard, if not outright impossible in OMSI) I cannot be more specific.

#### 1.6.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Correctness

I am neither a physicist, nor someone knowledgeable on the field of cooling / heating systems; in fact I am not even particularly bright at science / math in general (but even if I were, OMSI's not-so-great math support would still get in the way). The script is consequently based on common sense and empirical evidence at large, rather than backed by "hard" science. If you came here in search of something even remotely scientifically accurate, I must disappoint you—you visited the wrong HTTP resource.

#### 1.6.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Terms of use

This OMSI 2 modification is public domain content, published under the terms of the [*Unlicense*](../LICENSE). By using *UCHill*, in a nutshell, you acknowle that:
* You are using *UCHill* at your own risk. I am *not* to be held liable for any damages arising from the use of *UCHill*, including, but not limited to, the disruption of your device's operation, the corruption of your OMSI 2 installation, or the decline of your cat's psychological well-being.
* The fact that you are granted permission to use *UCHill* however you please does *not* imply that you are also entitled to support provision. In general, you can do whatever you want, as long as it doesn't pose a hindrance to *my* respective right of doing whatever *I* want.

#### 1.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Terminology / conventions

*OMSI (root / base) directory*, `<OMSI>`<br/>
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;The directory of an OMSI 2 installation.<br/><br/>
*UCHill (installation) directory*, `<UCHill>`<br/>
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;The directory containing the modification's core files, located at<br/>
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`<OMSI\Scripts\uchill`.<br/><br/>
*Target vehicle (directory)*, **`<target vehicle>`**<br/>
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Some OMSI 2 *user* vehicle (bus), with which *UCHill* is to be integrated;<br/>
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;the directory containing the files thereof, typically a sub-directory of<br/>
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`<OMSI>Vehicles\`.<br/>

***
<sub><a name="footnote_1">1</a>: Those are merely referenced, i.e., they are *not redistributed* with this modification.</sub>
