[<sub>&#8592; 3.1.1 - Active functions</sub>](./3_functionality_details.md#311active-functions) <sub>|</sub> [<sub>Index</sub>](./0_index.md)
***
#### 3.1.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Humidity management
***
#### 3.1.1.3.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Definitions

Abbreviation | Meaning
------------ | -------
*F<sub>X</sub>*, *A<sub>X</sub>*| Function and attribute labels, as per the [overview](./3_functionality_details.md#3111overview).
*AM* | *A/C-based mode*, an *operational mode* (see below) of F3.
*CM* | *Cabin heater-based mode*, another *operational mode* of F3.
*AP* | *Activation profile* (see below)
*MS* | *Manual setting* of AP.
*AS* | *Automatic setting* of AP.

#### 3.1.1.3.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Introduction

This function provides humidity management capabilities to the vehicle: *Dehumidification* (*reducing* the cabin's air's level of humidity when the former is too *humid*) and *humidification* (*increasing* the cabin's air's level of humidity when the former is too *dry*). It does not (directly) affect the cabin's temperature.

#### 3.1.1.3.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Operational mode

Depending on whether the vehicle features a roof-mounted A/C unit or not, the function may operate in either of the two following primary modes: *AM* (A/C unit present) or *CM* (A/C unit absent). The former leverages the A/C unit's functionality for both dehumidification and humidification purposes, while the latter is simply tied to the [cabin heater](./3115_cabin_heaters.md) function *(F5)*, providing solely dehumidification. Note that, strictly speaking, F3 is only a *real* function when operating in AM—in CM it is F5 that performs the actual work, while dehumidification is merely a side effect.

#### 3.1.1.3.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Effectiveness

The function's output volume—the amount of moisture it is capable of removing from (in a dehumidification context), or inserting into (in a humidification context) the cabin *per time unit*—is (in a mathematical sense) a function of the A/C unit's or [F5's](./3115_cabin_heaters.md#effectiveness) effectiveness, in AM and CM respectively, with AM generally being considered *more* effective than CM, due to being temperature rate-independent. In AM, the *maximum* achievable positive or negative humidity contribution of the function is additionally *[AP](#31135activation-profile-and-ac-based-heating--cooling-function-coupling)*-dependent.

F3's effectiveness is naturally further affected, albeit only implicitly, by a number of other factors, including:
- The environmental level of humidity; when *greater* than that of the cabin, F3's effectiveness is *decreased* in a *dehumidification* context, and *increased* in a *humidification* context; when *lower* than that of the cabin, F3's effectiveness is *increased* in a *dehumidification* context, and *decreased* in a *humidification* context.
- Likewise, windows' and doors' opening states, as well as *[F1](./3112_driver_passenger_ac.md#31124output-temperature---drivers-ac)* and *[F2](./3112_driver_passenger_ac.md##31125output-temperature---passengers-ac)*.
It should also be noted that the "perceived" effectiveness of the function—increase or decline in *relative* cabin humidity—varies significantly depending on the cabin's temperature. In general, the lower the cabin temperature, the higher the perceived effectiveness.

#### 3.1.1.3.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Activation profile and A/C-based heating / cooling function coupling

The function features a secondary operational mode—let's refer to it as a profile to avoid confusion—that controls its "lifecycle", specifically with regard to its activation and deactivation conditions. Two settings pertain to this profile: *Manual (MS)* and *automatic (AS)*. The former applies regardless of the operational mode, whenever the function gets triggered as a result of its controller being pushed; the latter applies only in AM, in those cases where the function gets activated as the result of another function's operation.

As already mentioned in the [overview](./3_functionality_details.md#3111overview), *F1* and *F2* act as attributes of F3 when the latter is in AM. Specifically F3 is activated in AS-AP when, and for as long as, the following preconditions hold:
- F3's controller is in its "OFF" state,
- either or both of F1 (in AM), F2 are *active*, i.e., are in a *non*-[idle](./3112_driver_passenger_ac.md#311216idle-states) state and their [eco profile](./3112_driver_passenger_ac.md#311211economy-profile) is *not* in effect), and
- cabin humidity levels are high or low enough for dehumidification or humidification respectively to be applicable.

When in AS-AP, the LED indicator of F3's controller *blinks*.

#### 3.1.1.3.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Sounds

Humidity management has no sounds of its own, depending either on [F2's](./3112_driver_passenger_ac.md#311213sounds), when in AM, or on [F5's](./3115_cabin_heaters.md#31155sounds) sounds, when in CM; in the former case it only uses the *interior* and *exterior A/C*-related ones of F2's sounds. The function's sound volume is static.

#### 3.1.1.3.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;F5 vs F3 in CM

Both do the exact same thing, that is, both heat up the cabin via the cabin heaters. The (subtle) difference between the two lies in their (de-)activation conditions:
- The former can be activated provided that the cabin's temperature is *below* F5's *maximum heating* cabin temperature. When active, it will keep on heating the cabin until this condition is satisfied, disregarding the level of cabin humidity, and "overriding" F3's "opinion" on the matter.
- The latter can likewise be activated provided that the cabin's temperature is low enough, but with the additional constraint that the cabin's humidity is *high* enough as well. When *either* of the two conditions is no longer true, F3 will become disabled and unavailable for use.

#### 3.1.1.3.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Inertia

From the instant the function gets triggered (as a result of e.g. its controller being pushed), several factors affect the overall delay, i.e., the time that must elapse, before it can begin actually contributing to the cabin's humidity, including:
- A random propagation delay for either the A/C unit (when F3 is in AM) or the cabin heaters (in CM) to even acknowledge the activation signal. This factor affects F3's deactivation delay as well.
- In AM, the environmental humidity level's departure from the (targeted) cabin humidity.
- In CM, the engine temperature's *negative* departure from a positive value considered "normal".

#### 3.1.1.3.9&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Idle states

Unless F3 is active in AM / AS-AP, its controller's LED being in a *blinking* state indicates that the function is *inactive* or currently undergoing a *state transition*, i.e., being in the process of becoming active or inactive. Possible reasons:
- The cabin humidity has dropped below F3's *minimum*, or has exceeded its *maximum humidity* limit, in a *dehumidification* or *humidification* context, respectively.
- In AM, when the A/C unit has been deactivated to prevent damage due to the environment being severely cold or hot.
- In CM, when the engine has not yet warmed up sufficiently, or when the cabin's temperature has exceeded F5's *maximum heating* cabin temperature.
- The engine is not running, the master electrics switch is off or the battery is depleted; no blinking will occur in the latter case of course.

Consequently:
- In AM, the A/C unit is deactivated, unless needed by another function.
- In CM, the cabin heaters are deactivated, unless F5 is itself still active.
- F3's contribution to the volume of the sounds of F1 / F2 (AM) or F5 (CM) it uses is zeroed.

***
[<sup>&#8592; 3.1.1 - Active functions</sup>](./3_functionality_details.md#311active-functions) <sup>|</sup> [<sup>Index</sup>](./0_index.md)
