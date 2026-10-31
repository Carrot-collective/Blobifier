
We have found that cheap MG90/SG90 servo motors can die pretty fast from bed heat, therefore we developed the option to use a pancake stepper motor instead. 

This model uses a small GT2 belt as a toothed rack to move the blobifier's tray back and forth. 
To make room for a stepper motor, the blobifier assembly is risen by 2mm. This should not be an issue on most setups, but depending how your bottom panel sits, it might collide with the shaker arm. To solve this, a redesigned shaker arm is provided.

This mod is licensed under the same license of Blobifier, [GPLv3](https://www.gnu.org/licenses/gpl-3.0.en.html).

# BOM

To build this, you will need to have the standard components for a blobifier and a few more:

 * 2x M3 FHCS 6mm
 * 3x M3 BHCS or SHCS 8mm
 * Nema17 Stepper motor, pancake recommended
 * GT2 Timing belt, 37 teeth, open
 * GT2 20T Pulley with 5mm bore
 * An open stepper slot on your motherboard with your preferred driver

If you do not have the required components for a regular blobifier, to build a minimal setup (tray and bucket) you will also need:

 * 2x M3 T-nut
 * 3x M3 SHCS or BHCS 8mm
 * 3x M3 FHCS 8mm (or 6mm also works)
 * 3x M3 heat set inserts
 * 1x 10x15 aluminium strip (if you have the 'newer' 10x30, you can cut it easily)
 * 2x M2 10mm screws (any head)
 * 1x D2F-L microswitch



# Printed parts

You will need to print the following parts:
 * StepperHousing - This is the main blobifier body. It replaces both the base and the mount from the original project. 
 * Tray - the model is designed around the 10x15 old version. The tray has been modified to bolt down to the movement system using two flush screws.
 * BeltCarrier and Tensioner - we wrap the belt around this part. It translates rotational movement to linear. A screw hole allows the belt to be tensioned after installation. 
 * Arm - a taller (by 2mm) shaker arm is provided to avoid collisions.

# Software configuration

Create a new configuration file (for example `blobifier-stepper.cfg`), include it in your main `printer.cfg` file and put the following contents. Adjust pins and driver type as per your setup. 


```ini

# If your MCU is a repurposed SB2209, the following pins are used:
# step: PD0, dir: PD1, enable: !PD2, uart: PA15

# MOTOR6 port on BTT Octopus v1.0
[manual_stepper stepper_blobifier]
step_pin: PE2
dir_pin: PE3
enable_pin: !PD4
microsteps: 32
endstop_pin: tmc2209_stepper_blobifier:virtual_endstop
position_min: 0
position_max: 15
velocity: 200
accel: 9000

[tmc2209 manual_stepper stepper_blobifier]
uart_pin: PE1
run_current: 0.6
hold_current: 0.3
sense_resistor: 0.110
stealthchop_threshold: 99999999

[gcode_macro _BLOBIFIER_HOME]
description: Pull back to -15mm, then set that point as 0s
gcode:
  {% set speed = 200 %}
  MANUAL_STEPPER STEPPER=stepper_blobifier ENABLE=1
  MANUAL_STEPPER STEPPER=stepper_blobifier MOVE=-15 SPEED={speed} SYNC=1
  MANUAL_STEPPER STEPPER=stepper_blobifier SET_POSITION=0

# --- Fully extend to +15mm ---
[gcode_macro _BLOBIFIER_EXTEND]
description: Extend blobifier to +15mm (fully out). If disabled, home first.
gcode:
  {% if not printer.stepper_enable.steppers['manual_stepper stepper_blobifier'] %}
    _BLOBIFIER_HOME
  {% endif %}
  MANUAL_STEPPER STEPPER=stepper_blobifier MOVE=15 SYNC=1

# --- Retract to 0mm (inside) ---
[gcode_macro _BLOBIFIER_RETRACT]
description: Retract blobifier to 0mm (inside). If disabled, home first.
gcode:
  {% if not printer.stepper_enable.steppers['manual_stepper stepper_blobifier'] %}
    _BLOBIFIER_HOME
  {% endif %}
  MANUAL_STEPPER STEPPER=stepper_blobifier MOVE=0 SYNC=1
```

In the `mmu/addons/blobifier_hw.cfg` file, comment out the entire servo section: 

```ini
# [mmu_servo blobifier]
# Pin for the servo.
# pin: PD6
# Adjust this value until a 'BLOBIFIER_SERVO POS=out' extends the tray fully without a 
# buzzing sound
# minimum_pulse_width: 0.00053
# Adjust this value until a 'BLOBIFIER_SERVO POS=in' retracts the tray fully without a 
# buzzing sound
# maximum_pulse_width: 0.0023
# Leave this value at 180
# maximum_servo_angle: 180
```

In the `mmu/addons/blobifier.cfg` file, find the `BLOBIFIER_SERVO` macro and apply the following changes:

```diff
[gcode_macro BLOBIFIER_SERVO]
gcode:
  {% set bl = printer['gcode_macro BLOBIFIER'] %}
  {% set pos = params.POS %}
  {% if pos == "in" %}
-    SET_SERVO SERVO=blobifier ANGLE={bl.tray_angle_in}
+    _BLOBIFIER_RETRACT
    G4 P{bl.dwell_time}
  {% elif pos == "out" %}
-    SET_SERVO SERVO=blobifier ANGLE={bl.tray_angle_out}
+    _BLOBIFIER_EXTEND
    G4 P{bl.dwell_time}
  {% else %}
    {action_respond_info("BLOBIFIER: provide POS=[in|out]")}
  {% endif %}
-  SET_SERVO SERVO=blobifier WIDTH=0
```

The servo blobifier sits about 2mm higher than the standard blobifier, therefore you might need to increase your `variable_tray_top` by 2mm. 
It is also possible to decrease `variable_dwell_time` to 100 because the stepper is much faster than the standard servo motor. 

# Assembly

| Install the D2F-L Switch using two M2x10 screws. | Install the stepper using two M3x8 screws. | Slide in the pulley and tighten the grub screws using the lateral hole. |
| --- | --- | --- |
| ![01](Images/01.jpg) | ![02](Images/02.jpg) | ![03](Images/03.jpg) |

| Place the tensioner in the belt carrier and insert the belt in the tensioner side. Pull the end (so that the tensioner sits at the beginning of the track) and insert tightly on the other side. Screw a M3x8 in the provided hole in the carrier to ensure belt tension. | Place the carrier assembly inside the main part | Insert the tray and screw it in the carrier using two M3x6 FHCS | 
| --- | --- | --- |
| ![04](Images/04.jpg) | ![05](Images/05.jpg) | ![06](Images/06.jpg) |

### Final result

![07](Images/07.jpg)

