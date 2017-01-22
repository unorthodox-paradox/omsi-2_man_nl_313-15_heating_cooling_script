[<sub>&#8592; Chapter 3 - Functionality in detail</sub>](./3_functionality_details.md) <sub>|</sub> [<sub>Table of contents</sub>](./0_table_of_contents.md)
***
#### 3.1.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Auxiliary heating
***
#### 3.1.1.4.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Definitions

Abbreviation | Meaning
------------ | -------
*F<sub>X</sub>* | Function labels, as per the [overview](3_functionality_details.md#3111overview).

#### 3.1.1.4.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Introduction

This function interfaces with an (imaginary) auxiliary power unit to pre-heat the engine, as well as the water—which is normally engine-heated—employed by [F5](./3115_cabin_heaters.md), in order to, in the former case, assist ignition, as well as, in the latter, boost F5's initial effectiveness, under cold environmental conditions. When active, F4 also minimally and implicitly heats the cabin itself (actually the engine itself does, as a result of being--due to this function and/or normal operation--warmer than the cabin).

#### 3.1.1.4.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Effectiveness

F4's effectiveness is proportional to the environmental temperature and the engine's volume; the second factor falls outside of this script's responsibilities, as it is the engine script that affects it.

#### 3.1.1.4.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Sounds

F4 uses a single sound, whose maximum sound volume is statically defined.

#### 3.1.1.4.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Inertia

F4's activation delay depends on the engine's temperature's negative departure from a minimum positive value considered "normal", as well as on a random signal propagation delay; its deactivation delay is entirely random.

#### 3.1.1.4.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Idle states

When the function's controller's LED is in a blinking state, F4 is either in the process of starting up, or inactive because its targeted engine temperature—which is statically defined—has been achieved.
***
[<sup>&#8592; Chapter 3 - Functionality in detail</sup>](./3_functionality_details.md) <sup>|</sup> [<sup>Table of Contents</sup>](./0_table_of_contents.md)

