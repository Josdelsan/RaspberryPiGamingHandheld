# Display

## Hardware options

There are many display options available for RPI. In this project it was chosen to use an SPI TFT LCD display that uses the ILI9341 driver (not ILI9341V). When using a GameBoy Advance shell, both 2.8 and 3.2 inches will fit. The 3.2 inch may require some modifications to the plastic cover that hold the leds under the screen and aditional software configuration in order to adjust screen borders. These screen usually works at a resolution of 320x240 pixels. They are sold in different formats: presoldered to a pcb, 18 pins connector or 40 pins connector.

<img src="https://github.com/user-attachments/assets/1299418f-d67e-4300-9adf-db7a47c776b6" width="300">

*ILI9341 displays (red pcb and 18p) and ILI9341V display (black pcb)*

### ILI9341 display presoldered to a pcb

When searching for ILI9341 display online, it is common to find these displays already soldered to a breakout board. This option is the most soldering friendly and easiest to debug but it may not fit inside the desired shell. When looking for a good fitting display, make sure the display is as centered as possible (display ribbon cable in the opposite side of the pins).

<img src="https://github.com/user-attachments/assets/46185253-528c-46ac-ba5e-e39df1e407d8" width="300">

*Displays that are sold with a breakout board*

For this project, touch screen is not used. When buying a display, look for displays that do not have exposed touch screen pins, this will save you space (same for sd card slots). You could ultimately remove the pins if you are not going to use them and/or modify the pcb using a cutting tool. Be extremely careful when modifying any pcb since it is very easy to damage the display and make it unusable (**NOT RECOMMENDED**).

When using theses displays, make sure to check the voltage required to power. They usually work at 3.3V or 5V. They are packed with different pins configurations, it is important to check the pins for later driver software configuration. Some manufacturers will replace SDI (MISO) and SDO (MOSI) for a single SDA that works as SDI. For this purpose, only SDI is used.

### ILI9341 18 pins connector display

ILI9341 with 18 pins ribbon connector are usually cheaper than the presoldered displays and easier to fit inside the shell. The downside is that they require precise soldering skills and some kind of breakout board in order to connect it to the RPI. A custom pcb is presented in the custom pcb section for this purpose.

<img src="https://github.com/user-attachments/assets/9e17e4c7-dac8-49e8-b883-a650cb8dbc49" width="300">

*18 pin connector ILI9341 display*

The 18 pins connector are configured as follows (make sure to check the datasheet of the display you are using):
- GND
- RESET
- SCL/CLK (Clock)
- DC (Data control)
- CS (Chip select)
- SDI/SDA (MISO) (Standard input)
- SDO (MOSI) (Standard output)
- GND
- VCC (3.3V)
- LEDA (Backlight LED Anode)
- LEDK1 (Backlight LED Cathode)
- LEDK2 (Backlight LED Cathode)
- LEDK3 (Backlight LED Cathode)
- LEDK4 (Backlight LED Cathode)
- XL (Touch screen)
- YU (Touch screen)
- XR (Touch screen)
- YD (Touch screen)

You will need to connect a resistor between 3.3v and LEDA in order to power the backlight. The value of the resistor will depend on how much brightness you want to achieve. I find 10R to be a good value for my needs. It could also be powered by a PWN pin in order to control the brightness, RPI0 has support for software PWM, It is not recommended for gaming sessions since it may cause flickering.


### ILI9341 40 pins connector display

ILI9341 with 40 pins connector are similar priced to the 18 pins connector and harder to work with. I have not tested any of these displays and I discourage their use against the 18 pins version for these kind of projects.

### Other displays

This project was heavily based on the [fbcp-ili9341 driver](https://github.com/juj/fbcp-ili9341), so it is recommended to use an 4-wire SPI display that is supported by this driver. If you use an alternative display in your project that requires additional information, feel free to contribute to this guide.

### [Tutorial] Connecting an SPI display

Raspberry Pi has two Serial Peripheral Interface (SPI) buses, SPI0 and SPI1. SPI1 is disabled by default, so we will be using SPI0 for the screen connection. SPI0 pins are 19, 21, 23, 24 and 26 (GPIO 7, 8, 9, 10 and 11). The display will be connected as follows:

- GND -> GND
- VCC -> 3.3V or 5V depending on the display
- CS -> GPIO 8 (SPI0 CE0)
- RESET -> GPIO 25
- DC -> GPIO 24
- CLK -> GPIO 11 (SPI0 SCLK)
- SDI -> GPIO 10 (SPI0 MOSI)
- SDO -> GPIO 9 (SPI0 MISO)
- LED -> 3.3V or 5V depending on the display

RESET and DC pins can be connected to any GPIO pin, it is important to remember the pin number for later software configuration. Some displays may have different pin names/configurations, make sure to check the datasheet of the display you are using. The essential pins for 4 wire SPI are CS, RESET, DC, CLK, SDI(SDA). Some breakout boards may not have LED/BLK pins since they use the VCC pin to power the backlight. You can check RPI pinout [here](https://pinout.xyz/pinout/spi).


## Software configuration for SPI displays

The software use for configuring the display may vary depending on the OS version. In this guide, we will cover two options `fbcp-ili9341` (up to Debian 10 buster) and `panel-mipi-dbi` (from Debian 11 bullseye). When installing Retropie (4.8) from Raspberry Pi imager, it will install Debian 10 (22/07/2024). It is recommended to use this version so the display is configured using `fbcp-ili9341`.

**DISCLAIMER** We will explain how to configure `panel-mipi-dbi` eventhough its performance is not good when running emulationstation because it forces software (CPU) rendering. Feel free to contribute to this guide if you find a solution.

### [Tutorial] Configuring fbcp-ili9341

You may need some dependencies in order to clone and build the driver like `cmake` and `git`. This tutorial will cover basic driver installation, check [driver GitHub repository](https://github.com/juj/fbcp-ili9341) for more detailed information.

1. Make sure you have a valid OS version (Debian 10 buster or older) in order to use `fbcp-ili9341`. You can check your OS version by running the command `lsb_release -a`.

2. Clone the repository to your desired location (I will use `/home/pi` for this guide):
```bash
git clone https://github.com/juj/fbcp-ili9341.git
```

3. Enter the repository folder and create a build folder:
```bash
cd fbcp-ili9341
sudo mkdir build
cd build
```

4. Run cmake to generate the makefile, this command is configured to use an ILI9341 display with RESET and DC pins connected to GPIO 25 and 24 respectively. display statistics are turned off and clock divisor is set to 6 (30 is a good starting point for debugging). Check fbcp-ili9341 repository for more information on how to configure the driver for other displays and additional options.
```bash
sudo cmake -DILI9341=ON -DGPIO_TFT_DATA_CONTROL=24 -DGPIO_TFT_RESET_PIN=25 -DSPI_BUS_CLOCK_DIVISOR=6 -DSTATISTICS=0 ..  
```

5. Build the driver:
```bash
sudo make -j
```


6. Modify the `/boot/config.txt` file to configure the display. Make sure the following lines are added or uncommented. Check `hdmi_cvt` values for your display resolution. The following configuration is for a 320x240 display.
```bash
hdmi_force_hotplug=1
disable_overscan=1
hdmi_cvt=320 240 60 5
hdmi_group=2
hdmi_mode=87
hdmi_drive=2
```

7. Comment the following lines in `/boot/config.txt`:
```bash
#dtparam=spi=on
```

8. Run the driver. There are two options, `systemd` service or `rc.local` script. Both options work fine, but system service is a bit faster and easier to debud using `systemctl` command.

- Configure `rc.local` script. Modify the file `/etc/rc.local` and add the following line before `exit 0`:
```bash
/home/pi/fbcp-ili9341/build/fbcp-ili9341 &
```

- Configure `systemd` service. Create a service (named `display.service` in this example) file in `/etc/systemd/system/` with the following content:
```bash
[Unit]
Description=Display driver
Before=basic.target
After=local-fs.target sysinit.target
DefaultDependencies=no

[Service]
Type=simple
ExecStart=sudo /home/pi/fbcp-ili9341/build/fbcp-ili9341 &

[Install]
WantedBy=basic.target
```

9. In case you are using `systemd` service, enable and start the service:
```bash
sudo systemctl enable display.service
sudo systemctl start display.service
```

10. Reboot the system and the display should be working. If you have any issues, check the driver output using `systemctl status display.service` (if using systemd).


### [Tutorial WIP] Configuring panel-mipi-dbi
panel-mipi-dbi driver documentation can be found [here](https://github.com/notro/panel-mipi-dbi/wiki). This driver is used in Debian 11 bullseye and newer versions. I tested this method with Debian desktop version and headless version, both worked fine but there is a performance issue when running emulationstation (seems like it forces software rendering). Feel free to contribute to this guide if you find a solution.

1. Add the following lines to `/boot/config.txt`. Make sure to change the resolution values to match your display resolution and the GPIO pins for RESET and DC pins.
```bash
dtoverlay=mipi-dbi-spi,speed=32000000
dtparam=compatible=ili9341\0panel-mipi-dbi-spi
dtparam=width=320,height=240,width-mm=57,height-mm=43
dtparam=reset-gpio=25,dc-gpio=24
```

2. Create a txt file for the display firmware commands. The following commands are for ILI9341 displays. Make sure to check the datasheet of the display you are using. Save the following content to a txt file:
```bash
command 0x28
command 0xcf 0x00 0xc1 0x30
command 0xed 0x64 0x03 0x12 0x81
command 0xe8 0x85 0x00 0x79
command 0xcb 0x39 0x2c 0x00 0x34 0x02
command 0xf7 0x20
command 0xea 0x00 0x00
command 0xc0 0x1d
command 0xc1 0x12
command 0xc5 0x33 0x3f
command 0xc7 0x92
command 0x3a 0x55
command 0xb1 0x00 0x12
command 0xb6 0x0a 0xa2
command 0x44 0x02
command 0xf2 0x00
command 0x26 0x01
command 0xe0 0x0f 0x2a 0x28 0x08 0x0e 0x08 0x54 0xa9 0x43 0x0a 0x0f 0x00 0x00 0x00 0x00
command 0xe1 0x00 0x15 0x17 0x07 0x11 0x06 0x2b 0x56 0x3c 0x05 0x10 0x0f 0x3f 0x3f 0x0f


command 0x2A 0x00 0x00 0x00 0xEF
command 0x2B 0x00 0x00 0x01 0x3F
command 0x2C

command 0x11
delay 100
command 0x21
command 0x29
delay 100
command 0x36 0x28
```

Commands for ILI9341 and ILI9341V are almost the same, it is only necessary to change display orientation (mirror image) and RGB order.
- [ILI9341 datasheet](https://cdn-shop.adafruit.com/datasheets/ILI9341.pdf)
- [ILI9341V datasheet](https://www.displayfuture.com/Display/datasheet/controller/ILI9341V.pdf)

3. Use the script provided by panel-mipi-dbi repository to create a firmware file. It can be found [here](https://github.com/notro/panel-mipi-dbi). Use the script and name the output file a `ili9341.bin`:
```bash
mipi-dbi-cmd ili9341.txt ili9341.bin
```

4. Copy the firmware file to `/lib/firmware/`. If you binary file is named differently, modify the `dtparam=compatible=ili9341\0panel-mipi-dbi-spi` to match `dtparam=compatible=yourfilename\0panel-mipi-dbi-spi`.
```bash
sudo cp ili9341.bin /lib/firmware/
```

5. Reboot the system and the display should be working.
