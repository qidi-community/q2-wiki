# Randomizing Z Homing Probe Location

Repeated Z homing in the same spot causes low spots in bed surfaces. Randomize the probe location to distribute wear evenly.

## Configuration

Add this `homing_override` to your printer.cfg:

```ini
[homing_override]
axes:xyz
gcode: 
    {% set home_all = 'X' not in params and 'Y' not in params and 'Z' not in params %}              #
    {% set z_hop_speed = (printer.configfile.settings['stepper_z'].homing_speed * 60) | float %}    #
    {% set travel_speed = (printer.toolhead.max_velocity * 60) | float %}                           #
    {% set sensorless_variables = printer["gcode_macro _Sensorless_Homing_Variables"] %}            #
    {% set z_hop_distance = sensorless_variables.z_hop_distance | float %}                          # Collect all variables needed for sensorless homing
    {% set first_homed_axis = sensorless_variables.first_homed_axis | string %}                     # from machine config file and _Sensorless_Homing_Variables
    {% set safe_x = sensorless_variables.safe_x | float %}                                          #
    {% set safe_y = sensorless_variables.safe_y | float %}                                          #
    {% set safe_z = sensorless_variables.safe_z_enable | abs %}                                     #
    {% set chamber = printer["heater_generic chamber"].target|int %}
                                                                                     #

    {% if safe_x == -128 %}                                                                         #
        {% set safe_x = (printer.configfile.settings.stepper_x.position_max) /2 %}                  # If safe_x is '-128', set safe_x to the center of the X axis
    {% endif %}                                                                                     #

                                                                                    #

    {% if safe_y == -128 %}                                                                         # 
        {% set safe_y = (printer.configfile.settings.stepper_y.position_max) /2 %}                  # If safe_y is '-128', set safe_y to the center of the Y axis
    {% endif %}                                                                                     #
                                                                                  #

    {% if z_hop_distance > 0 %}                                                                     # Check if z_hop_distance is greater than zero
      {% if 'x' not in printer.toolhead.homed_axes and 'y' not in printer.toolhead.homed_axes %}    # If X and Y are not homed, move Z to z_hop_distance
        SET_KINEMATIC_POSITION Z=0
        G0 Z{z_hop_distance} F{z_hop_speed}                                                         #
      {% endif %}                                                                                   #
    {% endif %}                                                                                     #

    {% if first_homed_axis == 'X' %}                                                                # If first_homed_axis is 'X', begin G28 param check
      {% if home_all or 'X' in params %}                                                            #
        _HOME_X                                                                                     # If home_all or 'X' is in params, home X
      {% endif %}                                                                                   #
      {% if home_all or 'Y' in params %}                                                            # If home_all or 'Y' in params, home Y
        _HOME_Y                                                                                     #
      {% endif %}                                                                                   #
    {% endif %}                                                                                     #

    {% if first_homed_axis == 'Y' %}                                                                # If first_homed_axis is 'Y', begin G28 param check
      {% if home_all or 'Y' in params %}                                                            #
        _HOME_Y                                                                                     # if home_all or 'Y' is in params, home Y
      {% endif %}                                                                                   #
      {% if home_all or 'X' in params %}                                                            # If home_all or 'X' in params, home X
        _HOME_X                                                                                     #
      {% endif %}                                                                                   #
    {% endif %}                                                                                     #

    {% if safe_z == 1 and (('Z' in params) or home_all) and ('Z' not in printer.toolhead.homed_axes) %}  # If safe_z is enabled, Z is to be homed, and Z is not homed
        {% if (printer.toolhead.position.z|float < 10) %}
            SET_KINEMATIC_POSITION Z=0
            G1 Z{z_hop_distance} F{z_hop_speed}
        {% endif %}
        G0 X{safe_x} Y{safe_y} F{travel_speed}                                                     # Move to safe XY position at travel speed
    {% endif %}                                                                                     #
   
    {% if home_all or 'Z' in params %}
        G90
        M106 S255  #Turn on part cooling fan to help cooldown nozzle
        M109 S150  #Heat/cooldown nozzle to 150c, prevents any filament from sticking to bed
        M106 S0    #Turn off part cooling fan, maybe helps remove noise from load cell
        SET_HEATER_TEMPERATURE HEATER=chamber TARGET=0   #Turn off chamber heater, maybe helps reduce electrical noise from load cell
        SET_GCODE_OFFSET Z=0 MOVE=0
        {% set base_x  = printer['gcode_macro PRINTER_PARAM'].max_x_position/2 | float %}   #Randomize probe point
        {% set base_y  = printer['gcode_macro PRINTER_PARAM'].max_y_position/2 | float %}
        {% set safe_x  = range((base_x - 10)|int, (base_x + 10)|int + 1) | random %}
        {% set safe_y  = range((base_y - 10)|int, (base_y + 10)|int + 1) | random %}
        G0 X{safe_x} Y{safe_y} F7800
        G28 Z
        G1 Z20 F2000
        SET_HEATER_TEMPERATURE HEATER=chamber TARGET={chamber}  #Restore chamber heater
        
    {% endif %} 
```

And add this macro for sensorless homing variables:

```ini
[gcode_macro _Sensorless_Homing_Variables]
description: Variables for sensorless homing

variable_z_hop_distance: 10                # Distance to move Z axis prior to homing, and after homing.
variable_first_homed_axis: 'Y'              # First axis to home when 'G28' is called. Some prefer homing Y before X.

## The following variables are used for moving the printhead to a certain part of the bed before homing the Z axis:

variable_safe_z_enable: True               # Enables/disables moving the printhead before homing the Z axis.
variable_safe_x: -128                       # Safe X position to home the Z axis, leave at -128 to home to the center of the X axis.
variable_safe_y: -128                       # Safe Y position to home the Z axis, leave at -128 to home to the center of the Y axis.

# Do not modify below
gcode
```

## Installation

1. Add the `[homing_override]` section to your printer.cfg
2. Add the `[gcode_macro _Sensorless_Homing_Variables]` section to your printer.cfg
3. Restart Klipper for the changes to take effect

## How It Works

Randomizes probe position within ±10mm of bed center during Z homing.