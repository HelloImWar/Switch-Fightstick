# tableturf-grinder

This is a program for AVR microcontrollers that uses the [LUFA library](https://github.com/abcminiuser/lufa) to control a Nintendo Switch console to automatically lose repeatedly in Splatoon 3's Tableturf Battle gamemode. Since every non-resign loss against a computer-controlled opponent gives you 40 experience points, this is a valid way to grind for experience points. This program is based on [progmem/Switch-Fightstick](https://github.com/progmem/Switch-Fightstick).

### Alternatives

* [fga401/AutoTableTurf](https://github.com/fga401/AutoTableTurf) uses a capture card to capture the console screen and a Bluetooth adapter to act as a controller. Similar to this program but attempts to actually win at Tableturf.

## Required hardware

* An AVR microcontroller board with a USB port, such as an Arduino Uno, Leonardo, Nano or Micro, or a Teensy++ 2.0. 
* A USB-A cable that can connect to the board. Most boards either require a USB-A to micro USB cable, USB-A to mini USB cable or USB-A to USB-C cable.
* If your computer lacks USB-A ports (for example if you're using a Mac), you'll also need an adaptor that can connect USB devices to your computer.

## Software installation

If you are using an Arduino Uno, Leonardo, Nano, Micro or a Teensy++ 2.0 or any board with the same microcontrollers, you can download a prebuilt version of this program from [the releases page on GitHub](https://github.com/HelloImWar/tableturf-grinder/releases/tag/latest). The files on the releases page are automatically recompiled every time this repository is updated. Make sure to download the correct file for your board:

| Board | File |
| -: | :- |
| Teensy++ 2.0 | Joystick-at90usb1286.hex |
| Arduino Leonardo / Arduino Micro | Joystick-atmega32u4.hex |
| Arduino Uno / Arduino Nano | Joystick-atmega16u2.hex |

If you prefer to manually compile programs or if there is no prebuilt binary for your board, see the [manual compilation instructions](#manual-compilation).

Rename the downloaded file to "Joystick.hex". Now you have to flash Joystick.hex onto your microcontroller. This step varies for different boards.

### Teensy++ 2.0

The [Teensy Loader](https://www.pjrc.com/teensy/loader.html) application can be used to upload .hex files onto Teensy boards through the USB port.

### Arduino Leonardo / Arduino Micro

The [Arduino IDE](https://www.arduino.cc/en/software) can be used for these boards to upload .hex files through the USB port. Go to File > Preferences and check the checkbox for "Show verbose output during upload". Use the Arduino IDE to upload any sketch, and then copy the avrdude command that it uses to upload the compiled .hex file to your Arduino board. Copy that command to a terminal, replacing (in the command) the path to the .hex file from Arduino IDE with the path to Joystick.hex. Make sure you double-press the reset button on the board a few seconds before uploading Joystick.hex so that avrdude can communicate with the bootloader.

### Arduino Uno / Arduino Nano
The primary microcontroller on these boards does not have USB capabilities, so you'll have to flash Joystick.hex onto the USB microcontroller on the board by following [this guide](https://docs.arduino.cc/hacking/software/DFUProgramming8U2). Just use Joystick.hex as the firmware file. If you want to use your Arduino board for something else afterwards, you'll have to flash the official firmware .hex file back onto the USB microcontroller.

## Usage

Now that you have the program uploaded to your microcontroller board, place your Nintendo Switch into its dock and open Splatoon 3. You can select any Rival (not players), but it is recommended to select the level 1 Baby Jelly in Tableturf Battle (in order to minimize the time per battle, we want a low level opponent so they don't use specials whose animations will prolong the battle). Once you get to the screen where you have to select your deck, plug the USB-A end of the cable into one of the USB ports in your dock and the other end of the cable into your microcontroller board. The program will keep running until you unplug the board.

## Manual compilation

Here are the instructions for manually compiling the Joystick.hex files.

First, you'll have to configure this program for your microcontroller board. Open the "makefile" file in this repository and change the `MCU`, `ARCH` and `F_CPU` to correspond to the microcontroller that handles USB connections. Example values:

| | `MCU` | `ARCH` | `F_CPU` |
| -: | :- | :- | :- |
| Teensy++ 2.0 | `at90usb1286` | `AVR8` | `16000000` |
| Arduino Leonardo / Arduino Micro | `atmega32u4` | `AVR8` | `16000000` |
| Arduino Uno / Arduino Nano | `atmega16u2` | `AVR8` | `16000000` |

Next, you have to download the LUFA library. If you used `git clone` to download this repository, you can just run `git submodule update --init --recursive` to download LUFA into the correct location. If you downloaded this repository using the "Download ZIP" button on the GitHub website, you'll have to [download LUFA manually](https://github.com/abcminiuser/lufa/archive/refs/tags/LUFA-210130.zip) and then extract it into the LUFA folder in this repository so that the LUFA folder in this repository contains the README.txt from LUFA.

Next, compile this program. If you're using Linux or macOS, you'll need to install the `make` and `avr-libc` packages from your package manager (e.g. APT or RPM for Linux or Homebrew for macOS). If you're using Windows, the easiest way is to install Windows Subsystem for Linux 2 and then install and use `make` and `avr-libc` in there. Once you have `make` and `avr-libc` installed, just run the command `make` while inside this directory to compile the program. This will result in a file called "Joystick.hex".
