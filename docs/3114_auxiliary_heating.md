[<sub>&#8592; 3.1.1.3 - Humidity management</sub>](./3113_humidity_management.md) <sub>|</sub> [<sub>Index</sub>](./0_index.md) <sub>|</sub> [<sub>3.1.1.5 - Cabin heaters &#8594;</sub>](./3115_cabin_heaters.md)
***
#### 3.1.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Auxiliary heating
***
#### 3.1.1.4.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Definitions

Abbreviation | Meaning
------------ | -------
*F<sub>X</sub>* | Function labels, as per the [overview](3_functionality_details.md#3111overview).

#### 3.1.1.4.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Introduction

This function interfaces with an (imaginary) auxiliary power unit to pre-heat the engine, as well as the water—which is normally engine-heated—employed by [*F5*](./3115_cabin_heaters.md), in order to, in the former case, assist ignition, as well as, in the latter, boost F5's initial effectiveness, under cold environmental conditions. When active, F4 also minimally and implicitly heats the cabin itself (actually the engine does, as a result of conduction, regardless of F4's "reinforcement").

#### 3.1.1.4.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Effectiveness

F4's effectiveness is proportional to the environmental temperature and the engine's volume; the second factor, however, is disregarded by UCHill's currently oversimplified implementation.

#### 3.1.1.4.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Sounds

F4 uses a single sound, whose maximum sound volume is statically defined.

#### 3.1.1.4.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Inertia

F4's activation delay depends on the engine's temperature's negative departure from a minimum positive value considered "normal", as well as on a random signal propagation delay; its deactivation delay random entirely.

#### 3.1.1.4.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Idle states

When the function's controller's indicator is in a *blinking* state, F4 is either in the process of starting up, or inactive because its targeted engine temperature—which is statically defined—has been achieved.
***
[<sup>&#8592; 3.1.1.3 - Humidity management</sup>](./3113_humidity_management.md) <sup>|</sup> [<sup>Index</sup>](./0_index.md) <sup>|</sup> [<sup>3.1.1.5 - Cabin heaters &#8594;</sup>](./3115_cabin_heaters.md)

