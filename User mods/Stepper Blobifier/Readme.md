# Experimental Stepper Based Blobifier

Servos tend to burn out in hot chambers. This version of the Blobifier uses a nema17 pancake stepper together with a rack and pinion system to translate the rotational stepper motion to a linear motion.

![image](https://github.com/user-attachments/assets/d8e42dc4-78ea-4e01-8c63-38f73f1d22d3)

The rear JST switch is used for homing the stepper and accurately setting tray position, with the front switch remaining as the bucket sensing switch.


## Options
Depending on how you want your wiring configured you can:
1. Single 8 pin JST connector in the housing, containing both the stepper and end stop signals
2. 4 pin JST for the stepper and 3 pin for the end stops
3. Single 3 pin for the end stops with the stepper wiring connected directly to the stepper from the main board.

## Bill of Materials (BOM)
1. 4x M2x10 self tapping screws
2. 1x M3x6 FHSCS screw
3. 2x M3x8 FHSCS screws. If your stepper has a shaft longer than 20mm you may need to also print the stepper spacer and replace these screws with 2x M3x10 FHSCS.
4. 1x 8 pin JST PCB header, or 1x 4 pin JST PCB header and 1x 3 pin JST PCB header, or 1x 3 pin JST PCB header
5. 1x 6 pin JST PH header for the stepper
6. AWG 26 wires

![image](https://github.com/user-attachments/assets/14258d97-f9e1-4cac-bfc9-167bec1dcf40)

## Assembly
Printed parts are not shrinkage compensated. Please ensure appropriate shrinkage compensation is set in the slicer! Parts with varying tolerances are provided (rack, pinions). Print all of them and test the ones that provide the optimal fit.

1. Insert the spacer in the stepper (optional - if stepper shaft is longer than 20mm)
2. Try the three pinions and use the one that can be inserted with a moderate amount of force and press until seated fully.
3. Glue in the aluminum plate to the tray and insert the tray in the main housing from the rear of the unit
4. Push the tray forward. Insert the rack from the front and secure with the M3x6 FHCS
5. Insert the stepper in the housing and secure with M3x8 FHCS / M3x10 FHCS if using the spacer
6. Place the two switches as shown in the pictures and secure with the M2x10 self tapping screws

![image](https://github.com/user-attachments/assets/75cc2d82-aae3-4c4d-bff5-8b43bad73334)
![image](https://github.com/user-attachments/assets/9efee17d-b1bf-42f1-9769-43394d8002d6)
![image](https://github.com/user-attachments/assets/98d8bc08-3ede-45cd-a29c-eeb1871969a7)


## Wiring
1. You can route the stepper wires to the body JST connector using the side slot or can directly connect to the stepper.
2. Cut to length and solder on the JST header
3. Connect the C wires from the end stop switches together and then to the JST header
4. Solder the **NO wire** from the rear homing end stop switch to the JST header. 
5. Solder the NC wire from the front bucket sensing switch to the JST header

## Software setup

Setup the stepper blobifier in the blobifier configuration as per the configuration instructions.

## Credits:
Credits go to **[TeamClockworks-RO108](https://github.com/TeamClockworks-RO108)** for the idea and work in demonstrating the concept!

Happy printing!
