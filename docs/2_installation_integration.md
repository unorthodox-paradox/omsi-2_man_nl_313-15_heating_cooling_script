## 2 - Installation / integration
***
### Basic installation for *MAN Stadtbusfamilie* vehicles

*<sub>(Last tested against hotfix v. 0.5 of the add-on)</sub>*

Extract the compressed archive's `Vehicles` directory into your root OMSI 2 directory, choosing "Yes" if asked whether merging is desired; no existing (add-on) files will be modified. Next, open the `.bus` file of the vehicle you intend to use the script with, with a text editor, replacing the references to `script\heizung_varlist.txt`, `script\heizung.osc` and `script\heizung_constfile.txt` with `script\heizung_varlist_up.txt`, `script\heizung_up.osc` and `script\heizung_constfile_1[258]_up.txt` (solo, solo-long or articulated—whichever matches your vehicle), respectively.

### Basic installation for other (third-party) vehicles

As long as the script's few dependencies (listed in the header of `heizung_up.osc`) are the standard M+R ones, or derivatives thereof retaining their corresponding standard ones' respective variables, and the vehicle is one of those featuring the typical analog heating / cooling panel (such as *alTerr*'s *MB O530* and *SU* series), integration should be fairly straightforward. The sole non-standard variables the script depends on are the boolean `Dachluke_[1234]_klappe_[12]` variables declared on `cockpit.osc`'s behalf, expressing the state of the hatches; those should be replaced or discarded as appropriate.

The following table provides an overview of the variables pertaining to the heating / cooling panel's buttons and, if available, their corresponding LED indicators. Note that some vehicles incorporate the buttons' triggers (mouse / keyboard events) in their cockpit, rather than their heating script (which actually makes sense from separation-of-concerns perspective); if so, you will have to remove the superfluous triggers from either the targeted vehicle's cockpit script or this heating / cooling script, renaming the remaining ones as needed.

Trigger name | Button state variable | Button LED variable | Purpose
------------ | --------------------- | ------------------- | -------
`cp_heizregler_verteilung_drag` | `cockpit_heizregler_verteilung` | - | Air flow dispensation mode selector for the driver's A/C
`cp_heizregler_temp_drag` | `cockpit_heizregler_temp` | - | Temperature selector for the driver's A/C
`cp_heizregler_geblaese_drag` | `cockpit_heizregler_geblaese` | - | Fan speed selector for the driver's A/C
`taster_fahrerklima` | `cp_taster_fahrerklima` | `cp_taster_fahrerklima_led` | Driver's A/C controller
`taster_frischumluft` | `cp_taster_frischumluft` | `cp_taster_frischumluft_led` | Air circulation mode controller for the driver's and passengers' A/C's
`taster_klima` | `cp_taster_klima` | `cp_taster_klima_led` | Passengers' A/C controller
`taster_heat_or_frost` | `cp_taster_heat_or_frost` | `cp_taster_heat_or_frost_led` | Humidity management controller
`taster_zusatzheizung` | `cp_taster_zusatzheizung` | `cp_taster_zusatzheizung_led` | Auxiliary heating controller
`cp_heizluefter_toggle` | `cp_heizluefter_sw` | - | Cabin heaters controller

You might run into issues if some of your targeted vehicle's scripts *are themselves* dependent on `heizung.osc` variables, because several standard variables have, for reasons of consistency, been renamed herein. Fortunately, as far as the add-on is concerned, this has thus far not been the case. In such cases there is no real "quick-and-dirty" workaround, apart from replacing all affected variables everywhere with their proper counterparts.

### Sound integration

For the sounds to become audible, you will have to couple the [sound hooks](./manual.md#variables-of-potential-interest) with the desired sound files, via the vehicle's sound configuration file.

Below you will find a (simplistic) *sample* `sound_NL263_15.cfg` excerpt (i.e., it targets the *15-meter* version of the bus *specifically*). To use it, you need to copy the following files to `<OMSI>\Vehicles\MAN_NL_NG_263\Sound\misc\Klima`:
* `AC.wav`, `aircon.wav`, included in [Morphi](http://www.omnibussimulator.de/forum/index.php?page=User&userID=531)'s [Citaro](http://www.omnibussimulator.de/forum/index.php?page=Thread&threadID=27848) mod pack (version 4.3 referenced)
* `<OMSI>\Vehicles\MAN_NL_NG_263\Sound\misc\Umluftheizung.wav`
* `<OMSI>\Vehicles\MAN_NL_NG\Sound\klimator_100.wav`

Find and replace the section spanning from `Umluftheizung[...]` up to the end of the file with the following:
    
    ######################################
    cabin heaters
    
    [sound]
    misc\Umluftheizung.wav
    1
    
    [3d]
    -0.54
    -1.15
    1.2
    5
    
    [volcurve]
    cabin_heaters_sound_vol
    
    [pnt]
    0
    0
    
    [pnt]
    1
    1
    
    ######################################
    passengers' A/C - fan
    
    [sound]
    misc\Klima\aircon.wav
    0.4
    
    [viewpoint]
    2
    
    [3d]
    -0.005
    -2.1
    2.78125
    5
    
    [volcurve]
    passenger_ac_fan_sound_vol
    
    [pnt]
    0
    0
    
    [pnt]
    1
    1
    
    ######################################
    passengers' A/C - interior
    
    [sound]
    misc\Klima\AC.wav
    0.4
    
    [viewpoint]
    2
    
    [3d]
    -0.005
    -2.1
    2.78125
    5
    
    [volcurve]
    passenger_ac_int_sound_vol
    
    [pnt]
    0
    0
    
    [pnt]
    1
    1
    
    ######################################
    driver's A/C - fan
    
    [sound]
    misc\Klima\aircon.wav
    0.25
    
    [viewpoint]
    2
    
    [3d]
    -1.1
    5.4
    1.14
    2.5
    
    [volcurve]
    driver_ac_fan_sound_vol
    
    [pnt]
    0
    0
    
    [pnt]
    1
    1
    
    ######################################
    driver's A/C - interior
    
    [sound]
    misc\Klima\AC.wav
    0.27
    
    [viewpoint]
    2
    
    [3d]
    -1.1
    5.4
    1.14
    2.5
    
    [volcurve]
    driver_ac_int_sound_vol
    
    [pnt]
    0
    0
    
    [pnt]
    1
    1
    
    ######################################
    A/C - exterior
    
    [sound]
    misc\Klima\klimator_100.wav
    0.6
    
    [viewpoint]
    5
    
    [3d]
    0
    -8.21369
    0.7
    5
    
    [volcurve]
    ac_ext_sound_vol
    
    [pnt]
    0
    0
    
    [pnt]
    1
    1
    
    #############################################
    auxheat
    
    [sound]
    misc\Klima\Standheizung02.wav
    0.8
    
    [3d]
    -0.7
    0.4
    0.6
    3
    
    [volcurve]
    auxheat_sound_vol
    
    [pnt]
    0
    0
    
    [pnt]
    1
    1

### Window misting effect integration

This falls outside the scope of this discussion—I haven't even tried it myself yet, so this is purely theoretical—but, in a nutshell, one would have to:

1. Create the fog textures to be displayed on the windows' interior and exterior side (if you feel adventurous you could also experiment with frost textures, although some further scripting would be necessary in that case).
1. Bind the textures to the [misting hooks](./manual.md#variables-of-potential-interest) or any other variables being to some extent dependent on the former via the vehicle's model configuration file.
1. Modify the wiper script so that it handles fog on (the exterior side of) the windshield similarly to the way it handles precipitation; in other words, clearing windshield-accumulated mist with the wipers should be made possible.
