# Controls

## Hardware

For creating a gaming handheld device and avoid using an external controller, we need to add some buttons to the Raspberry Pi. Using internal pull-up/pull-down resistors, we can connect switch buttons directly to the GPIO pins of the Raspberry Pi. Check the Raspberry Pi Zero pinout [here](https://pinout.xyz/).

The tricky part is to add buttons in a way it fits the case you are using and it is comfortable to play. In this section we will cover a few options for adding buttons to the Raspberry Pi Zero. Some of the options are more complex and professional (custom PCBs) and others are more simple and DIY (protoboard).

### Buying a DIY Button PCB Board

One of the easiest and cheapest ways to add buttons to the project is to buy a DIY button PCB board for your specific case. They can be easily found on websites like AliExpress or eBay for the GBA/GB/GBC cases. This PCB will perfectly fit the case (screw holes, case holes, PCB thickness) and will be compatible with the original conductive silicone rubber pads that usually come with the case.

<img src="https://github.com/user-attachments/assets/e5af906a-b6c4-430f-872d-d825ebff61a0" width="300">

*DIY PCB bought online*

They are not very well documented. You will need to use a multimeter to check which pins are conected to each button and ground. You can directly solder each button to the Raspberry Pi GPIO pins and ground to any GND pin. Sometimes they will include a footprint for a resistor and a smd LED (power LED) that you can omit. This option will give you the most professional look and feel while keeping the cost low.

### DIY Protoboard

If you have some protoboards laying around and you are confident about your soldering/cutting skills, you can create your own DIY button board out of it. This options gives you the flexibility of adding more buttons, changing the layout or using different kind of buttons apart from the conductive silicone rubber pads.

You can use the original conductive silicone rubber pads by creating traces using conductive paint, copper tape or applying solder to the protoboard. In replacement of the conductive silicone rubber pads, you can use push buttons, theses are harder to fit inside the case due to their size and height. Becareful about the protoboard thickness, it might not fit the case.

This method requires more time and effort. It is recommended for people that are looking for the ultimate DIY experience. Please, make sure you have the right tools and knowledge before using cutting tools, soldering irons and other dangerous tools.

### Custom PCB

If you are looking for a professional look and feel, you can design your own custom PCB to fit not only the control but also all the other components of the project (more covered in the custom pcb section). This method can be expensive due to manufacturing, shipping and customs inspection costs (depending on your country). If you are already familiar with PCB design, there is not much to say here. This project PCB design was done using KiCad 8.0.

If you are planning to use push or smd buttons, you will need to look for the footprint or design it using the datasheet of the button. Creating the footprint for the conductive silicone rubber pads is easy. I recommend using a photo at 1:1 of the original gaming console PCB for reference. In this project particular case, the buttons footprint was designed to be compatible with the original conductive silicone rubber pads and also some specific smd buttons.

<img src="https://github.com/user-attachments/assets/9d15a302-039e-4fc9-bd86-63e568c47346" width="300">

*Custom PCB version 0.1 made for the GBA based handheld (front)*

<img src="https://github.com/user-attachments/assets/8a79af05-5d2e-4e7f-a146-ad210824873d" width="300">

*Custom PCB version 0.1 made for the GBA based handheld (back)*

### Different Button Types talk

Choosing the right button type is a very personal decision. Some people stick to the silicone rubber feel (GBA/GB/GBC) while others prefer a more mechanical/clikey feel (GBA sp). After using many different form factor button, here are my thoughts and recommendations:

- **Conductive Silicone Rubber Pads**: They are heavily dependent on the preassure you apply between the board and the shell, it might affect the button feel and response. If you have detection issues you can try sticking small cutouts of copper tape to the pads to increase thickness and conductivity. They will fit perfectly inside its case and plastic buttons, they are silent and cheap.
- **Push Buttons**: They have a more consistent feel and response. Becareful with the operating force, you might feel uncomfortable playing games that requires fast button presses. They are really difficult to fit inside the case due to their size and can be noisy. The case plastic buttons might not fit the push buttons, you will need to 3D print custom ones or modify the existing ones (cutting, sanding, adding silicone glue).
- **SMD Buttons**: They will likely require a custom PCB. Due to its size, you can still use the silicone rubber pads to fit the plastic buttons while using the smd buttons underneath. I heavily recommend looking for really low operating force buttons (like the ones used in the GBA SP) to avoid fatigue while playing.

<img src="https://github.com/user-attachments/assets/213c4942-677c-4b7d-b511-011295cd0456" width="300">

*Different buttons that are viable*

<img src="https://github.com/user-attachments/assets/2bc83a04-7d2e-4f9c-91cd-4a0492db0c8a" width="300">

*Rubber silicone pads*

### [Tutorial] Connecting a Button to the Raspberry Pi

In this tutorial we will connect a simple push button to the Raspberry Pi GPIO pins. This is the most basic way to add buttons to the Raspberry Pi. You can use this tutorial as a base for more complex button configurations.

Button will have two ends, one will be connected to the GPIO pin and the other to the GND pin. When the button is pressed, the GPIO pin will be connected to the GND pin and the Raspberry Pi will detect it as a button press.

If you want to check if the button was correctly connected, you can use `gpiozero` python library. In [its documentation](https://gpiozero.readthedocs.io/en/stable/recipes.html#button) you can find a lot of examples with images and code.


## Software

Buttons can be configured using the `gpiozero` python library. This library provides a simple interface for controlling GPIO devices connected to a Raspberry Pi. This allows creating custom buttons shortcuts for specific actions like system shutdown, etc.

In this particular project, since we are using RetroPie, we will use a Python based GPIO Controller called GPIOnext. It provides a CLI for configuring a gamepad using GPIO pins. You can find more information about it in the [GPIOnext official repository](https://github.com/mholgatem/GPIOnext). This is the easiest way to configure a gamepad in RetroPie using GPIO pins.
