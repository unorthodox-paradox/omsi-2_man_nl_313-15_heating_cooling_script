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

As already noted in [2.2.2.1](./2_installation_integration.md#2221authoring-the-integration-adapter), `uchill.osc` depends on an intermediate adapter script in order to retrieve and update vehicle-specific information / state in a portable fashion. The sequence-like diagram below depicts the interaction between the two scripts:
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

In summary:
* `uchill_integration__init` initializes the adapter.
* `uchill_integration__actualize_vehicle_state` *pushes* information from `uchill.osc` to the rest of the vehicle.
* The remaining of the integration macros *pull* information from the vehicle to `uchill.osc`.

Of course, in cases where the information provided by the vehicle is not strictly compatible with the information expected by `uchill.osc`, or vice versa, it is an additional job of the integration adapter's macros to "translate" between the two.

#### 5.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Variables

#### 5.3.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Local

The following are the variables declared and employed by `uchill.osc`. Note that solely variables that might be of some value to third-party scripts are enumerated in this section—those internal to the script, i.e., those being of auxiliary / technical nature and/or have no stable contract and/or strict value set are disregarded. Also keep in mind that, unless otherwise specified, reassigning any variable(s) externally will likely cause `uchill.osc` script to break, unless you know what you are doing.

#### 5.3.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Feature state

#### 5.3.1.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_running`

#### 5.3.1.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_running_target`

#### 5.3.1.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_running`

#### 5.3.1.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_running_target`

#### 5.3.1.1.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`ac_humidity_management_active`

#### 5.3.1.1.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heaters_running`

#### 5.3.1.1.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heaters_running_target`

#### 5.3.1.1.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heater_dehumidification_active`

#### 5.3.1.1.9&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`auxheat_active`

#### 5.3.1.1.10&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`ghe_severity`

#### 5.3.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Feature output temperature

#### 5.3.1.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_t`

#### 5.3.1.2.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_t_target`

#### 5.3.1.2.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_t`

#### 5.3.1.2.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_t_target`

#### 5.3.1.2.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heater_t`

#### 5.3.1.2.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heater_t_target`

#### 5.3.1.2.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`ghe_t_increase_target`

#### 5.3.1.2.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`ghe_t_target`

#### 5.3.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Surface area

#### 5.3.1.3.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_A_windowdoor_open`

#### 5.3.1.3.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_A_windowdoor_max`

#### 5.3.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Feature output air / humidity volume

#### 5.3.1.4.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_windowdoor`

#### 5.3.1.4.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_driver_ac`

#### 5.3.1.4.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_driver_ac_target`

#### 5.3.1.4.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_driver_ac_humidity`

#### 5.3.1.4.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_driver_ac_max`

#### 5.3.1.4.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_passenger_ac`

#### 5.3.1.4.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_passenger_ac_target`

#### 5.3.1.4.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_passenger_ac_humidity`

#### 5.3.1.4.9&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_passenger_ac_max`

#### 5.3.1.4.10&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_passenger_ac`

#### 5.3.1.4.11&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_cabin_heater`

#### 5.3.1.4.12&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_cabin_heater_target`

#### 5.3.1.4.13&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Vrate_cabin_heater_max`

#### 5.3.1.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Feature temperature / heat rate

#### 5.3.1.5.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Qrate_envir`

#### 5.3.1.5.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Qrate_engine`

#### 5.3.1.5.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Qrate_windowdoor`

#### 5.3.1.5.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Qrate_driver_ac`

#### 5.3.1.5.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Qrate_ghe`

#### 5.3.1.5.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabinair_Qrate_ghe_plus_losses`

#### 5.3.1.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Temporal

#### 5.3.1.6.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_start_stop_timer`

#### 5.3.1.6.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_start_stop_delay`

#### 5.3.1.6.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_start_stop_timer`

#### 5.3.1.6.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_start_stop_delay`

#### 5.3.1.6.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`ac_humidity_management_start_stop_timer`

#### 5.3.1.6.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`ac_humidity_management_start_stop_delay`

#### 5.3.1.6.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heaters_start_stop_timer`

#### 5.3.1.6.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heaters_start_stop_delay`

#### 5.3.1.6.9&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`auxheat_start_stop_timer`

#### 5.3.1.6.10&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`auxheat_start_stop_delay`

#### 5.3.1.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Integration

#### 5.3.1.7.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Static host vehicle attributes

#### 5.3.1.7.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cabinair_V`

#### 5.3.1.7.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__engine_cvm`

#### 5.3.1.7.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Static or dynamic host vehicle attributes

#### 5.3.1.7.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__number_of_passenger_windows`

#### 5.3.1.7.2.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__number_of_hatches`

#### 5.3.1.7.2.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__number_of_cabin_heaters`

#### 5.3.1.7.2.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__number_of_passenger_ac_units`

#### 5.3.1.7.2.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__number_of_inward_swinging_door_wings`

#### 5.3.1.7.2.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__number_of_outward_swinging_door_wings`

#### 5.3.1.7.2.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__number_of_outward_sliding_door_wings`

#### 5.3.1.7.2.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__passenger_ac_installed`

#### 5.3.1.7.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Inbound host vehicle state (`uchill.osc` &#8592; integration script &#8592; host vehicle)

#### 5.3.1.7.3.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__electrics_on`

#### 5.3.1.7.3.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__engine_running`

#### 5.3.1.7.3.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__engine_t`

#### 5.3.1.7.3.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__engine_t_env`

#### 5.3.1.7.3.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__door_state_sum`

#### 5.3.1.7.3.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__passenger_window_state_sum`

#### 5.3.1.7.3.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__driver_window_state`

#### 5.3.1.7.3.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__hatch_forward_state_sum`

#### 5.3.1.7.3.9&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__hatch_backward_state_sum`

#### 5.3.1.7.3.10&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_driver_ac_air_dispensation`

#### 5.3.1.7.3.11&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_driver_ac_temp`

#### 5.3.1.7.3.12&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_driver_ac_fan`

#### 5.3.1.7.3.13&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_driver_ac`

#### 5.3.1.7.3.14&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_air_circulation`

#### 5.3.1.7.3.15&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_passenger_ac`

#### 5.3.1.7.3.16&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_auxheat`

#### 5.3.1.7.3.17&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_humidity_management`

#### 5.3.1.7.3.18&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_cabin_heaters`

#### 5.3.1.7.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Outbound host vehicle state (`uchill.osc` &#8594; integration script &#8594; host vehicle)

#### 5.3.1.7.4.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_driver_ac_led`

#### 5.3.1.7.4.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_air_circulation_led`

#### 5.3.1.7.4.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_passenger_ac_led`

#### 5.3.1.7.4.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_auxheat_led`

#### 5.3.1.7.4.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_humidity_management_led`

#### 5.3.1.7.4.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cp_cabin_heaters_led`

#### 5.3.1.7.4.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__cabinair_Qrate_engine_fanheatcooling`

#### 5.3.1.7.4.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`uchill_integration__engine_Qrate_auxheat`

#### 5.3.1.7.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Hooks

#### 5.3.1.7.5.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_fan_sound_vol`

#### 5.3.1.7.5.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_fan_sound_vol_target`

#### 5.3.1.7.5.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_int_sound_vol`

#### 5.3.1.7.5.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`driver_ac_int_sound_vol_target`

#### 5.3.1.7.5.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_fan_sound_vol`

#### 5.3.1.7.5.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_fan_sound_vol_target`

#### 5.3.1.7.5.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_int_sound_vol`

#### 5.3.1.7.5.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`passenger_ac_int_sound_vol_target`

#### 5.3.1.7.5.9&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`ac_ext_sound_vol`

#### 5.3.1.7.5.10&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`ac_ext_sound_vol_target`

#### 5.3.1.7.5.11&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heaters_sound_vol`

#### 5.3.1.7.5.12&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_heaters_sound_vol_target`

#### 5.3.1.7.5.13&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`auxheat_sound_vol`

#### 5.3.1.7.5.14&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`auxheat_sound_vol_target`

#### 5.3.1.7.5.15&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_window_int_misting`

#### 5.3.1.7.5.16&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_window_int_misting_target`

#### 5.3.1.7.5.17&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_window_ext_misting`

#### 5.3.1.7.5.18&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_window_ext_misting_target`

#### 5.3.1.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Miscellaneous

#### 5.3.1.8.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`cabin_dew_point

#### 5.3.1.8.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`env_dew_point`

#### 5.3.1.8.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;`env_rel_humidity

#### 5.3.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;External

In order to function properly, `uchill.osc` additionally relies upon the following external—yet still host vehicle-independent—variables:

Name of variable | Reads from | Writes to
-----------------|------------|----------
`Timegap`<sup>[1](#external_variable_table_remark_1)</sup> | X |
`Weather_Temperature`<sup>[1](#external_variable_table_remark_1)</sup> | X |
`Weather_AbsHum`<sup>[1](#external_variable_table_remark_1)</sup> | X |
`SunAlt`<sup>[1](#external_variable_table_remark_1)</sup> | X |
`Envir_Brightness`<sup>[2](#external_variable_table_remark_2)</sup> | X |
`Velocity`<sup>[2](#external_variable_table_remark_2)</sup> | X |
`humans_count`<sup>[2](#external_variable_table_remark_2)</sup> | X |
`Debug_[0-5]`<sup>[2](#external_variable_table_remark_2)</sup> | | X
`Cabinair_Temp`<sup>[2](#external_variable_table_remark_2)</sup> | X | X
`Cabinair_absHum`<sup>[2](#external_variable_table_remark_2)</sup> | X | X
`Cabinair_relHum`<sup>[2](#external_variable_table_remark_2)</sup> | X |
`PrecipType`<sup>[2](#external_variable_table_remark_2)</sup> | X |
`PrecipRate`<sup>[2](#external_variable_table_remark_2)</sup> | X |

<sub><a name="external_variable_table_remark_1">1</a>: OMSI [*System variable*](http://www.omnibussimulator.de/omsiwiki.de/index.php?title=System-_und_vordefinierte_lokalen_Variablen#Systemvariablen)</sub><br/>
<sub><a name="external_variable_table_remark_2">2</a>: OMSI [*On-demand local variable*](http://www.omnibussimulator.de/omsiwiki.de/index.php?title=System-_und_vordefinierte_lokalen_Variablen#Vordefinierte_lokale_Variablen)</sub>

#### 5.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Constants
