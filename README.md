# AGENIUS-FW-MOD  
**Artillery Genius Firmware Mod**  


<img align="right" width="175" src="https://github.com/MarlinFirmware/Marlin/raw/2.0.x/buildroot/share/pixmaps/logo/marlin-250.png" style="max-width:100%;">

<img align="left" width="175" src="https://github.com/pinguinpfleger/ASWX1-FW-MOD/blob/2.0.x/artillery_logo_brand.png?raw=true" style="max-width:100%;">

 
The AGENIUS-FW-MOD is an optimization for the Artillery Genius 3D printer.  
The Artillery Genius is delivered with Marlin 1.19 [link](http://www.artillery3d.com/DownLoad/15688.html) and deactivated EEPROM memory function `M500`.  
  
This optimized firmware is based on [Marlin Firmware Version 2.0.x](https://github.com/MarlinFirmware/Marlin/tree/2.0.x)  
and on Marlin [Artillery Genius config](https://github.com/MarlinFirmware/Configurations/blob/master/config/examples/Artillery/Genius/Configuration.h)  

<br>

There is also an [optimized firmware for Artillery Sidewinder X1 touch display](https://github.com/pinguinpfleger/ASWX1-TFTFW-MOD) which you can install too but it is optional.  

## Releases

See [upstream](https://github.com/pinguinpfleger/ASWX1-FW-MOD#releases)
   
## Improvements  

1. **Save to EEPROM**  
   Enabled EEPROM `M500` to persist settings.  
   Now you can store PIDs and Z-Offsets to EEPROM  

2. **LIN_ADVANCE activated**  
    Linear Advance brings you better dimensional precision due to reduced bleeding edges.  
    Higher printing speeds are possible without any loss of print quality - as long as your extruder can handle the needed speed changes.  
    Visible and tangible print quality is increased even at lower printing speeds.  
    No need for high acceleration and jerk values to get sharp edges.  
   Read https://marlinfw.org/docs/features/lin_advance.html for more details and how to calibrate.  
   By default the K_Factor is set to 0, so it is disabled.  
   To enable it using gcode you should first calibrate your specific K factor.  
   You can do this [here](https://marlinfw.org/tools/lin_advance/k-factor.html). Accordingly set the K factor within your slicer using e.g. `M900 K0.2`  

4. **S_CURVE_ACCELERATION activated**  
   This option eliminates vibration during printing by fitting a Bézier curve to move acceleration, producing much smoother direction changes.  
  
5. **ADAPTIVE_STEP_SMOOTHING activated**  
    Adaptive Step Smoothing increases the resolution of multi-axis moves, particularly at step frequencies below 1kHz (for AVR) or 10kHz (for ARM), where aliasing between axes in multi-axis moves causes audible vibration and surface artifacts.
    The algorithm adapts to provide the best possible step smoothing at the lowest stepping frequencies.  



## Individual adjustments  
Individual adjustments can be made in [Configuration.h](/Marlin/Configuration.h) and [Configuration_adv.h](/Marlin/Configuration_adv.h)  
  
Examples can be found in the links below.  
[Enabling BL-Touch](https://5020dafe-17d8-4c4c-bf3b-914a8fdd5140.filesusr.com/ugd/f5a1c8_d40d077cf5c24918bd25b6524f649f11.pdf), also look at [Issue \#6](https://github.com/pinguinpfleger/ASWX1-FW-MOD/issues/6#issuecomment-619517281)  
[E3D Hemera - Artillery (Evnovo) Sidewinder X1 Firmware Modification](https://e3d-online.dozuki.com/Guide/Hemera+Artillery+(Evnovo)+Sidewinder+X1+Firmware+Modification/121?fbclid=IwAR2BITq4oZrkuaCNYb0VciskU4G2GWVfzusxQzLEnCxz8Fv-EvAqf_lkZS4) 
  
[Read more about configuring Marlin](https://marlinfw.org/docs/configuration/configuration.html)  
  
Of course the firmware must be recompiled than.  
There are serveral ways to compile.

\[Linux / Mac\]  
An easy one is [platformio CLI](https://docs.platformio.org/en/latest/installation.html#installation-methods) command.  
To complile you just need execute `platformio run` in the root folder of this repository (where platformio.ini is also located).  
The new compiled firmware is saved here: .pio/build/megaatmega2560/firmware.hex  

\[Windows\]  
There is a great instruction how to [use Arduino-IDE on Marlin.org](https://marlinfw.org/docs/basics/install_arduino.html).  
This should be the easiest way on Windows.  
  
Board: "Arduino/Genuino Mega or Mega 2560"  
Processor: "ATmega2560 (Mega 2560)"  

Customize your configs, use "Sketchs -> Export compiled Binary", flash

  
## Flashing  
**Important**: Don't forget to read and backup your current EEPROM-settings with `M503`!  
  
The display and the USB-Port are sharing the same wires so flashing the motherboard-firmware need some extra work.  
There are two ways possible to flash the firmware.  
  
### 1. Disconnect the display  
**be careful, DISCONNECT the 230V power cord, you do not need it for flashing, everything will be savely powered using the USB Connection**  
Either compile the attached source or flash the precompiled hex file. For flashing the precompiled hex file you can e.g. install and use Prusa Slic3r 2.x. In order to be able to flash the firmware you must unscrew the bottom plate of you printer **(danger, 230V connectors, unplug power cord first)** and disconnect the MKS LCD cable. Otherwise it wont let you flash as both, the TFT and Flasher communicate using serial. After flashing and powering off you simply reconnect the tft+enclosure fan and install the bottom plate again.

  
### 2. Loop method
With this method we try to talk to the motherboard before the display is ready to listen. 
You have to use a Linux or Mac mashine for this.
  
The [flash.sh](/flash.sh) script is trying to flash the command in a loop until the command finishs succesfully.  
Steps:  
- Make sure Artillery Sidewinder X1 is unplugged from the power supply.  
- Unplug USB Cable too.  
- Connect your linux box (or mac) with the printer USB-Port  
- Place firmware.hex and flash.sh in same directory and execute flash.sh.  
- You will see timeout errors thats normal.  
- Plug USB Cable  
- Leave flash.sh running and unplug / plug USB Cable or hit the reset button until the flash.sh finishs  
  
A detailed explanation of this procedure is available from [joskfg at Github](https://www.thingiverse.com/make:734108/)
  
  
### Reset to factory defaults  
I recommend to reset the newly flashed firmware to its defaults and overwrite any older settings.  
***Don't forget to read your settings by `M503` and copy/save them somewhere.***  
The gcode command to reset the firmware to the hardcoded defaults is `M502`,  
followed by `M500` to save these default setting to EEPROM.  
After that you can restore your settings from your M503-Backup e.g. `M92 X80.12 Y80.12 Z399.78 E420.00`  
Save again by `M500` and finally reload all stored data from Eeprom by `M501`  

You can execute the gcode commands using a terminal program like Arduino-IDE, [Pronterface](https://www.pronterface.com/)) or using the Terminal Tab in Octoprint.    
  
<br><hr>  

## Credits  
The repository here is the continuation of the MarlinFW from [**Robscar's firmware mod** at Thingiverse](https://www.thingiverse.com/thing:3856144).  
The modified firmware for the Makerbase MKS-TFT 3.2 touch display has been seperated to an own repository:  
https://github.com/pinguinpfleger/ASWX1-TFTFW-MOD
  

## Links

### Slicer Machine & Profile Settings
https://3d-nexus.com/resources/file-archives/category/8-artillery-evnovo

### Youtube
RICS 3D Marlin 2 https://www.youtube.com/watch?v=JlgykMHhMzw  
Waggster Mod https://www.youtube.com/watch?v=ynm8inRMVkE

### Other Mods
Waggster Mod (BL-Touch) https://pretendprusa.co.uk/index.php?action=downloads;cat=5  
3D Nexus (Mesh Bed Leveling, Marlin 1.1.x) https://3d-nexus.com/resources/file-archives/download/5-printer-firmware/11-artillery-swx1-marlin-1-1-9-advanced-firmware-and-gui
3D Print Beginner (also derived from Robscar's firmware) https://3dprintbeginner.com/sidewinder-x1-firmware/

<br><hr>  

## Disclaimer, use at your own risk!  
There are inherent dangers of upgrading your firmware and config files. I caution you to make sure that you completely understand the potential risks before applying/uploading any of the files provided to your 3D-Printer. The firmware and Config Files are provided "as is" without warranty of any kind, either express or implied.
