![render of the power distribution PCB](https://github.com/Haxintosh/tiled-pcb-heater/blob/958d70e85ded5e5bcc465d8803a0e40bc7d69e6a/pics/power_distribution_render.png)
# Tiled 3D printing bed
This project is a scalable, Klipper-compatible heated bed system designed for efficiency and flexibility. The bed is built from PCB-based heater tiles (≈100W each) that can be individually powered, allowing selective heating to reduce energy use. A central hub board, driven by an STM32 microcontroller, controls up to multiple channels with individual MOSFET switching and thermistor sensing.  

The project consists of 2 types of PCBs, the tiles and the power distribution boards.  

Currently there is a 150mm tile and a 8 channel power distribution board.  

## Specs
The calculator can found here: https://www.desmos.com/calculator/dyaic1mb9s  

Per tile:
| Measurements 	| Value         	|
|--------------	|---------------	|
| Dimensions   	| 150mm x 150mm 	|
| Power			| ~110W @ 24v   	|
| Max Temp		| ~105°C*			|

> [!WARNING]
> The max operating temperature depends on the rated temperature of the connector. The thermal fuse blows at around 125°C to prevent thermal runaway, but the current chosen connector (MicroFit 3) is only rated to 105°C. 
