[<sub>&#8592; Chapter 4 - Using *UCHill*</sub>](./4_usage.md)
***
#### Chapter 5
## Technical reference
***
This chapter goes over some of *UCHill*'s internal workings as well as customization options.

#### 5.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Overview

Terminology note: A *script-level function* is any portion of the script (typically spanning one or more macros) that implements (a subset of) the functionality pertaining to one or several of the high-level vehicle aspects discussed in [chapter 3](./3_functionality_details.md). For the sake of clarity, the latter will for the remainder of this page be referred to as *features*. There is no precise mapping between script-level functions and features; features have really only been employed thus far as a necessary oversimplification to facilitate the process of explaining in a straightforward manner to readers not familiar with OMSI's scripting language what it is the script (roughly) does.

The basic structure of the original M+R script has been maintained. For each script-level function, the primary macro, `cabinair_frame`, delegates to the appropriate macros in order to:
* Evaluate whether a function is "active", i.e., in this context, if preconditions hold for it to bring about change to system state.
* Calculate, based on the above evaluation, the function's delta (change or rate of change).
* Determine, for sound-emitting functions, the sound volume.

Lastly the delegator computes the final values that (measurable) system state comprises, by combining as appropriate their initial values with their corresponding deltas returned by the respective delegates. For example, in the case of cabin temperature, the overall difference from the cabin temperature the frame before is simply the summation of the temperature rates of all functions that affect temperature.

#### 5.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Integration adapter

As already noted in [2.2.2.1](./2_installation_integration.md#2221authoring-the-integration-adapter), `uchill.osc` depends on an intermediate adapter script in order to retrieve and update vehicle-specific information / state. The sequence-like diagram below depicts the interaction between the two scripts:
![main_script_adapter_script_interaction](http://i.imgur.com/WFOc5g2.png)

In plain English, the interaction comprises the following steps:
1. During initialization, after the main script of the vehicle has delegated to the `uchill_init` macro:
    1. `uchill_init` delegates to `uchill_integration__init`, which performs the initialization logic required by the adapter script (if any).
    1. `uchill_init` delegates to `uchill_integration__acquire_static_vehicle_attributes`, which assigns values of vehicle-specific variables and/or constants, representing *static* vehicle attributes (e.g., the vehicle's cabin's air volume capacity), to their corresponding integration variables.
1. `uchill_init` delegates to `uchill_integration__acquire_dynamic_vehicle_attributes`, which assigns values of vehicle-specific variables, representing *dynamic* vehicle attributes (e.g., whether a roof-mounted A/C unit is present, in the case of a vehicle allowing in-game (de-)installation of the unit), to their corresponding integration variables.
    1. During each frame, after the main script of the vehicle has delegated to the `uchill_frame` macro, and *before* `uchill_frame` delegates to `cabinair_frame` (wherein *UCHill*'s core logic lies):
    1. `uchill_frame` delegates to `uchill_integration__acquire_dynamic_vehicle_attributes` (see above).
    1. `uchill_frame` delegates to `uchill_integration__acquire_vehicle_state`, which assigns values of vehicle-specific variables, representing actual vehicle *state* (e.g. whether the engine is running), to the corresponding integration variables.
1. Finally, during each frame, after the main script of the vehicle has delegated to the `uchill_frame` macro, and *after* `cabinair_frame` has returned:
    * `uchill_frame` delegates to `uchill_integration__actualize_vehicle_state`, which, unlike the previous macros, is responsible for assigning the values of integration variables set by `uchill.osc` *itself* (e.g. a controller's LED indicator's state), to corresponding vehicle-specific variables.

In summary, `uchill_integration__init` initializes the adapter, `uchill_integration__actualize_vehicle_state` *pushes* information from `uchill.osc` to the rest of the vehicle, and the remaining of the integration macros *pull* information from the vehicle to `uchill.osc`. Of course, in cases where the information provided by the vehicle is not strictly compatible with the information expected by `uchill.osc`, or vice versa, it is an additional job of the integration adapter's macros to implement the logic necessary to "translate" between the two.
