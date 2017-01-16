#### Chapter 3
## Functionality in detail
***
This chapter provides details on the functionality offered by *UCHill*. It should be noted that, while effort has been put into making the discussion of each topic reasonably comprehensive, the main script (`<UCHill>\uchill.osc`) remains the sole definitive source of information at all times. Formulated otherwise, this chapter is not intended to cover every detail and corner case there is, simply because, if this were the case, the result would be harder to decipher than the script itself.

Terminology note: The recurring phrase "(when) in a cooling / heating / dehumidification / humidification context..." is to be interpreted as "in a situation where a cooling / heating / dehumidification / humidification effect is desired (more than anything else, i.e., being the user's first priority), then...".

#### 3.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Temperature- and humidity-affecting functions

A *temperature* and/or *humidity function* (or simply a *function*, for the remainder of this section) is any simulated functionality pertaining to the vehicle or the environment that affects the former's temperature and/or humidity in some manner. This section focuses on the most important ones among the functions, stating their respective purpose, discussing some of the factors affecting them, and providing practical examples and guidelines along the way.

#### 3.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Active functions

*Active functions* are user-controlled, i.e., functions which the user (virtual driver) can influence. This section will specifically center around the functions of this kind that are to some extent controllable via the cockpit's cooling / heating panel's controls.

Active functions may have *attributes*—"sub-functions" (for lack of a better term, in the sense that they do not per se contribute heat or moisture) that modify the way the functions they are associated with operate. While on a technical level everything is of course interconnected at some degree, attributes' relationships with functions extend beyond that, being more "business contract-like", reaching closer to the physical realm. In more practical terms, attributes express (assumed) real-world, circuit-level couplings, rather than implicit or merely artificial script-level connections or dependencies. Functions and attributes have a *many-to-many* relationship. To confuse matters further, functions can themselves act as other functions' attributes.

A central function-related aspect that the script addresses extensively is the notion of *inertia*, which within this scope could be described as being the time required for a change in a function's state to become effective, or, perhaps better formulated, the time required for the function to bring about (observable) system-wide change. The reason why this is mentioned vaguely at this point is to serve as a subtle but general reminder to users who might be concerned that a function is flawed simply because it induces no *instant* noticeable temperature or humidity change—worry not, it may take from seconds up to several minutes, depending on the context, for that to happen.

#### 3.1.1.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Overview

The screenshot below summarizes the functions and attributes that are to be discussed in this section; the (regular expression) notation `[FA]\d(\:\s\{F\d(,\sF\d)*\})?` in the bordered boxes enclosing the buttons is to be interpreted as *"the attribute or function identified by this digit acts as an attribute of these functions"*.

![heating-panel](http://i.imgur.com/mJpNQrm.png)
<sub>*Cooling / heating controls and associated functions in a typical VDV dashboard-equipped vehicle. The actual panel and/or dashboard controller layout may vary. Screenshot credit: *MAN Stadtbusfamilie* add-on.</sub>

The next table provides a textual representation of the same information, also providing links to each active function's documentation section below (attributes or functions acting as attributes have no sections of their own but are instead covered in the sections of the functions *they act upon*).

Function or attribute | Name | Attribute of
--------------------- | ---- | ---------------
*A1*<sup>[1](#function_table_remark_1)</sup> | Air dispensation mode | *F1*
*A2* | Temperature | *F1*
*A3* | Fan speed | *F1*
*A4* | Air circulation mode | *F1*, *F2*
*F1* | [Driver's A/C](./driver_passenger_ac.md) | *F3*<sup>[2](#function_table_remark-2)</sup>
*F2*<sup>[2](#function_table_remark_2)</sup> | [Passengers' A/C](./driver_passenger_ac.md) | *F3*<sup>[2](#function_table_remark_2)</sup>
*F3* | [Humidity management](./humidity_management.md) | *F5*<sup>[3](#function_table_remark_3)</sup>
*F4* | [Auxiliary heating](./auxiliary_heating.md) | *F5*
*F5* | [Cabin heaters](./cabin_heaters.md) | *F1*, *F2*<sup>[2](#function_table_remark_2)</sup>, *F3*<sup>[3](#function_table_remark_3)</sup>

<sub><a name="function_table_remark_1">1</a>: Not implemented - does nothing.</sub><br/>
<sub><a name="function_table_remark_2">2</a>: Only in vehicles having a roof-mounted A/C unit.</sub><br/>
<sub><a name="function_table_remark_3">3</a>: Only in vehicles without a roof-mounted A/C unit.</sub>

#### 3.1.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Driver's and passengers' A/C

#### 3.1.1.3&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Humidity management

#### 3.1.1.4&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Auxiliary heating

#### 3.1.1.5&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Cabin heaters

#### 3.1.1.6&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Cabin heaters

*Passive functions* are functions that the user has no (direct) control over. While several such functions are implemented by the script, the sole one of them that is, due to being somewhat new to OMSI-land, worth taking a closer look at, is that of the [greenhouse effect](./greenhouse_effect.md) (abbreviated *GhE* herein).

#### 3.1.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Greenhouse effect

#### 3.2&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Other functions

This section links to secondary features that do not affect the vehicle's temperature or humidity.

#### 3.2.1&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Window misting effect
