---
Title: "Modular 3D printing bed"
Author: "Geenk"
Description: "A modular and scalable 3D printing heated bed system."
Created On: "7/8/2025"
---

# 2025-08-07 Log 1: Initial planning & research
Time: 30min
Started the project and laid out the project's main criteria. The project is a Klipper compatible scalable and modular heated bed system. The tile nature of the beds allows the printer to selectively heat areas to reduce power consumption. The heated beds are made of individual tiles of PCB-based resistive heaters, which are connected to a power distribution board/hub. The hub, in turn, is connected to Klipper via UART over USB and is able to control 8 tiles of around 100W each. Each tile is connected through a connector for power and another one for thermistor (maybe add a connector on the tile for a thermistor to allow for a single cable from tile to hub? interference). To calculate the length and the width of the trace required for a given wattage, I've found and used the trace resistance formula and put it in a Desmos calculator (https://www.desmos.com/calculator/dyaic1mb9s). 
![desmos image of trace width and height calculations](https://github.com/Haxintosh/tiled-pcb-heater/blob/db1faf27f93f53c38962efba4f0ba4f78a835df6/pics/desmos.png)

# 2025-08-07 Log 2: Heated bed schematic
Time: 30min
Finished the schematic of the heated bed. I've decided to add in a thermal fuse in series with the 24V input to prevent the heater to go into thermal runaway. I have also added a LED to indicate when the plate is being powered. It was hard to figure out a way to connect a single trace to 24V and GND without screwing up the nets  of the PCB. I've tried tying 24V and GND but that resulted in everything being named GND and I also tried making the trace resistor into a component, but that didn't work since I needed to modify its' traces for things like screw holes. I finally decided on using solder jumpers to isolate the heater traces. For the connector, I've settled on a Molex MicroFit 3 since some of the connectors are rated up to 125C.  
![schematic of the 150mm heated bed](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/150mm_heater_schem.png)

# 2025-08-08 Log 3: Heated bed PCB
Time: 1.5h
Laid out the Hilbert curve heater trace using the KiCad space filling curve plugin, also finished most of the layout of the PCB (LED, connector, fuse). All components and traces are single sided since I plan on going with the aluminum PCB at JLCPCB.

# 2025-08-08 Log 4: Finalize Heated bed PCB
Time: 1h
Finalized the heated bed PCB. I added M2.5 holes for mounting and had to move the Hilbert curve traces for hole and countersink clearances. Ran DRC and fixed many errors related to the Hilbert curve since I generated it a bit too big. I added also some silkscreen texts.
![PCB image of the heated bed PCB](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/150mm_heater_pcb.png)
![rendered image of the heated bed PCB](https://github.com/Haxintosh/tiled-pcb-heater/blob/main/pics/150mm_heater.png)
# 2025-08-11 Log 5: Power distribution schematic (MCU + MOSFET drivers)
Time: 2h
Started the schematic for the power hub board. The board is made up of 4 main sections, the MCU, STM32F030K, was chosen due to its analog input multiplexing capabilities and its ease of integration with Klipper, the MOSFET switching, made up of a gate driver and a N channel MOSFET capable of handling up to 5A of current with safety margins, the thermistor sensing section, and finally the voltage rails. I've finished most of the MCU section, with the UART to USB converter, the crystal oscillators, and the pinout of the MCU. I've also done the MOSFETs, where each one is controller by a driver IC and there's 8 channels of switching. It was my first time working with STM32 chips but it was easier than I thought. 
![schematic of the STM32 MCU](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/mcu_schem.png)
![schematic of the MOSFET and the gate driver](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/mosfet_schem.png)
# 2025-08-12 Log 6: Power distribution schematic (thermistor sensing, power rails, and a bit of clean up)
Time: 2h
Finished up the schematic for the power distribution board. I've added in the thermistor sensing section and the voltage rails needed to power the board. Also added some protection in the form of a polyfuse on the USB rails and one fuse on each of the 24V input rail just in case. The thermistor sensing as derived from the BTT SKR mini E3 since it's an established design that  probably will work and I don't wanna spend hours debugging an analog circuit. The power rails are divided up into 24V for the heaters, 12V for gate driver power and allows the MOSFET to fully open, 5V for peripherals and powers the 3V3 rails. The 3V3 rails are divided up into analog 3V3 and digital 3V3 to minimize noise in the thermistor readings. 
![schematic of the switching regulator for the 5V and 12V](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/sw_schem.png)
![schematic of the LDOs and led indicator for power](https://github.com/Haxintosh/tiled-pcb-heater/blob/main/pics/ldo_schem.png)
![schematic of the thermistor sensing section](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/therm_schem.png)

# 2025-08-16 Log 7: MOSFET channel placement
Time: 3.5h
Started on the PCB layout for the power distribution board. The idea was to have 4 outputs on the left and right side of the PCB with a power input screw terminal in the middle of the 4 outputs. I thought I could save time by using the multichannel placement tools recently added to KiCad, but I didn't know how to use it at first for sheets without hierarchical sheets. But after 30min of experimentation, I figured it out and annotated the schematic with rule areas and directive labels. Then, I routed laid out a MOSFET switching channel, making sure there's plenty of thermal mass in the copper planes surrounding the MOSFET to take away the heat for thermal management. Then, after finishing a single channel, I've replicated the single channel design to 3 other channels for the left side. I've then moved them around a bit to fit the power connector in the middle. Then I had to extend some of the copper planes to connect the other channels. I then did the same for the right side. 
![image of a single MOSFET layout](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/mosfet.png)
![image of the complete MOSFET layout for the left](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/mosfet_col.png)


# 2025-08-17 Log 8: USB, protection and UART-USB
Time: 1h
Laid out the PCB for the USB C connector and the ESD protection circuitry, Also routed the polyfuse. The layout for the UART to USB was also done. I included a connector and some 0 Ohm resistors in series with the UART bus for a direct Klipper UART connection option.
![image of the whole PCB with the MOSFET layout and USB layout on top](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/mosfet_usb.png)

# 2025-08-18 Log 9: Power regulation
Time: 1.5h
Laid out the traces for the voltage regulators. The 5V and 12V rails are converters from 24V using switching regulators. I had to plan things out with the MCU to not interfere with the analog traces especially given the high noises originating from tshe switching regulators. The feedback resistors were placed far away from the switching nodes and most of the layout followed the recommended layout by TI. 

# 2025-08-18 Log 10: STM32
Time: 1.5h
Placed the MCU in the middle of the board, with the sensitive analog pins facing towards to bottom of the board and away from the switching regulators. Then the decoupling capacitors were placed along side the crystal oscillator. I then placed the 2 LDOs regulating the 3V3 rails quite close to the STM32 since there weren't many places left for them. I then connected the PWM control signals for the MOSFETs. I originally thought that the PWM traces from the MCU will cause a good amount of noise, but apparently Klipper uses a 10Hz signal to control the heaters. 
![image of the whole PCB with the MCU section done](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/mcu_pcb.png)

# 2025-08-20 Log 11: Thermistor & DRC
Time: 2h 
Routed the thermistor sensing section on the PCB and replicated the layout for the other 7 channels. Then routed from the MCU to the channels. I've also placed the SWD connector for debugging and for flashing just in case the USB flashing fails. I've also added a test hook for oscilloscope ground for probing purpose. I've also added some M2 screw mount holes on the 4 corners of the PCB and silkscreen labels for the different inputs and LEDs. I finished by rounding out the corners of the PCB and ran & fixed DRC.
![image of the themistor sensing layout](https://github.com/Haxintosh/tiled-pcb-heater/blob/98d143a2d95fdf668801079218101a170dff6366/pics/therm_pcb.png)
![image of the finished PCB](https://github.com/Haxintosh/tiled-pcb-heater/blob/cabced6e9d95e027c0f9c7038fd8d20f2d08e795/pics/pcb_view.png)

# 2025-08-21 Log 12: UART issue
Time: 30min
Found out that the for STM32 to flash over USB using a USB-UART converter, the pins PA9/PA10 must be used for UART. I was using a different set of pins, meaning that I would not be able to flash the STM32 via USB. I switched and rerouted the HEATER1-2 pins with the UART RX/TX pins. 
![image of the fixed PCB](https://github.com/Haxintosh/tiled-pcb-heater/blob/cabced6e9d95e027c0f9c7038fd8d20f2d08e795/pics/FIXED_view.png)
![image of a 3D view of the PCB](https://github.com/Haxintosh/tiled-pcb-heater/blob/cabced6e9d95e027c0f9c7038fd8d20f2d08e795/pics/FIXED_3d.png)
