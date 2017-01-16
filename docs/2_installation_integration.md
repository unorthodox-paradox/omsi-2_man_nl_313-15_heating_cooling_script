#### Chapter 2
## Installation / Integration
***
Using *UCHill* is a two-step process: First you must perform a one-time installation of the modification's core files. Subsequently you will have to integrate the modification with your individual vehicle(s) of choice.

#### 2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Installing *UCHill*

Extract the compressed archive's `Scripts` directory into your root OMSI 2 directory, choosing "Yes" if asked whether merging is desired; no existing files other than (potential remnants from a previous *UCHill* installation) the modification's own will be modified.

#### 2.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Integrating *UCHill*

#### 2.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Natively supported vehicles

As of version *1.1.0*, the *UCHill* distribution already contains the logic required for its integration with the vehicles enumerated below:

Vehicle (package) | Version (range) | Remarks
------------------|-----------------|--------
*MAN Stadtbusfamilie* add-on buses | &#60;any&#62; | 

If the list above includes the vehicle you intend to integrate *UCHill* with, all you have to do is open `<UCHill>\integrations\<name of target vehicle>\INSTALLATION_GUIDE.xhtml` with your favorite web browser and follow the instructions listed therein. Note that you will still have to modify or otherwise manipulate some of the vehicle's files, although the process will be more streamlined, compared to the alternative case. Plus, you will not have to write any code.

If the above list does not include your targeted vehicle, proceed to the next section.

#### 2.2.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Other vehicles

#### 2.2.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Authoring the integration adapter

To integrate *UCHill* with non-natively supported vehicles, you will have to author the integration logic yourself. Worry not, however, for this is generally a straightforward process. If you are the kind of person who wants to dive right into the code, have a look at the sample integration script at `<UCHill>\integrations\example\uchill_example_integration.osc`—otherwise read on.

To remain portable, i.e., independent from vehicle implementation details, the main *UCHill* script (`<UCHill>\uchill.osc`) abstains from reading and setting the value of any variable and constant, as well as from invoking any macro, except for OMSI "globals, i.e., system variables and macros, as well as those it *itself* has declared in its accompanying variable / constant list files or in its body. Still, in order to successfully perform its duties, it requires access to a few bits of information maintained externally, such as the engine's or a cockpit's controller's state. The solution to the problem of retrieving / setting external information without compromising portability lies in a thin "adapter layer"—an *SPI*-like construct, if you will—facilitating implementation-independent, bidirectional communication between *UCHill* and the remainder of the "host" vehicle's scripts. The adapter, being a separate script, comprises a handful of predefined macros and variables, which the integrator—*yourself*—is responsible to respectively implement and read and/or set the values of. The specifics of the adapter macros and variables are covered in the [reference](./manual.md#technical-documentation--reference).

Once you are done writing the adapter, save it as a new `.osc` file wherever you please and move on to the next section.

#### 2.2.2.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Copying vehicle-specific files

While not strictly necessary, it is for your own convenience recommended that, as a precaution, you make copies of the following files pertaining to the targeted vehicle:
* The vehicle's `.bus` file, directly under `<target vehicle>`.
* The vehicle's *main* script, usually residing at `<target vehicle>\<script dir>\<main script>`. If in doubt regarding the `<script dir>` and `<main script>` path name components, refer to the `.bus` file.
* The vehicle's sound configuration file, located at `<target vehicle>\<sound dir>\<sound file>`. If in doubt regarding the `<sound dir>` and `<sound file>` path name components, refer to the `.bus` file.

#### 2.2.2.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Modifying the `.bus` file

In your copy of the `.bus` file, perform the following steps:

1. Modify the second string following the `[friendlyname]` keyword to help you identify the modified version of the vehicle in-game later on.
1. In the `[varnamelist]` section, replace any entries pertaining to the vehicle's cooling / heating script(s) with:

        <UCHill>\uchill_vars.txt
        <OMSI>\Scripts\up-utils\up-utils_vars.txt
Furthermore, if your adapter script uses variables of its own, make sure to declare the relevant variable list file here as well. Finally, modify the integer value following the `[varnamelist]` keyword, so that it reflects the new number of entries within the section.
1. In the `[script]` section, replace any entries pertaining to the vehicle's cooling / heating script(s) with:

        <UCHill>\uchill\uchill.osc
        <path to your UCHill adapter script>
        <OMSI>\Scripts\up-utils\up-utils.osc
Finally, modify the integer value following the `[script]` keyword, so that it reflects the new number of entries within that section.
1. In the `[constfile]` section, replace any entries pertaining to the vehicle's cooling / heating script(s) with:

        <UCHill>\uchill_consts.txt
        <OMSI>\Scripts\up-utils\up-utils_consts.txt
Furthermore, if your adapter script uses constants of its own, make sure to declare the relevant constant list file here as well. Finally, modify the integer value following the `[constfile]` keyword, so that it reflects the new number of entries within the section.
1. Modify the path at the line below the `[sound]` keyword, so that it refers to your copy of the file (which you created earlier).

#### 2.2.2.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Modifying the main script file

In your copy of the main script file, replace the macro invocations of the preexistent cooling / heating script(s)—`(M.L.heizung_init)` and `(M.L.heizung_frame)`, by convention—with `(M.L.uchill_init)` and `(M.L.uchill_frame)`, respectively.

#### 2.2.2.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Integrating sounds with *UCHill*

For *UCHill* to use sounds, you will have to couple the [sound hooks](./manual.md#variables-of-potential-interest) with the desired sound files—which are *not* provided by this modification—via your copy of the sound configuration file. For examples on doing so, refer to the relevant sections of any natively supported vehicle's `INSTALLATION_GUIDE.xhtml` document.

#### 2.2.2.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Integrating the window misting effect

For the windows to actually fog in-game, one would have to, in a nutshell, carry out the steps listed below:
* Create the fog textures to be displayed on the windows' interior and exterior side (if you feel adventurous you could also experiment with frost textures, although some further scripting would be necessary in that case).
* Bind the textures to the [misting hooks](./manual.md#variables-of-potential-interest) or to any other variables being to any extent dependent on the former, via the vehicle's model configuration file.
* Modify the wiper script so that it handles fog on (the exterior side of) the windshield similarly to the way it already handles precipitation; in other words, clearing windshield-accumulated mist with the wipers should be made possible.

