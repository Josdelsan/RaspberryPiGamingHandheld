# Custom PCB

This section will cover some of the design decisions made for the custom GBA like PCB that is shared in this repository. This was my first PCB design ever, I decided to got for a custom PCB because when I built the first prototype I got pretty tired of dealing with a bunch of wires and soldering. I wanted to have a more professional finish and an easier way to fit the screen, buttons and other components. The PCB does not use any components apart from the modules (audio, power), Raspberry Pi Zero, the screen, USB port and power switch (resistor and power LED are optional). The goal was to simplify the wiring process and keeping the PCB cost as low as possible.

## Using the shared PCB

The PCB shared in this repository is the second version of the original PCB that I first designed. I corrected some design flaws related to the component placement that made the display fit so tight that causes some light inconsistencies in the screen.

If you are going to use this PCB, edit it or create your own, I recommend be careful with the PCB thickness. 1.6mm is commonly the cheapest option but it does not fit the GBA case for a few millimeters. I had to sand the shell a little bit to be able to close it. It really depends on the mechanical strength you need for your case.

I have not tested the PiBA v0.2 version but It should work exactly the same as the v0.1. I will update this section when I test it. Take into account that I am not a PCB designer, this design can be heavily improved.

## Board components

The following list contains the components used in the PCB, some of them are optional.

- **Raspberry Pi Zero W (W 2)**: It is mean to be soldered in the back of the PCB with the CPU facing to the back. You could flip the footprint if you want to solder some female 2-20P pin peader socket connector and use a RPI0 that came with the pin header pre-soldered (be careful with the height of the pin header in that case).
- **MH-CD42**: Power module that provides 5V output and charging circuit for lithium batteries.
- **6pin USB C port**: USB C port for power input.
- **R2 and R3**: Optional 5k1 resistor connected to USB C cc line for USB C to USB C cables.
- **JST 2.0 PH 2-pin connector**: Battery connector, any other connector could be used if needed.
- **Power switch**: GBA power switch.
- **ILI9341**: 2.8" or 3.2" TFT display. The 18pin version is meant to be soldered to the spare PCB part that can be cutted off. Any other display can be soldered to the 9 pins.
- **MAX98357**: Optional audio amplifier.
- **5 pin potentiometer**: Optional volume control.
- **Speaker**: Optional speaker.
- **Power LED**: Optional power LED.
- **R4**: Resistor for power LED.
- **RB, RL and AUX buttons**: Momentary tactile tact push buttons 4-Pin right angle (6x6mm, height does not matter but more than 5mm is recommended). The AUX button is optional, it takes the place of the original GBA headphone jack and it is meant to be used as a hotkey button.


## Adjust the PCB layout in KiCad to fit any case

The PCB cutout is designed to fit the GBA case but it can be easily adjusted to any other case. If you want to adapt to any other case, I recommend you find a 3D model of the case or a nice original case pcb scan. You can use an 1:1 image in KiCad to adjust the PCB layout to the case.