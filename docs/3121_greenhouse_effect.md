#### 3.1.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Greenhouse effect
***
#### 3.1.2.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Definitions

Abbreviation | Meaning
------------ | -------
*GhE* | Greenhouse-like effect

#### 3.1.2.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Introduction

The script simulates the [greenhouse](https://en.wikipedia.org/wiki/Greenhouse_effect#Real_greenhouses)-like effect that the sun has on the vehicle, that can cause the cabin's temperature to rise significantly during the warm hours of the day.

#### 3.1.2.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Effectiveness

The following factors directly affect the function's intensity:
* The solar elevation angle, in turn depending on the time of the day and the day of the year. The factor peaks at midday of the year's longest day.
* The weather conditions. The factor peaks on clear days.
* The vehicle's velocity. The factor peaks when the vehicle is stationary.
* The opening state of windows and doors. The factor peaks when all of them are shut.

#### 3.1.2.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Effect on other functions

When at its zenith, the GhE, by default, significantly reduces the cooling rate of *F1* (in AM) and *F2* (not accounting for either of them running in economy profile) and lowers their maximum cooling ability by a few degrees. Furthermore, the GhE (artificially) weakens, i.e., reduces, the rate of energy loss occurring due to air movement (because of e.g. open windows), when the cabin is *warmer* than the environment.

