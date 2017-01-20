[<sub>&#8592; Chapter 4 - Using *UCHill*</sub>](./4_usage.md)<br/>
***
#### Chapter 5
## Technical reference
***
This chapter goes over some of *UCHill*'s implementation details as well as customization options.

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

Of course, in cases where the information provided by the vehicle is not strictly compatible with the information expected by `uchill.osc`, or vice versa, it is the job of the integration adapter's macros to convert between the two. It is also their job to accordingly declare any other variables that the vehicle expects the cooling / heating script to declare and set them accordingly.

#### 5.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Variables

#### 5.3.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Local

The following are the variables declared and employed by `uchill.osc`. Note that solely variables that might be of some value to third-party scripts are enumerated in this section—those internal to the script, i.e., those being of auxiliary / technical nature and/or have no stable contract and/or strict value set are disregarded. Also keep in mind that, unless otherwise specified, reassigning any variable(s) externally will likely cause `uchill.osc` script to break, unless you know what you are doing.

As it currently stands, due to the amount of work that it would require, as well as the likelihood of the resulting page becoming impossible to maintain as a result, variable (inter-)relationships are currently not given herein. The inline commentary of `uchill.osc` might provide some further insight; if not, feel free to ask along.

#### 5.3.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Feature state

Variable | Purpose | Unit | Values
---------|---------|------|-------
`driver_ac_running`<br/>`driver_ac_running_target`<br/>`passenger_ac_running`<br/>`passenger_ac_running_target` | Current / Target F1 / F2 status. | | -1 = pre-heating/-cooling<br/>0 = stopped or running in maintenance<br/>1 = running normally<br/>2 = running in eco
`auto_humidity_management` | Whether F3 is active in AM / AS-AP. | | {0, 1}
`ac_humidity_management_active` | Whether F3 is active in AM, regardless of the AP in effect. | | {0, 1}
`cabin_heater_dehumidification_active` | Whether F3 is active in CM. | | {0, 1}
`auxheat_active` | Current F4 status. | | {0, 1}
`cabin_heaters_running`<br/>`cabin_heaters_running_target` | Current / Target F5 status. | | {0, 1}
`ghe_severity` | Overall GhE severity. | | [0, 1]

#### 5.3.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Feature output temperature

Variable | Purpose | Unit | Values
---------|---------|------|-------
`driver_ac_t`<br/>`driver_ac_t_target`<br/>`passenger_ac_t`<br/>`passenger_ac_t_target` | Current / Target F1 / F2 output temperature. | °C |
`cabin_heater_t`<br/>`cabin_heater_t_target` | Current / Target F5 output temperature. | °C |
`ghe_t_increase_target` | Maximum cabin temperature increase due to GhE in current context. Does not take losses (e.g., open windows) into account. | °C | ≥ 0
`ghe_t_target` | Maximum attainable overall cabin temperature due to GhE in current context. Does not take losses (e.g., open windows) into account. | °C |

#### 5.3.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Surface area

Variable | Purpose | Unit | Values
---------|---------|------|-------
`cabinair_A_windowdoor_open` | Surface area of currently open doors, windows, and hatches. | m<sup>2</sup> | ≥ 0
`cabinair_A_windowdoor_max` | Total openable surface area (doors + windows + hatches). | m<sup>2</sup> | ≥ 0

#### 5.3.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Feature output air / humidity flow

Variable | Purpose | Unit | Values
---------|---------|------|-------
`cabinair_Vrate_windowdoor` | Current air flow between the cabin and the environment, due to open doors, windows, and hatches. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_driver_ac`<br/>`cabinair_Vrate_driver_ac_target`<br/>`cabinair_Vrate_passenger_ac`<br/>`cabinair_Vrate_passenger_ac_target` | Current / Target F1 / F2 output air flow. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_driver_ac_max`<br/>`cabinair_Vrate_passenger_ac_max` | Maximum F1 / F2 output air flow. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_driver_ac_humidity`<br/>`cabinair_Vrate_passenger_ac_humidity` | Current humidity flow between the cabin and the environment, via F1 / F2. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_cabin_heater`<br/>`cabinair_Vrate_cabin_heater_target` | Current / Target F5 output air flow. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_cabin_heater_max` | Maximum F5 output air flow. | m<sup>3</sup>/s | ≥ 0

#### 5.3.1.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Feature temperature / heat transfer rate

Variable | Purpose | Unit | Values
---------|---------|------|-------
`cabinair_Qrate_envir` | Current conductive heat transfer between the cabin and the environment. Positive sign implies cabin heat loss. | J/s |
`cabinair_Qrate_engine` | Current conductive heat transfer between the cabin and the engine. Positive sign implies cabin heat gain. | J/s |
`cabinair_Qrate_windowdoor` | Current temperature rate between the cabin and the environment, due to open doors, windows and hatches. Positive sign implies cabin temperature increase. | °C/s |
`cabinair_Qrate_driver_ac`<br/>`cabinair_Qrate_passenger_ac` | Current F1 / F2 temperature rate. Positive sign implies cabin temperature increase. | °C/s |
`cabinair_Qrate_engine_fanheatcooling`<sup>[1](#temperature_heat_variable_table_remark_1)</sup> | Current convective heat transfer between the engine and the cabin (heaters). Always a heat loss for the engine. | J/s | ≥ 0
`engine_Qrate_auxheat`<sup>[1](#temperature_heat_variable_table_remark_1)</sup>| Current engine heat gain because of the auxiliary heating. | J/s | ≥ 0
`engine_Qrate_ghe_net` | Current temperature rate corresponding to current net GhE-induced cabin radiative heat. Does not take losses (e.g., `cabinair_Qrate_envir`) into account. | °C/s | ≥ 0
`engine_Qrate_ghe_gross` | Current temperature rate corresponding to current gross GhE-induced cabin radiative heat. Balances out losses (e.g., `cabinair_Qrate_envir`) so as to (artificially) enforce desired cabin temperature increase. | °C/s | ≥ 0

<sub><a name="temperature_heat_variable_table_remark_1">1</a>: As these are supposed to affect the engine's temperature, it is the job of the adapter and/or the engine script to leverage them as appropriate.</sub>

#### 5.3.1.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Temporal

Variable | Purpose | Unit | Values
---------|---------|------|-------
`driver_ac_start_stop_delay`<br/>`passenger_ac_start_stop_delay`<br/>`ac_humidity_management_start_stop_delay`<br/>`cabin_heaters_start_stop_delay`<br/>`auxheat_start_stop_delay` | Aggregate (i.e., accounting for all factors of inertia) feature start stop delay. | s | -1 = no state transition pending<br/>≥ 0 = otherwise
`driver_ac_start_stop_timer`<br/>`passenger_ac_start_stop_timer`<br/>`ac_humidity_management_start_stop_timer`<br/>`cabin_heaters_start_stop_timer`<br/>`auxheat_start_stop_timer` | Feature (de-)activation timer, counting down from the value of its corresponding delay variable to zero. | s | 0 = state transition occurred<br/>≥ 0 = state transition pending

#### 5.3.1.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Integration

#### 5.3.1.7.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Static host vehicle attributes

The integration adapter is responsible for *setting* those during invocation of its `uchill_integration__acquire_static_vehicle_attributes` macro.

Variable | Purpose | Related quasi-standard<br/>vehicle-specific<br/>variable(s) /<br/>constant(s) | Unit | Values
---------|---------|-------------------------------------------------------------------------------|------|-------
`uchill_integration__cabinair_V` | The host vehicle's cabin's air capacity. Always static as a side effect of the passenger cabin's configuration file's static nature. | (constant) `cabinair_V` | m<sup>3</sup> | > 0

#### 5.3.1.7.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Static or dynamic host vehicle attributes

The integration adapter is responsible for *setting* those during invocation of either its `uchill_integration__acquire_static_vehicle_attributes` or its `uchill_integration__acquire_dynamic_vehicle_attributes` macro.

Variable | Purpose | Related quasi-standard<br/>vehicle-specific<br/>variable(s) /<br/>constant(s) | Unit | Values
---------|---------|-------------------------------------------------------------------------------|------|-------
`uchill_integration__number_of_inward_swinging_door_wings` | The host vehicle's number of inward-swinging door wings. | - | | integer, ≥ 0
`uchill_integration__number_of_outward_swinging_door_wings` | The host vehicle's number of outward-swinging door wings. | - | | integer, ≥ 0
`uchill_integration__number_of_outward_sliding_door_wings` | The host vehicle's number of outward-sliding door wings. | - | | integer, ≥ 0
`uchill_integration__number_of_passenger_windows` | The host vehicle's number of passenger windows. | - | | integer, ≥ 0
`uchill_integration__number_of_hatches` | The host vehicle's number of roof hatches. | - | | integer, ≥ 0
`uchill_integration__number_of_cabin_heaters` | The host vehicle's number of cabin heaters. | - | | integer, ≥ 0
`uchill_integration__number_of_passenger_ac_units` | The host vehicle's maximum number of install-able roof-mounted A/C units. | - | | integer, ≥ 0
`uchill_integration__passenger_ac_installed` | Presence or absence of these A/C units. | - | | {0, 1}

#### 5.3.1.7.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Inbound host vehicle state (`uchill.osc` &#8592; integration script &#8592; host vehicle)

The integration adapter is responsible for *setting* those during invocation of its `uchill_integration__acquire_vehicle_state` macro.

Variable | Purpose | Related quasi-standard<br/>vehicle-specific<br/>variable(s) /<br/>constant(s) | Unit | Values
---------|---------|-------------------------------------------------------------------------------|------|-------
`uchill_integration__electrics_on` | The host vehicle's electrics' state. | `elec_busbar_main`<br/>`elec_busbar_avail`<br/>(constant) `elec_busbar`elec_busbar_minV` | | {0, 1}
`uchill_integration__engine_running` | The host vehicle's engine's state. | `engine_n` | | {0, 1}
`uchill_integration__engine_t` | The host vehicle's engine's temperature. | `engine_temperature` | °C |
`uchill_integration__engine_t_env` | The host vehicle's engine's chamber's temperature. | `engine_temperature_envir` | °C |
`uchill_integration__door_state_sum` | Summation of all the host vehicle's door wings' states | `door_[0-7]` | |
`uchill_integration__passenger_window_state_sum` | Summation of all the host vehicle's passenger windows' states<sup>[1](#host_vehicle_inbound_state_integration_variable_table_remark_1)</sup>. | `cp_klappfenster_[1-4]` | |
`uchill_integration__driver_window_state` | State of the host vehicle's driver's window. | `cp_fahrerfenster_pos` | | [0, 1]
`uchill_integration__hatch_forward_state_sum` | Summation of the states<sup>[1](#host_vehicle_inbound_state_integration_variable_table_remark_1)</sup> of all the host vehicle's hatches that are open in forward position (fully-open position implies forward position). | `cp_dachluke_[1-3]` | |
`uchill_integration__hatch_backward_state_sum` | Summation of the states<sup>[1](#host_vehicle_inbound_state_integration_variable_table_remark_1)</sup> of all the host vehicle's hatches that are open in backward position (fully-open position implies backward position). | `cp_dachluke_[1-3]` | |
`uchill_integration__cp_driver_ac_air_dispensation`<sup>[2](#host_vehicle_inbound_state_integration_variable_table_remark_2)</sup> | A1 controller state. | | |
`uchill_integration__cp_driver_ac_t` | A2 controller state. | | | [0, 1]
`uchill_integration__cp_driver_ac_fan` | A3 controller state. | | | [0, 1]
`uchill_integration__cp_air_circulation` | A4 controller state. | | | {0, 1}
`uchill_integration__cp_driver_ac` | F1 controller state. | | | {0, 1}
`uchill_integration__cp_passenger_ac` | F2 controller state. | | | {0, 1}
`uchill_integration__cp_humidity_management` | F3 controller state. | | | {0, 1}
`uchill_integration__cp_auxheat` | F4 controller state. | | | {0, 1}
`uchill_integration__cp_cabin_heaters` | F5 controller state. | | | {0, 1}

<sub><a name="host_vehicle_inbound_state_integration_variable_table_remark_1">1</a>: Lying in the interval [0, 1], with the bounds respectively expressing the fully closed and fully opened state.</sub><br/>
<sub><a name="host_vehicle_inbound_state_integration_variable_table_remark_2">2</a>: Currently unused; may be disregarded.</sub>

#### 5.3.1.7.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Outbound host vehicle state (`uchill.osc` &#8594; integration script &#8594; host vehicle)

The integration adapter is responsible for *reading* those and assigning their—post-processed, as appropriate—values to the corresponding vehicle-specific variables, during invocation of its `uchill_integration__actualize_vehicle_state` macro.

Variable | Purpose | Related quasi-standard<br/>vehicle-specific<br/>variable(s) /<br/>constant(s) | Unit | Values
---------|---------|-------------------------------------------------------------------------------|------|-------
`uchill_integration__cp_air_circulation_led` | A4 controller indicator state. | | | {0, 1}
`uchill_integration__cp_driver_ac_led` | F1 controller indicator state. | | | {0, 1}
`uchill_integration__cp_passenger_ac_led` | F2 controller indicator state. | | | {0, 1}
`uchill_integration__cp_humidity_management_led` | F3 controller indicator state. | | | {0, 1}
`uchill_integration__cp_auxheat_led` | F4 controller indicator state. | | | {0, 1}
`uchill_integration__cp_cabin_heaters_led` | F5 controller indicator state. | | | {0, 1}

#### 5.3.1.7.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Hooks

Variable | Purpose | Unit | Values
---------|---------|------|-------
`driver_ac_fan_sound_vol`<br/>`driver_ac_fan_sound_vol_target`<br/>`driver_ac_int_sound_vol`<br/>`driver_ac_int_sound_vol_target` | Current / Target F1 fan / interior A/C sound volume. | | [0, 1]
`passenger_ac_fan_sound_vol`<br/>`passenger_ac_fan_sound_vol_target` | Current / Target F2 fan sound volume. | | [0, 1]
`passenger_ac_int_sound_vol`<br/>`passenger_ac_int_sound_vol_target` | Current / Target interior A/C sound volume of F2 and/or F3 (in AM). | | [0, 1]
`ac_ext_sound_vol`<br/>`ac_ext_sound_vol_target` | Current / Target exterior A/C sound volume; shared by any of F1 / F2 / F3 (in AM). | | [0, 1]
`auxheat_sound_vol`<br/>`auxheat_sound_vol_target` | Current / Target F4 sound volume. | | [0, 1]
`cabin_heaters_sound_vol`<br/>`cabin_heaters_sound_vol_target` | Current / Target F5 sound volume. | | [0, 1]
`cabin_window_int_misting`<br/>`cabin_window_int_misting_target`<br/>`cabin_window_ext_misting`<br/>`cabin_window_ext_misting_target` | Current / Target WME on the inside / outside.

#### 5.3.1.8&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Miscellaneous

Variable | Purpose | Unit | Values
---------|---------|------|-------
`cabin_dew_point` | A (very rough) approximation of the cabin dew point. | °C |
`env_dew_point` | A (very rough) approximation of the environmental dew point. | °C |
`env_rel_humidity` | A (very rough) approximation of the environmental relative humidity. | | [0, 1]

#### 5.3.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;External

#### 5.3.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;OMSI-built-in

In order to function properly, `uchill.osc` additionally relies upon the following "built—in" OMSI variables:

Name of variable | Read | Write | OMSI [System variable](http://www.omnibussimulator.de/omsiwiki.de/index.php?title=System-_und_vordefinierte_lokalen_Variablen#Systemvariablen) | OMSI [on-demand-local variable](http://www.omnibussimulator.de/omsiwiki.de/index.php?title=System-_und_vordefinierte_lokalen_Variablen#Vordefinierte_lokale_Variablen)
-----------------|------|-------
`Timegap`<sup>[1](#external_variable_table_remark_1)</sup> | X | | X |
`Weather_Temperature`<sup>[1](#external_variable_table_remark_1)</sup> | X | | X |
`Weather_AbsHum`<sup>[1](#external_variable_table_remark_1)</sup> | X | | X |
`SunAlt`<sup>[1](#external_variable_table_remark_1)</sup> | X | | X |
`Envir_Brightness`<sup>[2](#external_variable_table_remark_2)</sup> | X | | | X
`Velocity`<sup>[2](#external_variable_table_remark_2)</sup> | X | | | X
`humans_count`<sup>[2](#external_variable_table_remark_2)</sup> | X | | | X
`Debug_[0-5]`<sup>[2](#external_variable_table_remark_2)</sup> | | X | | X
`Cabinair_Temp`<sup>[2](#external_variable_table_remark_2)</sup> | X | X | | X
`Cabinair_absHum`<sup>[2](#external_variable_table_remark_2)</sup> | X | X | | X
`Cabinair_relHum`<sup>[2](#external_variable_table_remark_2)</sup> | X | | | X
`PrecipType`<sup>[2](#external_variable_table_remark_2)</sup> | X | | | X
`PrecipRate`<sup>[2](#external_variable_table_remark_2)</sup> | X | | | X

#### 5.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Constants
