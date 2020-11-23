# Marlin for Wanhao I3 (v2.1 as test unit)
This repo is a fork of the original Marlin firmware, edited for compatibility with the Wanhao I3 printer (tested on a v2.1) and based on the configuration file from [arvdsar repo](https://github.com/arvdsar/wanhao_i3_v2_Marlin_v2_config "arvdsar repository").
It enables multiple features offered by merlin, while fixing old I3 firmware problems (like thermistore failure detection).  
Multiple changes have been made from arvdsar file :
### Speaker is back
The speaker has been re-enabled and "beep" frequency and time fixed.  
_Enable_ : line 1820 of Configuration.h  
_Duration_ : 50 [ms], line 1829 of Configuration.h  
_Frequency_ : 100 [Hz], line 1830 of Configuration.h  
_Speaker definition_ : added speaker pin in pins_SANGUINOLOLU_11.h, line 269 (read further for #ifdef enclosure)
### SD card support
The support for the SD card has been enabled, with R/W support.  
_Enable_ : Config.h line 1732  
### 4k7 pullup with 100k thermistor
For my test unit, the thermistor has the 4k7 pullup for the thermistor, not the 10k one.  
_Nozzle_ : Configuration.h line 420 (set back to 99 if you have the old pullup)  
_Bed_ : Configuration.h line 428 (set back to 99 if you have the old pullup)  
### Advanced pause and nozzle park
Advanced pause and nozzle park have been enabled for runout sensor support and better print pause handling.  
_Advanced pause_ : Configuration_adv.h line 2091  
_Nozzle park_ : Configuration.h line 1528  
### PID for bed and nozzle
Enabled PID for bed heating, while modifying default values for both PID's.  
_Bed PID enable_ : Configuration.h line 527  
_New bed values_ : P - 104.88 / I - 20 / D - 366.59 || for 50 [°C]  
_New nozzle values_ : P - 19.38 / I - 0.97 / D - 96.43 || for 220 [°C]  
#### Get your own values ####
When starting the printer, ensure bed and nozzle are not heated and around room temperature.  
Then launch a serial terminal, connected with a baudrate of 250'000.  
Commands are :
* M303 C10 E-1 S60 U1 (for bed auto-PID, 10 iterations, around 60 [°C])  
* M303 C10 E0 S220 U1 (for extruder auto-PID, 10 iterations, around 220 [°C])  
Both will output infos, and in the end something like :  
> #define DEFAULT_Kp 19.38  
> #define DEFAULT_Ki 0.97  
> #define DEFAULT_Kd 96.43  

Those values are set automatically (with the U1), but you can set them with :  
* M304 Pxx.xx Ixx.xx Dxx.xx (for bed)  
* M301 Pxx.xx Ixx.xx Dxx.xx (for extruder)  
Finally, save them with :  
* M500
### Default presets values
Modified values for presets of PLA and ABS (Configuration.h lines 1507+).  
_You can modify them manually on the printer menu._  
_PLA_ : 200 [°C] (extruder) / 50 [°C] (bed)  
_ABS_ : 230 [°C] (extruder) / 80 [°C] (bed)  
### Runout sensor support
Native connector for a runout sensor is not present on the Di3 board. You can find adapters online (or create your own), that will take the signal on the buzzer pin (and so, your buzzer will not be usable anymore).  
That is why said _pins_SANGUINOLOLU_11.h, line 269_ is enclosed by an #ifdef. If the runout is activated, the speaker cannot be used. Beware that both detector and speaker are not wired at the same time.  
_Enable_ : Configuration.h line 1174, comment with // if you do not want to use it and have speaker instead
### Memory management
To enable all those features, somme compromises have been made :
* **Slim LCD menu** : activated slim menus (remove some extraneous items) to recover space, Configuration.h line 1578
* **M503 disable** : M503 command has been disabled (EEPROM read on serial line), Configuration.h line 1477
* **Minimize size compilation flags** : added flags for minimizing size of the code (platformio.ini lin 523/524) - may slower execution, but nothing noticed yet -.  
## Build and upload
_Recommending VSCode build (see Marlin firmware description below), can't ensure compilation flags will be taken on Arduino environment and space may miss._  
_You need to have the U8glib installed in Arduino libraries folder : https://www.arduinolibraries.info/libraries/u8glib_.  
First, flash a bootloader thanks to an Arduino (did it with a Nano) : https://www.instructables.com/Using-an-Arduino-to-Flash-the-Melzi-Board-Wanhao-I/  
Then connect to printer directly, open project with VSCode (with PlatformIO add-on) and click "Upload" under env:sanguino1284p . It will build then upload to the printer.  
_In my case, I added the line upload_port = COM[3] (platformio.ini line 535) to be able to upload. Set the correct COM port number for your printer._

=========================================================================================================================

# Marlin 3D Printer Firmware

![GitHub](https://img.shields.io/github/license/marlinfirmware/marlin.svg)
![GitHub contributors](https://img.shields.io/github/contributors/marlinfirmware/marlin.svg)
![GitHub Release Date](https://img.shields.io/github/release-date/marlinfirmware/marlin.svg)
[![Build Status](https://github.com/MarlinFirmware/Marlin/workflows/CI/badge.svg?branch=bugfix-2.0.x)](https://github.com/MarlinFirmware/Marlin/actions)

<img align="right" width=175 src="buildroot/share/pixmaps/logo/marlin-250.png" />

Additional documentation can be found at the [Marlin Home Page](https://marlinfw.org/).
Please test this firmware and let us know if it misbehaves in any way. Volunteers are standing by!

## Marlin 2.0

Marlin 2.0 takes this popular RepRap firmware to the next level by adding support for much faster 32-bit and ARM-based boards while improving support for 8-bit AVR boards. Read about Marlin's decision to use a "Hardware Abstraction Layer" below.

Download earlier versions of Marlin on the [Releases page](https://github.com/MarlinFirmware/Marlin/releases).

## Building Marlin 2.0

To build Marlin 2.0 you'll need [Arduino IDE 1.8.8 or newer](https://www.arduino.cc/en/main/software) or [PlatformIO](http://docs.platformio.org/en/latest/ide.html#platformio-ide). Detailed build and install instructions are posted at:

  - [Installing Marlin (Arduino)](http://marlinfw.org/docs/basics/install_arduino.html)
  - [Installing Marlin (VSCode)](http://marlinfw.org/docs/basics/install_platformio_vscode.html).

### Supported Platforms

  Platform|MCU|Example Boards
  --------|---|-------
  [Arduino AVR](https://www.arduino.cc/)|ATmega|RAMPS, Melzi, RAMBo
  [Teensy++ 2.0](http://www.microchip.com/wwwproducts/en/AT90USB1286)|AT90USB1286|Printrboard
  [Arduino Due](https://www.arduino.cc/en/Guide/ArduinoDue)|SAM3X8E|RAMPS-FD, RADDS, RAMPS4DUE
  [LPC1768](http://www.nxp.com/products/microcontrollers-and-processors/arm-based-processors-and-mcus/lpc-cortex-m-mcus/lpc1700-cortex-m3/512kb-flash-64kb-sram-ethernet-usb-lqfp100-package:LPC1768FBD100)|ARM® Cortex-M3|MKS SBASE, Re-ARM, Selena Compact
  [LPC1769](https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/general-purpose-mcus/lpc1700-cortex-m3/512kb-flash-64kb-sram-ethernet-usb-lqfp100-package:LPC1769FBD100)|ARM® Cortex-M3|Smoothieboard, Azteeg X5 mini, TH3D EZBoard
  [STM32F103](https://www.st.com/en/microcontrollers-microprocessors/stm32f103.html)|ARM® Cortex-M3|Malyan M200, GTM32 Pro, MKS Robin, BTT SKR Mini
  [STM32F401](https://www.st.com/en/microcontrollers-microprocessors/stm32f401.html)|ARM® Cortex-M4|ARMED, Rumba32, SKR Pro, Lerdge, FYSETC S6
  [STM32F7x6](https://www.st.com/en/microcontrollers-microprocessors/stm32f7x6.html)|ARM® Cortex-M7|The Borg, RemRam V1
  [SAMD51P20A](https://www.adafruit.com/product/4064)|ARM® Cortex-M4|Adafruit Grand Central M4
  [Teensy 3.5](https://www.pjrc.com/store/teensy35.html)|ARM® Cortex-M4|
  [Teensy 3.6](https://www.pjrc.com/store/teensy36.html)|ARM® Cortex-M4|
  [Teensy 4.0](https://www.pjrc.com/store/teensy40.html)|ARM® Cortex-M7|
  [Teensy 4.1](https://www.pjrc.com/store/teensy41.html)|ARM® Cortex-M7|

## Submitting Changes

- Submit **Bug Fixes** as Pull Requests to the ([bugfix-2.0.x](https://github.com/MarlinFirmware/Marlin/tree/bugfix-2.0.x)) branch.
- Follow the [Coding Standards](http://marlinfw.org/docs/development/coding_standards.html) to gain points with the maintainers.
- Please submit your questions and concerns to the [Issue Queue](https://github.com/MarlinFirmware/Marlin/issues).

## Marlin Support

For best results getting help with configuration and troubleshooting, please use the following resources:

- [Marlin Documentation](http://marlinfw.org) - Official Marlin documentation
- [Marlin Discord](https://discord.gg/n5NJ59y) - Discuss issues with Marlin users and developers
- Facebook Group ["Marlin Firmware"](https://www.facebook.com/groups/1049718498464482/)
- RepRap.org [Marlin Forum](http://forums.reprap.org/list.php?415)
- [Tom's 3D Forums](https://forum.toms3d.org/)
- Facebook Group ["Marlin Firmware for 3D Printers"](https://www.facebook.com/groups/3Dtechtalk/)
- [Marlin Configuration](https://www.youtube.com/results?search_query=marlin+configuration) on YouTube

## Credits

The current Marlin dev team consists of:

 - Scott Lahteine [[@thinkyhead](https://github.com/thinkyhead)] - USA &nbsp; [Donate](http://www.thinkyhead.com/donate-to-marlin)
 - Roxanne Neufeld [[@Roxy-3D](https://github.com/Roxy-3D)] - USA
 - Chris Pepper [[@p3p](https://github.com/p3p)] - UK
 - Bob Kuhn [[@Bob-the-Kuhn](https://github.com/Bob-the-Kuhn)] - USA
 - Erik van der Zalm [[@ErikZalm](https://github.com/ErikZalm)] - Netherlands &nbsp; [![Flattr Erik](https://api.flattr.com/button/flattr-badge-large.png)](https://flattr.com/submit/auto?user_id=ErikZalm&url=https://github.com/MarlinFirmware/Marlin&title=Marlin&language=&tags=github&category=software)

## License

Marlin is published under the [GPL license](/LICENSE) because we believe in open development. The GPL comes with both rights and obligations. Whether you use Marlin firmware as the driver for your open or closed-source product, you must keep Marlin open, and you must provide your compatible Marlin source code to end users upon request. The most straightforward way to comply with the Marlin license is to make a fork of Marlin on Github, perform your modifications, and direct users to your modified fork.

While we can't prevent the use of this code in products (3D printers, CNC, etc.) that are closed source or crippled by a patent, we would prefer that you choose another firmware or, better yet, make your own.
