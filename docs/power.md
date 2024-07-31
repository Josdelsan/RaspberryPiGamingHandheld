# Power

Powering the Rapsberry Pi Zero can be achieved using the micro USB port that comes with the board. For obvious reasons, this is not a viable option for a handheld gaming device. The most straightforward way to power the RPI0 is connecting a battery to one of the 5V pins and GND pins.

## Batteries

There are a few options for batteries, we will comment on the most common ones.

### 3.7V Lithium Battery

It is the most common choice when it comes to powering small electronics. They usually comes with a JST 2.0 PH 2-pin connector that makes it easy to connect/disconnect. The capacity of the battery will depend on the size of the device and the power consumption of the components. For this project, a 2000mAh battery should be enough to power the RPI0 and the display for a few hours.

I have tested a 103450 2000mAh battery and it has been working perfectly when it comes to battery life. A bit of case modding is required to fit the battery inside the GBA shell.

<img src="https://github.com/user-attachments/assets/73ae319a-53df-4c20-bce3-c1011750b23e" width="300">

*3.7V Battery*

### AA Batteries

AA or other form factor batteries can be a nice choice if the case has already a battery compartment. You will have to check whether they are connected in series or parallel and the voltage they provide in order to correctly regulate it to 5V.

I find this method a bit more complicated and less convenient (specially non-rechargeable batteries). It is a good choice if you are looking for a more nostalgic experience.

## Power modules

In order to power the RPI0, we need a module responsible for regulating the voltage and safely charging the battery. All the information provided here is based on testing with 3.7V lithium batteries.

**IMPORTANT**, make sure you know what you are doing when manipulating batteries. They can be dangerous if not handled correctly. Take this information as a guide and not as a step-by-step tutorial. Always double check the information provided here with other sources.

### MH-CD42

The MH-CD42 is a small all-in-one module that provides a 5V output and a charging circuit for lithium batteries. It is very easy to use and it is perfect for this project. It can be found on AliExpress for a few dollars.

It includes lithium battery protection, overcurrent protection, overvoltage protection, short circuit protection, over temperature protection and 4 LEDs for battery level indication. It also has a KEY pin/button that can be used to turn on/off the output.

The output is 5V but it lacks of USB port. You will have to buy an USB (C or micro) connector and solder it to the output pins.

<img src="https://github.com/user-attachments/assets/54451c11-3d36-4ec2-b232-dff031d075f7" width="300">

*MH-CD42 power module*

### TP4056

The TP4056 is similar to the MH-CD42 but it lacks the voltage regulation so you will have to use a step-up converter to get the 5V output. It has an integrated USB port (USB spec depends on the model). If you are buying the USB C version, make sure it is configured to work with USB C to USB C cables (resistor connected to CC pin).

I have seen many different versions of this module even if they are all called TP4056. Make sure to check the datasheet of the module you are buying.

<img src="https://github.com/user-attachments/assets/1692f73b-b5d8-41a6-be13-d2fd084af065" width="300">

*TP4056 power module*

## Power button

Adding a power button to the project is necessary if we want to turn on/off the device without having to disconnect the battery. It can be as simple as a switch that connects the battery to the power module or as complex as a button that sends a signal to a microcontroller than safely turns off the Raspberry Pi. There are a few ideas for adding a power button to the project:

### Simple switch

Using a simple switch to connect the battery to the power module is the easiest way to add a power button. Make sure the switch can handle the current draw of the RPI otherwise it will be stuck in a boot loop.

It is also a nice choice when using a console shell since you can use the original power switch and achieve a more professional look. For the GBA shell, you can find an original power switch that fits the case online.

This method will not allow the Raspberry Pi to safely shutdown. This might end up corrupting the SD card if the device is turned off while writing data (It has never happened to me but it is a possibility). It is recommended to creating a button shortcut/gpio-shutdown dtoverlay that triggers a safe shutdown before turning off the power or use emulationstation shutdown option.

The script for safely shutting down pressing multiple buttons can be done using Python and `gpiozero` library. Simple example:

```python
# Shutdown rpi zero
import time
import os
from gpiozero import Button

def shutdown(channel=None):
    os.system("sudo shutdown -h now")

b1 = Button(7)
b2 = Button(13)
b3 = Button(12)

while True:
    if b1.is_pressed and b2.is_pressed and b3.is_pressed:
        shutdown()
    time.sleep(0.5)
```

### Using MH-CD42 KEY pin

The MH-CD42 module exposes a button pin that can be used to turn on/off the output. Connect one push button terminal to the KEY pin and the other to GND. A single press will turn on the output and a long press will turn off the output.

This method is a nice way to control the power but it lacks the ability to prevent the device turning on when the battery is being charged. Additionally, if you still have room for push buttons next to the power button, you can add a safe shutdown button using the same method as the previous example or configuring a dtoverlay in the `/boot/config.txt` file. More information about gpio-shutdown can be found [here](https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README#L1643).


### Using a microcontroller

For a more professional finish, a microcontroller can be used to read the power switch state and safely shutdown the Raspberry Pi before cutting the power. Using dtoverlays, a GPIO pin can be configured to be set to low when the Raspberry Pi is shutting down. This pin can be connected to the microcontroller to indicate the shutdown state and cut the power when the Raspberry Pi is off.

This option may be overkill given the previous options but it is a nice way to learn about microcontrollers and how to communicate with the Raspberry Pi. Becareful about microcontroller power consumption and size, it might not fit inside the case.


## USB ports

Adding an USB charging port to the project is the most convenient way to charge the battery. It is up to you to decide whether you want to use a micro USB, USB C, mini USB or any other form factor. Make sure to read the datasheet of socket you are buying in order to correctly connect the pins.

If you are using an USB C port, take into account that some of presoldered USB C ports to PCBs lacks the CC pin resistors so you cannot use USB C to USB C cables. If you are gonna buy a USB C port and solder it yourself, I recommend buying the 6-pin version for easier soldering and adding the resistors yourself (5.1k for CC1 and 5.1k for CC2, both to GND).
