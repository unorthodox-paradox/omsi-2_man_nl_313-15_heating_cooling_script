[<sub>&#8592; Chapter 3 - Functionality in detail</sub>](./3_functionality_details.md#312passive-functions) <sub>|</sub> [<sub>Index</sub>](./0_index.md) <sub>|</sub> [<sub>3.2 - Other functions &#8594;</sub>](./3_functionality_details.md#32other-functions)
***
#### 3.1.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Greenhouse effect
***
#### 3.1.2.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Definitions

Abbreviation | Meaning
------------ | -------
*F<sub>X</sub>*| Function labels, as per the [overview](./3_functionality_details.md#3111overview).
*GhE* | Greenhouse-like effect

#### 3.1.2.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Introduction

*UCHill* simulates a [greenhouse](https://en.wikipedia.org/wiki/Greenhouse_effect#Real_greenhouses)-like effect, in other words, the effect of insolation on the vehicle, that can cause the cabin's temperature to rise significantly during the warm hours of the day.

#### 3.1.2.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Effectiveness

The following factors directly affect the GhE's intensity:
* The solar elevation angle, in turn depending on the time of the day and the day of the year. The factor peaks at midday of the year's longest day.
* The weather conditions. The factor peaks on clear days.
* The opening state of windows, hatches, and doors, in terms of solar radiation "escaping" the cabin. This factor is negligibly small and peaks when all windows, hatches, and doors are shut.

Implicitly, the function's effectiveness is additionally affected by:
* The opening state of windows, hatches, and doors, in the sense of convective heat transfer from the cabin to the environment. The more windows, hatches and doors are open, the weaker the function becomes.
* The operation of [F1](./3112_driver_passenger_ac.md) / [F2](./3112_driver_passenger_ac.md) in a cooling context. The GhE is weakest when both functions are active.

#### 3.1.2.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Effect on other functions

When at its zenith, the GhE tends to reduce the cooling rate of *F1* (in AM) and *F2* (not accounting for either of them running in economy profile), lowering their maximum cooling ability by up to a few degrees. Furthermore, the GhE (artificially) weakens, i.e., reduces, the rate of energy loss occurring due to air flow (because of e.g. open windows), when the cabin is *warmer* than the environment.
***
[<sup>&#8592; 3.1.2 - Passive functions</sup>](./3_functionality_details.md#312passive-functions) <sup>|</sup> [<sup>Index</sup>](./0_index.md) <sup>|</sup> [<sup>3.2 - Other functions &#8594;</sup>](./3_functionality_details.md#32other-functions)
