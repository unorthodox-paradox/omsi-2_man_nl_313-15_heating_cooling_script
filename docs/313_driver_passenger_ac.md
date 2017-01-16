#### 3.1.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Driver's and passengers' A C
***
#### 3.1.1.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Introduction

*UCHill* implements three, or for clarity's sake, one *unconditionally* and two *conditionally* A/C-based functions. Two of them, the *driver's* and *passengers' A/C*, have much in common, and will therefore be discussed jointly in this section. As already hinted, the *A/C* component of the functions' names is not to be interpreted as the function necessarily being dependent on / using an actual A/C unit at all times.

F1, as suggested by its name, is responsible for heating and cooling the driver's compartment, and that alone. Consequently, its contribution to the overall cabin temperature—the script does not attempt to measure the temperature in the driver's area separately—is almost negligibly small. F2 is responsible for heating and cooling the main cabin area that the passengers occupy, and is only usable in vehicles featuring a roof-mounted A/C unit.

#### 3.1.1.2.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Operational mode

Depending on its controlling button's state, F1 may operate in either of the two following primary modes: *EM* (button in "OFF" state) or *AM* (button in "ON" state). The former employs a mixture of cool and engine-heated air, while the latter supplies air whose temperature is regulated by the roof-mounted A/C unit (in vehicles not having one, the availability of a smaller, driver-dedicated imaginary one is assumed).

F2 has no such operational mode, as it is always backed by the exterior A/C unit.

#### 3.1.1.2.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output temperature - Driver's A/C

In EM, the temperature of the function's air output lies in the interval [t<sub>cool</sub>, t<sub>engine\*</sub>], depending directly on *A2*'s setting. The value of the "cool" lower bound is either equal to that of the environment, when A4 is in *FM* (button in "OFF" state), or that of the cabin, when A4 is in *RM* (button in "ON" state). The starred upper bound implies that, regardless of the engine's temperature, the output temperature never rises above safety limits.

In AM, the output temperature's interval likewise depends on A2, with its minimum and maximum values in this case varying based on several factors, including the A/C unit's effectiveness and the environmental temperature's departure from the A/C's *ideal* output temperature represented by A2's setting. A4 plays a role in this operational mode as well: In RM, the positive feedback loop occurring—the amount of air being processed during each frame is warmer or cooler than that the frame before, therefore being easier to warm up or cool down further by spending the same amount of energy—allows the A/C to attain *better*, i.e., closer to the desired ideal, output temperatures than those it would achieve in the same context under FM.

#### 3.1.1.2.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output temperature - Passengers' A/C

F2's output temperature depends on the same factors as F1's output temperature in AM, except for A2, the setting of which does not affect it. Specifically, depending on the context—heating or cooling—, the output temperature is based on a predefined constant "baseline", i.e., a specific *ideal* heating / cooling temperature, rather than A2's setting.

#### 3.1.1.2.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output air volume - Driver's A/C

The volume of F1's air output generally depends on A3's setting. In EM, when A4 is in FM, the output volume additionally depends on the vehicle's velocity; in all other cases it is velocity-independent. Furthermore, in EM the maximum achievable output volume is the same regardless of A4, while in AM the maximum achievable output volume is considerably smaller when A4 is in RM than when it is in FM.

#### 3.1.1.2.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output air volume - Passengers' A/C

F2's output volume depends on the same factors as F1's output volume in AM, except for A3, the setting of which does not affect it.

#### 3.1.1.2.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output humidity volume - Driver's A/C

The humidity of F1's air output, that is, the volume of water vapor "contained in" the air admitted into the vehicle's interior by the function (obviously) depends on the environmental level of humidity. It additionally varies based on the operational mode and A4's setting as follows: In EM and with A4 being in FM, the output's humidity is equal to the environmental humidity; in all other cases it is a mere fraction thereof.

#### 3.1.1.2.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output humidity volume - Passengers' A/C

F2's output humidity volume depends on the same factors as F1's output humidity volume does in AM.

#### 3.1.1.2.9&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Overall function effectiveness

In general, a heating / cooling function's effectiveness depends both on its output temperature and its output volume. Taking both factors into account, the information of the previous sections can, for the vast majority of use cases, be condensed as follows:
- F2 is *(much) more* effective than F1.
- For the same set of A2, A3, and A4 settings, in a *heating* context, F1's effectiveness in EM mode is *better* than its effectiveness in AM.
- For the same set of A2, A3, and A4 settings, in a *cooling* context, F1's effectiveness in AM is *better* than the its effectiveness in EM.
- A4, when in RM, while improving the output temperature factor, severely reduces the output volume factor, thus actually bringing about a net performance *decrease*.
- In a *dehumidification* context, wherein the environmental level of humidity is *higher* than that of the cabin, both functions are *more* effective when A4 is in RM than when it is in FM (and, of course, *most* effective when they are switched off); when the environmental humidity is *lower* than the cabin, they are *more* effective when A4 is in FM than when it is set to RM.
- In a *humidification* context, wherein the environmental level of humidity is *higher* than that of the cabin, both functions are *more* effective when A4 is in FM than when it is in RM; when the environmental humidity is *lower* than that of the cabin, they are *more* effective when A4 is in RM than when it is set to FM (and, of course, *most* effective when they are switched off).

#### 3.1.1.2.10&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Economy profile

Both functions (F1 only when in AM mode) feature a secondary operational mode—let's refer to it as a *profile* to avoid confusion—which, when in effect, causes the A/C unit to use *environmental* air as its heating or cooling agent, greatly enhancing its performance (and, at least in the real world, reducing its energy consumption—but that is subject to the engine and/or electrics scripts, thus falling outside of this modification's scope) as a consequence. This economy-like profile, also known as *free heating / cooling*, gets automatically applied when A4 is set to FM and either a) in a *heating* context, the environment happens (for whatever reason) to already be considerably *warmer* than the cabin, or b) in a *cooling* context, the environment is considerably *cooler* than the cabin. Once the aforementioned preconditions no longer hold, the function reverts to normal A/C-based operation.

#### 3.1.1.2.11&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Maintenance profile

When [idle](#idle-states), both functions (F1 only while in AM) in most cases revert to a *maintenance-like* state, wherein their fan speeds—thus also their output volumes—are reduced, A4 is implicitly set to RM (regardless of its actual setting) and the output temperature is automatically adjusted to that of the cabin. For the duration of this profile's enforcement, the A/C unit remains in a disabled, "stand-by" state, and A4's indicator blinks.

#### 3.1.1.2.12&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Sounds

Use of the two functions can trigger playback of up to (save for button sounds) five different sounds: two per function (one being fan- and one A/C-emitted) plus one allocated to (if present) the exterior A/C unit.

The volume of the fan-related ones among the sounds is affected by the corresponding function's output volume as well as the power supply (engine and battery state). The interior A/C sounds' volume is too affected by the function's output volume and power supply, as well as use of the economy and maintenance profiles—when either of the latter is active, this group of sounds gets disabled—and use of *[F3](./humidity_management.md)*. Lastly, the exterior A/C sounds' volume depends on power supply and generally on which ones of all three A/C-based functions—F1, F2 and F3, weighted differently—are in use; in other words, its volume gets maximized when all functions are active.

#### 3.1.1.2.13&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Inertia

From the instant either of the functions gets triggered (as a result of e.g. their controller being pushed or, in the case of F1 in AM, A2 being adjusted), several factors affect the overall delay, i.e., the time that must elapse, before they can begin actually contributing to the cabin's temperature (and its humidity, as a side effect) in the desired way; the primary among these factors are:
- A random propagation delay (F1 in AM) for the A/C unit to even acknowledge the activation signal.
- Unless eco mode applies (F1 in AM), the environmental temperature's departure from the (targeted) output temperature of each function.
- The time required for the A/C unit's heating / cooling circuit (F1 in AM) to reach a temperature that is "close enough" to the (targeted) output temperature of each function, so that its output can be perceived as "comfortable" by humans. In a heating context, for instance, if it is freezing cold outside, the A/C unit would not admit any air into the cabin until it is capable of heating it up to well above the freezing point.
- Lastly, the time needed by the fans to achieve the desired rotation speed.

The aforementioned "signal propagation" and fan speed adjustment factors apply to the corresponding function deactivation delays as well.

#### 3.1.1.2.14&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Cabin heater coupling

As already mentioned in the [overview](./manual.md#overview), *[F5](./cabin_heaters.md)* acts as an attribute of F1 and F2. Specifically, the former *prevents* the latter (only applying to F1 when in AM) from outputting any air whatsoever if either of the following holds:
- When, in a *heating* context, the (targeted) output temperature of F1 and/or F2 is *significantly lower* than that achieved by F5; that is, when the A/C unit is unable to "compete" with the engine-assisted F5 and the air output of the functions it backs would, as a consequence, hinder rather than reinforce F5.
- When in a *heating* context that is "perceived" by F1 and F2 as being a *cooling* context, F5 has not yet fully cooled down and/or its controller is on the "ON" state. Wait—what? Yep, I know, this is somewhat confusing, so here is an example: Assume that you are heating up the vehicle via a combination of F1 and/or F2 *and* F5. Once the *maximum heating* cabin temperature is reached (which, by default, is equal for all functions), deactivation of all three gets triggered. F5, however, having a much higher heat and air volume [inertia](./cabin_heaters.md#inertia) than the rest of them, will continue emitting considerable amounts of heat for some time following its deactivation event. When the environment is mildly cold to neutral, this "post-deactivation radiation" effect of F5 can cause the cabin's temperature to ascend well above F1's and/or F2's *minimum cooling* cabin temperature. This restriction will at that point prevent the latter from being automatically re-initiated *as if* a *cooling* context were in effect, until F5 has completely cooled down *and* has manually been disabled, preventing a wasting of energy.

#### 3.1.1.2.15&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Cabin heater coupling

Occasionally you will witness either function's controller's LED being in a *blinking* state (unless electrics are disabled), which generally indicates that the function (F1 only when in AM) is *inactive* or undergoing a *state transition*, i.e., being in the process of becoming active or inactive. The following table describes some such states that you are most likely to encounter:

Deactivation cause | What happens
------------------ | ------------
The environment is severely *cold* or *hot* and the A/C unit has been deactivated to prevent damage. | [Maintenance profile](#maintenance-profile)
The cabin temperature has exceeded the function's *maximum heating*, or has dropped below its *minimum* cooling limit, in a *heating* or *cooling* context, respectively. | Maintenance profile
Power supply is insufficient - engine is off. | Maintenance profile
Power supply is insufficient - master electrics switch is off or battery is depleted. No LED blinking will be occurring in this case of course. | General shut down
The heating / cooling circuit is still in the process of heating up / cooling down and its current output temperature is still, respectively, below or above the comfort limit (see also: [Inertia](#inertia)). | The A/C unit is active and its exterior sounds are audible, while the output volume of the functions affected, as well as their interior sounds—both fan- and A/C-emitted—are zeroed.
The A/C unit is in some other start-up or shut-down state. | Maintenance profile
F5 prevents the functions from starting (see also: [Cabin heater coupling](#cabin-heater-coupling)). | Maintenance profile
