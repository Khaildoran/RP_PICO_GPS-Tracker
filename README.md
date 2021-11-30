# piGPS - Microcontroller Application

This is another random Raspberry Pi project on the internet... 

This app is the microcontroller portion of a RP based GPS tracking system. The system will use the power efficient Pico rp2040 to detect movement using a GPS breakout board from U-blox. If movement is detected it will power on it's bigger sibling the RPZero, relay the coordinates, and await further instruction from that device. 


## Architecture 
This app is a [Finite State Machine (FSM)](https://en.wikipedia.org/wiki/Finite-state_machine). There are 3 primary states that the controller will attempt to move between continuously while looking for geographical movement. 

**'READGPS'** - In this state the Pico will connect to the u-blox GPS board and read in new GPS coordinates. It will build up a small array of these coordinates, and pass them on for further inspection

**'EVALCOORD'** - in this state the Pico will average out the coordinates (to account for inaccurate data from GNSS) and check the coordinates against it's previous coordinates looking for a distance of greater than ~200ft.
If movement is detected it will relay this information back to the controller. 

'**CONNECTPI'** - in this state the Pico will power on an RP zero running Raspbian and pass on the current state information which includes any data/coordinates gathered in previous states. This state also acts as a catch all for exceptions that weren't able to be handled by the pico controller. if a green light is passed back from the RP zero, the pico will resume.

Transition Table - using a set of conditions (the previous state, and error code returned from that state) the controller (FSM) will find the next task to run.

![State Transition Table](https://github.com/Khaildoran/piGPS_MicroController/blob/main/assets/StateTransitionTable.PNG?raw=true)

![Error Code Legend](https://github.com/Khaildoran/piGPS_MicroController/blob/main/assets/errCodeDesc.PNG?raw=true)
