[<sub>&#8592; Chapter 4 - Using UCHill</sub>](./4_usage.md) <sub>|</sub> [<sub>Index</sub>](./0_index.md)
***
#### Chapter 5
## Technical reference
***
This chapter goes over some of UCHill's implementation details as well as customization options.

#### 5.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Overview

The basic structure of the original M+R script has been maintained. For each function, the primary macro, `cabinair_frame`:
* Evaluates whether a function is "active", i.e., if preconditions hold for it to bring about change to system state.
* Calculates, if the above evaluation has succeeded, the function's delta (change or rate of change).
* Determines, for sound-emitting functions, the sound volume.

Lastly it computes the final values that (measurable) system state comprises, off of their initial values and corresponding deltas. For example, in the case of cabin temperature, the overall difference from the cabin temperature the frame before is the summation of the temperature rates of all functions that affect temperature.

#### 5.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Integration adapter

As already noted in [2.2.2.1](./2_installation_integration.md#2221authoring-the-integration-adapter), `uchill.osc` depends on an intermediate adapter script in order to retrieve and update vehicle-specific information / state in a portable fashion. The sequence-like diagram below depicts the interaction between the two scripts:
![main_script_adapter_script_interaction](http://i.imgur.com/WFOc5g2.png)
In plain English, the interaction comprises the following steps:

1. During initialization, after the main script of the vehicle has delegated to the `uchill_init` macro:
    1. `uchill_init` delegates to `uchill_integration__init`, which performs the initialization logic (if any) needed by the adapter script.
    1. `uchill_init` delegates to `uchill_integration__acquire_static_vehicle_attributes`, which assigns values of vehicle-specific variables and/or constants, representing *static* vehicle attributes (e.g., the vehicle's cabin's air volume capacity), to their corresponding integration variables.
    1. `uchill_init` delegates to `uchill_integration__acquire_dynamic_vehicle_attributes`, which assigns values of vehicle-specific variables, representing *dynamic* vehicle attributes (e.g., whether a roof-mounted A/C unit is present, in the case of a vehicle allowing in-game (de-)installation of the unit), to their corresponding integration variables.
1. During each frame, after the main script of the vehicle has delegated to the `uchill_frame` macro, and *before* `uchill_frame` delegates to `cabinair_frame` (wherein UCHill's core logic lies):
    1. `uchill_frame` delegates to `uchill_integration__acquire_dynamic_vehicle_attributes` (see above).
    1. `uchill_frame` delegates to `uchill_integration__acquire_vehicle_state`, which assigns values of vehicle-specific variables, representing actual vehicle *state* (e.g. whether the engine is running), to the corresponding integration variables.
1. Finally, during each frame, after the main script of the vehicle has delegated to the `uchill_frame` macro, and *after* `cabinair_frame` has returned:
    * `uchill_frame` delegates to `uchill_integration__actualize_vehicle_state`, which, unlike the previous macros, is responsible for assigning the values of integration variables set by `uchill.osc` *itself* (e.g., state of a controller's indicator), to corresponding vehicle-specific variables.

In summary:
* `uchill_integration__init` initializes the adapter.
* `uchill_integration__actualize_vehicle_state` *pushes* information from `uchill.osc` to the rest of the vehicle.
* The remaining of the integration macros *pull* information from the vehicle to `uchill.osc`.

Of course, in cases where the information provided by the vehicle does not fully adhere to the format or semantics `uchill.osc` expects it to, or the other way around, it is the job of the integration adapter's macros to convert between the two. It is also their job to declare any other variables that the remainder of the vehicle's infrastructure expects to be provided by the cooling / heating script, and set their values.

#### 5.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Variables

#### 5.3.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Local

The following are the variables declared and employed by `uchill.osc`. Note that solely variables that might be of some value to third-party scripts are enumerated in this section—those internal to the script, i.e., those being of "technical" nature and/or having no stable contract or strict value set, are disregarded. Also keep in mind that, unless otherwise specified, reassigning any variable(s) externally will likely cause `uchill.osc` to break, unless you know what you are doing.

As it currently stands, due to the amount of work that it would require, as well as the likelihood of the resulting document becoming impossible to maintain as a result, variable relationships are currently not given herein. The inline commentary of `uchill.osc` might provide some further insight; if not, feel free to ask along.

#### 5.3.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Function state

Variable | Purpose | Unit | Value
---------|---------|------|------
`driver_ac_running`<br/>`driver_ac_running_target`<br/>`passenger_ac_running`<br/>`passenger_ac_running_target` | Current / Target F1 / F2 status. | | -1 = pre-heating/-cooling<br/>0 = stopped or running in maintenance<br/>1 = running normally<br/>2 = running in eco
`ac_auto_humidity_management_active` | Whether F3 is active in AM, AS-AP. | | {0, 1}
`ac_humidity_management_active` | Whether F3 is active in AM, regardless of the AP in effect. | | {0, 1}
`cabin_heater_dehumidification_active` | Whether F3 is active in CM. | | {0, 1}
`auxheat_active` | Current F4 status. | | {0, 1}
`cabin_heaters_running`<br/>`cabin_heaters_running_target` | Current / Target F5 status. | | {0, 1}
`ghe_intensity` | Overall GhE severity. | | [0, 1]

#### 5.3.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output temperature

Variable | Purpose | Unit | Value
---------|---------|------|------
`driver_ac_t`<br/>`driver_ac_t_target`<br/>`passenger_ac_t`<br/>`passenger_ac_t_target` | Current / Target F1 / F2 output temperature. | °C |
`cabin_heater_t`<br/>`cabin_heater_t_target` | Current / Target F5 output temperature. | °C |
`ghe_t_increase_target` | Maximum cabin temperature increase due to GhE in current context. Does not take losses (due to e.g. open windows) into account. | °C | ≥ 0
`ghe_t_target` | Maximum attainable overall cabin temperature due to GhE in current context. Does not take losses (due to e.g. open windows) into account. | °C |

#### 5.3.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Surface area

Variable | Purpose | Unit | Value
---------|---------|------|------
`cabinair_A_open_surfaces` | Surface area of currently open doors, windows, and hatches. | m<sup>2</sup> | ≥ 0
`cabinair_A_openable_surfaces` | Total openable surface area (doors + windows + hatches). | m<sup>2</sup> | ≥ 0

#### 5.3.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output air / humidity volume

Variable | Purpose | Unit | Value
---------|---------|------|------
`cabinair_Vrate_open_surfaces` | Current air flow between the cabin and the environment, due to open doors, windows, and hatches. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_driver_ac`<br/>`cabinair_Vrate_driver_ac_target`<br/>`cabinair_Vrate_passenger_ac`<br/>`cabinair_Vrate_passenger_ac_target` | Current / Target F1 / F2 output air flow. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_driver_ac_max`<br/>`cabinair_Vrate_passenger_ac_max` | Maximum F1 / F2 output air flow. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_driver_ac_humidity`<br/>`cabinair_Vrate_passenger_ac_humidity` | Current humidity flow between the cabin and the environment, via F1 / F2. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_cabin_heater`<br/>`cabinair_Vrate_cabin_heater_target` | Current / Target F5 output air flow. | m<sup>3</sup>/s | ≥ 0
`cabinair_Vrate_cabin_heater_max` | Maximum F5 output air flow. | m<sup>3</sup>/s | ≥ 0

#### 5.3.1.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Temperature - heat transfer

Variable | Purpose | Unit | Value
---------|---------|------|------
`cabinair_Qrate_env_conduction` | Current conductive heat transfer between the cabin and the environment. Positive sign implies cabin heat loss. | J/s |
`cabinair_Qrate_engine` | Current heat transfer between the cabin and the engine. Positive sign implies cabin heat gain. | J/s |
`cabinair_Qrate_env_convection` | Current temperature rate corresponding to convective heat transfer between the cabin and the environment, due to open doors, windows and hatches. Positive sign implies cabin temperature increase. | °C/s |
`cabinair_Qrate_driver_ac`<br/>`cabinair_Qrate_passenger_ac` | Current F1 / F2 temperature rate. Positive sign implies cabin temperature increase. | °C/s |
`cabinair_Qrate_engine_fanheatcooling`<sup>[1](#5315_variable_table_remark_1)</sup> | Current heat transfer from the engine to the cabin (heaters). Always a heat loss for the engine. | J/s | ≥ 0
`engine_Qrate_auxheat`<sup>[1](#5315_variable_table_remark_1)</sup>| Current engine heat gain due to F4. | J/s | ≥ 0
`cabinair_Qrate_ghe_net` | Temperature rate corresponding to current net GhE-induced cabin radiative heat. Does not take losses (e.g., `cabinair_Qrate_env_conduction`) into account. | °C/s | ≥ 0
`cabinair_Qrate_ghe_gross` | Temperature rate corresponding to current gross GhE-induced cabin radiative heat. Balances out losses (e.g., `cabinair_Qrate_env_conduction`) so as to (artificially) enforce desired cabin temperature increase. | °C/s | ≥ 0

<sup><a name="5315_variable_table_remark_1">1</a>: As these are supposed to affect the engine's temperature, it is the job of the adapter and/or the engine script to employ them as appropriate.</sup>

#### 5.3.1.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Temporal

Variable | Purpose | Unit | Value
---------|---------|------|------
`driver_ac_start_stop_delay`<br/>`passenger_ac_start_stop_delay`<br/>`ac_humidity_management_start_stop_delay`<br/>`auxheat_start_stop_delay`<br/>`cabin_heaters_start_stop_delay` | Aggregate (i.e., accounting for all factors of start / stop inertia) function start / stop delay. | s | -1 = no state transition pending<br/>≥ 0 = otherwise
`driver_ac_start_stop_timer`<br/>`passenger_ac_start_stop_timer`<br/>`ac_humidity_management_start_stop_timer`<br/>`auxheat_start_stop_timer`<br/>`cabin_heaters_start_stop_timer` | Function (de-)activation timer, counting down from the value of its corresponding delay variable to zero. | s | 0 = no state transition pending<br/>> 0 = otherwise

#### 5.3.1.7&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Integration

#### 5.3.1.7.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Static host vehicle attribute

The integration adapter is responsible for *setting* those during invocation of its `uchill_integration__acquire_static_vehicle_attributes` macro.

Variable | Purpose | Related quasi-standard variables / constants | Unit | Value
---------|---------|----------------------------------------------|------|------
`uchill_integration__cabinair_V` | The host vehicle's cabin's air capacity. Always static as a side effect of the passenger cabin's configuration file's static nature. | `cabinair_V`<sup>[1](#53171_variable_table_remark_1)</sup> | m<sup>3</sup> | > 0

<sup><a name="53171_variable_table_remark_1">1</a>: Refers to a constant.</sup>

#### 5.3.1.7.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Static or dynamic host vehicle attribute

The integration adapter is responsible for *setting* those during invocation of either its `uchill_integration__acquire_static_vehicle_attributes` or its `uchill_integration__acquire_dynamic_vehicle_attributes` macro.

Variable | Purpose | Related quasi-standard variables / constants | Unit | Value
---------|---------|----------------------------------------------|------|------
`uchill_integration__number_of_inward_swinging_door_wings` | The host vehicle's number of inward-swinging door wings. | | | integer, ≥ 0
`uchill_integration__number_of_outward_swinging_door_wings` | The host vehicle's number of outward-swinging door wings. | | | integer, ≥ 0
`uchill_integration__number_of_outward_sliding_door_wings` | The host vehicle's number of outward-sliding door wings. | | | integer, ≥ 0
`uchill_integration__number_of_folding_passenger_windows` | The host vehicle's number of (folding) passenger windows. | | | integer, ≥ 0
`uchill_integration__number_of_hatches` | The host vehicle's number of roof hatches. | | | integer, ≥ 0
`uchill_integration__number_of_cabin_heaters` | The host vehicle's number of cabin heaters. | | | integer, ≥ 0
`uchill_integration__number_of_passenger_ac_units` | The host vehicle's maximum number of install-able roof-mounted A/C units. | | | integer, ≥ 0
`uchill_integration__passenger_ac_installed` | Presence or absence of these A/C units. | | | {0, 1}

#### 5.3.1.7.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Inbound host vehicle state (`uchill.osc` &#8592; integration script &#8592; host vehicle)

The integration adapter is responsible for *setting* those during invocation of its `uchill_integration__acquire_vehicle_state` macro.

Variable | Purpose | Related quasi-standard variables / constants | Unit | Value
---------|---------|----------------------------------------------|------|------
`uchill_integration__electrics_on` | The host vehicle's electrics' state. | `elec_busbar_main`<br/>`elec_busbar_avail`<br/>`elec_busbar_minV`<sup>[3](#53173_variable_table_remark_3)</sup> | | {0, 1}
`uchill_integration__engine_running` | The host vehicle's engine's state. | `engine_n` | | {0, 1}
`uchill_integration__engine_t` | The host vehicle's engine's temperature. | `engine_temperature` | °C |
`uchill_integration__engine_t_env` | The host vehicle's engine's chamber's temperature. | `engine_temperature_envir` | °C |
`uchill_integration__door_wing_state_sum` | Summation of all the host vehicle's doors' wings' opening states<sup>[1](#53173_variable_table_remark_1)</sup>. | `door_[0-7]` | |
`uchill_integration__passenger_window_state_sum` | Summation of all the host vehicle's passenger windows' opening states<sup>[1](#53173_variable_table_remark_1)</sup>. | `cp_klappfenster_[1-4]` | |
`uchill_integration__driver_window_state` | Opening state<sup>[1](#53173_variable_table_remark_1)</sup> of the host vehicle's driver's window. | `cp_fahrerfenster_pos` | | [0, 1]
`uchill_integration__hatch_forward_state_sum` | Summation of the opening states<sup>[1](#53173_variable_table_remark_1)</sup> of all the host vehicle's hatches that are open in forward-facing position (fully-opened position implies forward position). | `cp_dachluke_[1-3]` | |
`uchill_integration__hatch_backward_state_sum` | Summation of the opening states<sup>[1](#53173_variable_table_remark_1)</sup> of all the host vehicle's hatches that are open in backwars-facing position (fully-opened position implies backward position). | `cp_dachluke_[1-3]` | |
`uchill_integration__cp_driver_ac_air_dispensation`<sup>[2](#53173_variable_table_remark_2)</sup> | A1's controller's state. | | |
`uchill_integration__cp_driver_ac_t` | A2's controller's state. | | | [0, 1]
`uchill_integration__cp_driver_ac_fan` | A3's controller's state. | | | [0, 1]
`uchill_integration__cp_air_circulation` | A4's controller's state. | | | {0, 1}
`uchill_integration__cp_driver_ac` | F1's controller's state. | | | {0, 1}
`uchill_integration__cp_passenger_ac` | F2's controller's state. | | | {0, 1}
`uchill_integration__cp_humidity_management` | F3's controller's state. | | | {0, 1}
`uchill_integration__cp_auxheat` | F4's controller's state. | | | {0, 1}
`uchill_integration__cp_cabin_heaters` | F5's controller's state. | | | {0, 1}

<sup><a name="53173_variable_table_remark_1">1</a>: Lying in the interval [0, 1], with the bounds respectively expressing the fully closed and fully opened state.</sup><br/>
<sup><a name="53173_variable_table_remark_2">2</a>: Currently unused; may be disregarded.</sup><br/>
<sup><a name="53173_variable_table_remark_3">3</a>: Refers to a constant.</sup>

#### 5.3.1.7.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Outbound host vehicle state (`uchill.osc` &#8594; integration script &#8594; host vehicle)

The integration adapter is responsible for *reading* those and assigning their—post-processed, as appropriate—values to the corresponding vehicle-specific variables, during invocation of its `uchill_integration__actualize_vehicle_state` macro.

Variable | Purpose | Related quasi-standard variables / constants | Unit | Value
---------|---------|----------------------------------------------|------|------
`uchill_integration__cp_air_circulation_indicator` | Indicator state of A4's controller. | | | {0, 1}
`uchill_integration__cp_driver_ac_indicator` | Indicator state of F1's controller. | | | {0, 1}
`uchill_integration__cp_passenger_ac_indicator` | Indicator state of F2's controller. | | | {0, 1}
`uchill_integration__cp_humidity_management_indicator` | Indicator state of F3's controller. | | | {0, 1}
`uchill_integration__cp_auxheat_indicator` | Indicator state of F4's controller. | | | {0, 1}
`uchill_integration__cp_cabin_heaters_indicator` | Indicator state of F5's controller. | | | {0, 1}

#### 5.3.1.7.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Hooks

Variable | Purpose | Unit | Value
---------|---------|------|------
`driver_ac_fan_sound_vol`<br/>`driver_ac_fan_sound_vol_target`<br/>`driver_ac_int_sound_vol`<br/>`driver_ac_int_sound_vol_target` | Current / Target F1 fan / interior A/C sound volume. | | [0, 1]
`passenger_ac_fan_sound_vol`<br/>`passenger_ac_fan_sound_vol_target` | Current / Target F2 fan sound volume. | | [0, 1]
`passenger_ac_int_sound_vol`<br/>`passenger_ac_int_sound_vol_target` | Current / Target interior A/C sound volume of F2 and/or F3 (in AM). | | [0, 1]
`ac_ext_sound_vol`<br/>`ac_ext_sound_vol_target` | Current / Target exterior A/C sound volume; shared by any of F1 / F2 / F3 (in AM). | | [0, 1]
`auxheat_sound_vol`<br/>`auxheat_sound_vol_target` | Current / Target F4 sound volume. | | [0, 1]
`cabin_heaters_sound_vol`<br/>`cabin_heaters_sound_vol_target` | Current / Target F5 sound volume. | | [0, 1]
`window_int_misting`<br/>`window_int_misting_target`<br/>`window_ext_misting`<br/>`window_ext_misting_target` | Current / Target WME on the inside / outside. | [0, 1]

#### 5.3.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;External

In order to function properly, `uchill.osc` additionally relies upon some external variables.

#### 5.3.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;OMSI-built-in

Variable | Read| Write | [System variable](http://www.omnibussimulator.de/omsiwiki.de/index.php?title=System-_und_vordefinierte_lokalen_Variablen#Systemvariablen) | [(On-demand-)Local variable](http://www.omnibussimulator.de/omsiwiki.de/index.php?title=System-_und_vordefinierte_lokalen_Variablen#Vordefinierte_lokale_Variablen)
---------|-----------|------------|------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------
`Timegap` | X | | X |
`Weather_Temperature` | X | | X |
`Weather_AbsHum` | X | | X |
`SunAlt` | X | | X |
`Envir_Brightness` | X | | | X
`Velocity` | X | | | X
`humans_count` | X | | | X
`Debug_[0-5]` | | X | | X
`Cabinair_Temp` | X | X | | X
`Cabinair_absHum` | X | X | | X
`Cabinair_relHum` | X | | | X
`PrecipType` | X | | | X
`PrecipRate` | X | | | X

#### 5.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Constants

#### 5.4.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Local

The following is a non-exhaustive list of constants appearing within `uchill.osc`, some of which may *not* be fit for customization. Note that the script does not perform any sanitization of constant values whatsoever; also, constants' names might be misleading. Therefore, before modifying a constant's value, consider taking a (figurative) moment to study the implementation's use of that constant—what it actually represents, which values, value range(s), or other constraints it is expected to adhere to, and how it affects dependent variables.

#### 5.4.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Function effectiveness

#### 5.4.1.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output temperature

Constant | Purpose | Unit
---------|---------|-----
`ac_heating_loss_per_degree` | Expresses, in a heating context, effectiveness decline of F1 and F2, in terms of output temperature decrease, proportionally to the environmental (when operating in FM) or cabin (when in RM) temperature's negative departure from the function's target output temperature. | °C
`ac_cooling_loss_per_degree` | Expresses, in a cooling context, effectiveness decline of F1 and F2, in terms of output temperature increase, proportionally to the environmental (when operating in FM) or cabin (when in RM) temperature's positive departure from the function's target output temperature. | °C
`driver_ac_min_selectable_t` | Minimum temperature setting of A1; only relevant for F1 in AM. | °C
`driver_ac_min_max_selectable_t_interval` | Departure of maximum temperature setting of A1 from `driver_ac_min_selectable_t`. | °C
`ac_heating_output_t_max` | Absolute maximum output air temperature of F1 (in AM) and F2, when operating in a heating context. | °C
`ac_cooling_output_t_min` | Absolute minimum output air temperature of F1 (in AM) and F2, when operating in a cooling context. | °C
`fan_heater_output_t_increase_max` | F1's maximum output air temperature departure from environmental (when operating in FM) or cabin (when in RM) temperature, when operating in EM. | °C
`heat_exchanger_effectiveness` | Artificial effectiveness factor affecting the output temperature of F5. |

#### 5.4.1.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Output air / humidity volume

Constant | Purpose | Unit
---------|---------|-----
`cabinair_A_sliding_driver_window` | Openable (sliding) driver window surface area. | m<sup>2</sup>
`cabinair_A_sliding_driver_window_min` | Lower bound of `cabinair_A_sliding_driver_window`, expressing imperfect insulation. | m<sup>2</sup>
`cabinair_A_per_folding_passenger_window` | Openable surface area per (folding) passenger window. | m<sup>2</sup>
`cabinair_A_per_folding_passenger_window_min` | Lower bound of `cabinair_A_per_folding_passenger_window`, expressing imperfect insulation. | m<sup>2</sup>
`cabinair_A_per_forwards_open_hatch` | Surface area considered<sup>[1](#5412_constant_table_remark_1)</sup> openable per hatch, when the hatch is opened in forward-facing position and vehicle's velocity is positive, or when the hatch is opened in backward-facing position and vehicle's velocity is negative. For a fully-opened hatch, `cabinair_A_per_forwards_open_hatch` + `cabinair_A_per_backwards_open_hatch` is used. | m<sup>2</sup>
`cabinair_A_per_backwards_open_hatch` | Surface area considered<sup>[1](#5412_constant_table_remark_1)</sup> openable per hatch, when the hatch is opened in backward-facing position and vehicle's velocity is positive, or when the hatch is opened in forward-facing position and vehicle's velocity is negative. For a fully-opened hatch, `cabinair_A_per_forwards_open_hatch` + `cabinair_A_per_backwards_open_hatch` is used. | m<sup>2</sup>
`cabinair_A_per_hatch_at_standstill` | Surface area considered<sup>[1](#5412_constant_table_remark_1)</sup> openable per hatch, when the hatch is opened either in forward- or in backward-facing position, and the vehicle is stationary. For a fully-opened hatch, 2 * `cabinair_A_per_hatch_at_standstill` is used. | m<sup>2</sup>
`cabinair_A_per_hatch_min` | Lower bound of `cabinair_A_per_forwards_open_hatch`, `cabinair_A_per_backwards_open_hatch`, and `cabinair_A_per_hatch_at_standstill` (or of the sum thereof, or of the product 2 * `cabinair_A_per_hatch_at_standstill`, as appropriate depending on the context), expressing imperfect insulation. | m<sup>2</sup>
`cabinair_A_per_door_wing` | Openable surface area per door wing. | m<sup>2</sup>
`cabinair_A_per_inward_swinging_door_wing_min` | Lower bound of `cabinair_A_per_door_wing`, in the case of an inward-swinging door wing, expressing imperfect insulation. | m<sup>2</sup>
`cabinair_A_per_outward_swinging_door_wing_min` | Lower bound of `cabinair_A_per_door_wing`, in the case of an outward-swinging door wing, expressing imperfect insulation. | m<sup>2</sup>
`cabinair_A_per_outward_sliding_door_wing_min` | Lower bound of `cabinair_A_per_door_wing`, in the case of an outward-sliding door wing, expressing imperfect insulation. | m<sup>2</sup>
`cabinair_A_driver_ac` | Surface area of F1's vents. | m<sup>2</sup>
`cabinair_A_per_passenger_ac_unit` | Surface area of each of F2's units' vents. | m<sup>2</sup>
`cabinair_A_per_cabin_heater` | Convection-relevant surface area of each of F5's units. | m<sup>2</sup>
`cabinair_openable_surface_convection_effectiveness` | Generic, artificial effectiveness factor, affecting the overall convective heat transfer ability of windows, hatches, doors, and F1, when operating in EM, FM. |
`driver_ac_effectiveness` | Artificial effectiveness factor, affecting the convective ability of F1 in AM, FM. |
`driver_ac_recirculation_mode_effectiveness` | Artificial effectiveness factor, affecting the convective ability of F1 in RM (regardless of operational mode). |
`driver_ac_humidity_relevant_air_volume_recycling_factor` | Affects the output humidity volume of F1 in RM (regardless of operational mode). |
`driver_ac_idle_air_volume_factor` | Affects the output air volume of F1, when operating in AM (regardless of A4) or in EM-RM, and the function is in an idle state. |
`driver_ac_engine_off_air_volume_factor` | Affects the output air volume of F1, when operating in AM (regardless of A4) or in EM-RM, and the engine is off. |
`driver_ac_powerless_non_recirculated_air_volume_factor` | Affects the output air volume of F1, when in EM-RM, and both the engine and the electrics are off. |
`passenger_ac_effectiveness` | Artificial effectiveness factor, affecting the convective ability of F2 in FM. |
`passenger_ac_recirculation_mode_effectiveness` | Artificial effectiveness factor affecting the convective ability of F2 in RM. |
`passenger_ac_humidity_relevant_air_volume_recycling_factor` | Affects the output humidity volume of F2 in RM. |
`passenger_ac_idle_air_volume_factor` | Affects the output air volume of F2, when in an idle state. |
`passenger_ac_engine_off_air_volume_factor` | Affects the output air volume of F2, when the engine is off. |
`ac_dehumidification_effectiveness` | Artificial effectiveness factor, affecting the dehumidification rate of F3 in AM. |
`ac_humidification_effectiveness` | Artificial effectiveness factor affecting the humidification rate of F3 in AM. |
`cabin_heater_effectiveness` | Artificial effectiveness factor, affecting the output air volume of F5. |

<sup><a name="5412_constant_table_remark_1">1</a>: We differentiate between the three cases—the vehicle moving forwards, backwards, and being stationary—in order to (artlessly) simulate the effect of aerodynamics on convection via the hatches. In the real world, of course, the surface area remains constant.</sup>

#### 5.4.1.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Temperature - heat transfer

Constant | Purpose | Unit
---------|---------|-----
`engine_Qrate_auxheat_const` | Engine heat gain due to F4. | J/s
`ghe_t_increase_rate_max` | Maximum (net) cabin temperature increase rate due to the GhE. | °C/s
`ghe_time_max` | Time required for GhE to achieve maximum cabin temperature increase, assuming ideal (minimal losses) and constant vehicle and environmental conditions. | s

#### 5.4.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Start / Stop precondition

Constant | Purpose | Unit
---------|---------|-----
`ac_cooling_cabin_t_min` | F1 (in AM) and F2 will not cool the cabin down below this temperature. | °C
`ac_heating_cabin_t_max` | F1 (in AM) and F2 will not heat the cabin up above this temperature. | °C
`ac_env_t_min` | F1 (in AM), F2, and F3, will refuse to operate if the environmental temperature is below this temperature. | °C
`ac_env_t_max` | F1 (in AM), F2, and F3, will refuse to operate if the environmental temperature is above this temperature. | °C
`ac_cooldown_threshold_t_diff` | F1 (in AM) and F2 will not start if the cabin's temperature's departure from, depending on the context, `ac_cooling_cabin_t_min` or `ac_heating_cabin_t_max`, is lower than this value. | °C
`driver_ac_start_delay_env_factor` | Used to increase F1's (in AM) start-up delay, proportionally to the environmental temperature's departure from the function's output temperature. | s/°C
`passenger_ac_start_delay_env_factor` | Used to increase F2's start-up delay, proportionally to the function's output temperature's departure from the environmental temperature. | s/°C
`auxheat_engine_t_max` | F4 will not heat the engine up above this temperature. | °C
`auxheat_start_delay_env_factor`<br/>`auxheat_engine_t_normal` | Used to increase F4's start-up delay, proportionally to the engine's temperature's departure from `auxheat_engine_t_normal` | s/°C<br/>°C
`cabin_heater_cabin_t_max` | F5 will not heat the cabin up above this temperature. | °C
`cabin_heater_engine_t_min` | F5 will refuse to operate if the engine's temperature is below this temperature. | °C
`cabin_heater_turbo_mode_env_t_max` | F5 will operate in turbo profile if the environmental temperature is lower than this value (and other apposite preconditions hold). | °C
`cabin_heater_turbo_mode_engine_t_max` | F5 will operate in turbo profile if the engine's temperature is below this value (and other apposite preconditions hold). | °C
`cabin_heater_start_delay_env_factor`<br/>`cabin_heater_engine_t_normal` | Used to increase F5's start-up delay, proportionally to the engine's temperature's departure from `cabin_heater_engine_t_normal`. | s/°C<br/>°C
`cabin_heater_cooldown_threshold_t_diff` | F5 will not start if the cabin's temperature's departure from `cabin_heater_cabin_t_max` is lower than this value. | °C
`window_misting_dew_point_threshold` | Expresses maximum departure of the cabin's or the environment's temperature to its respective dew point, beyond which window misting will not occur on the corresponding side. | °C
`dehumidification_rel_cabin_humidity_min` | F3 will not dehumidify the cabin below this relative humidity. | 
`humidification_rel_cabin_humidity_max` | F3 (in AM) will not humidify the cabin above this relative humidity. |
`ac_auto_humidity_management_dehumidification_start_rel_cabin_humidity_max` | F3 (in AM-AP) will not dehumidify the cabin below this relative humidity. |
`ac_auto_humidity_management_humidification_start_rel_cabin_humidity_min` | F3 (in AM-AP) will not humidify the cabin above this relative humidity. |
`ac_auto_humidity_management_dehumidification_stop_rel_cabin_humidity_min` | F3 (in AM-AP) will stop dehumidifying the cabin, once its relative humidity has exceeded this value. |
`ac_auto_humidity_management_humidification_stop_rel_cabin_humidity_max` | F3 in (AM-AP) will stop humidifying the cabin, once its relative humidity has dropped below below this value. |
`humidity_management_cooldown_threshold_humidity_diff` | F3 will not start if the cabin's relative humidity's departure from, depending on the context, `dehumidification_rel_cabin_humidity_min`, `humidification_rel_cabin_humidity_max`, `ac_auto_humidity_management_dehumidification_start_rel_cabin_humidity_max`, or `ac_auto_humidity_management_humidification_start_rel_cabin_humidity_min`, is lower than this value. |
`ac_humidity_management_start_delay_env_factor` | Used to increase F3's start-up delay, proportionally or inversely proportionally to the cabin's absolute humidity, when in a dehumidification or humidification context, respectively. | s*m<sup>3</sup>/g
`window_misting_rel_cabin_humidity_min` | Window misting will not occur on the corresponding side when the cabin's or the environment's relative humidity is lower than that.
`driver_ac_start_delay_min`<br/>`passenger_ac_start_delay_min`<br/>`ac_humidity_management_start_delay_min`<br/>`auxheat_start_delay_min`<br/>`cabin_heater_start_delay_min` | Minimum function start-up delay due to signal propagation / acknowledgement delays. | s
`driver_ac_stop_delay_min`<br/>`passenger_ac_stop_delay_min`<br/>`ac_humidity_management_stop_delay_min`<br/>`auxheat_stop_delay_min`<br/>`cabin_heater_stop_delay_min` | Minimum function shut-down delay due to signal propagation / acknowledgement delays. | s
`driver_ac_start_delay_additional_max`<br/>`passenger_ac_start_delay_additional_max`<br/>`ac_humidity_management_start_delay_additional_max`<br/>`auxheat_start_delay_additional_max`<br/>`cabin_heater_start_delay_additional_max` | Maximum function start-up delay due to signal propagation / acknowledgement delays. | s
`driver_ac_stop_delay_additional_max`<br/>`passenger_ac_stop_delay_additional_max`<br/>`ac_humidity_management_stop_delay_additional_max`<br/>`auxheat_stop_delay_additional_max`<br/>`cabin_heater_stop_delay_additional_max` | Maximum function shut-down time due to signal propagation / acknowledgement delays. | s

#### 5.4.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Update rate

Constant | Purpose | Unit
---------|---------|-----
`ac_t_out_fast_update_rate` | Update rate of the `driver_ac_t` and `passenger_ac_t` variables, during shut-down or start-up, whilst operating in economy profile. | °C/s
`ac_t_out_slow_update_rate` | Update rate of the `driver_ac_t` and `passenger_ac_t` variables, during start-up, unless operating in economy profile. | °C/s
`cabin_heater_t_out_fast_update_rate` | Update rate of the `cabin_heater_t` variable, during start-up and "early" shut-down. | °C/s
`cabin_heater_t_out_slow_update_rate` | Update rate of the `cabin_heater_t` variable, during "late" / post-deactivation shut-down. | °C/s
`driver_ac_temp_relevant_air_volume_update_rate` | Update rate of the `cabinair_Vrate_driver_ac` variable. | m<sup>3</sup>/s
`passenger_ac_temp_relevant_air_volume_update_rate` | Update rate of the `cabinair_Vrate_passenger_ac` variable. | m<sup>3</sup>/s
`cabin_heater_air_volume_fast_update_rate` | Upate rate of the `cabinair_Vrate_cabin_heater` variable, during start-up. | m<sup>3</sup>/s
`cabin_heater_air_volume_slow_update_rate` | Update rate of the `cabinair_Vrate_cabin_heater` variable, during shut-down. | m<sup>3</sup>/s
`driver_ac_fan_sound_update_rate` | Update rate of the `driver_ac_fan_sound_vol` variable. | s<sup>-1</sup>
`driver_ac_int_sound_update_rate` | Update rate of the `driver_ac_int_sound_vol` variable. | s<sup>-1</sup>
`passenger_ac_fan_sound_update_rate` | Update rate of the `passenger_ac_fan_sound_vol` variable. | s<sup>-1</sup>
`passenger_ac_int_sound_update_rate` | Update rate of the `passenger_ac_int_sound_vol` variable. | s<sup>-1</sup>
`ac_ext_sound_update_rate` | Update rate of the `ac_ext_sound_vol` variable. | s<sup>-1</sup>
`auxheat_sound_update_rate` | Update rate of the `auxheat_sound_vol` variable. | s<sup>-1</sup>
`cabin_heater_sound_update_rate` | Update rate of the `cabin_heaters_sound_vol` variable. | s<sup>-1</sup>
`window_misting_update_rate` | Update rate of the `window_int_misting` and `window_ext_misting` variables. | s<sup>-1</sup>

#### 5.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Macros

The next table provides a summary over some key macros amongst those of `uchill.osc`, (a subset of) which, along with the macros they delegate to, you will most likely have to refer to, when looking for answers to questions not addressed by the documentation, or adapt, in (the not at all unlikely) case the script does not, nor can be adequately customized via its constants so as to, satisfy your particular use case.

Macro | Purpose
------|--------
`cabinair_frame` | This macro, being a remnant of the original M+R script, contains most of the script's core logic. Its concerns, some implemented by the macros it delegates to, others inline, include a) function state adjustment, b) calculation of pertinent output temperatures, air / humidity volumes, emitted sound volumes, and heat / temperature rates, c) calculation and update of overall cabin temperature / humidity, and d) calculation of the degree of window misting. Due to being so conspicuously ad-hoc, the macro is a candidate for further modularization / refactoring in the future.
`calculate_driver_ac_temps`<br/>`calculate_passenger_ac_temps`<br/>`calculate_cabin_heater_temps`<br/>`ghe_impact` | Calculation of output temperature (or temperature rate, in the case of `ghe_impact`).
`actualize_driver_ac_status`<br/>`actualize_passenger_ac_status`<br/>`actualize_auto_humidity_management_status`<br/>`actualize_ac_humidity_management_status`<br/>`actualize_auxheat_status`<br/>`actualize_cabin_heater_status` | Determination of function state.
`calculate_driver_ac_air_exchange_volumes`<br/>`calculate_passenger_ac_air_exchange_volumes`<br/>`calculate_cabin_heater_air_exchange_volume` | Calculation of output air / humidity volume.
`actualize_driver_ac_sound`<br/>`actualize_passenger_ac_sound`<br/>`actualize_ext_ac_sound`<br/>`actualize_auxheat_sound`<br/>`actualize_cabin_heater_sound` | Adjustment of sound volume.
`calculate_window_int_misting_degree`<br/>`calculate_window_ext_misting_degree` | Adjustment of window misting.
***
[<sup>&#8592; Chapter 4 - Using UCHill</sup>](./4_usage.md) <sup>|</sup> [<sup>Index</sup>](./0_index.md)
