# tableturf-grinder [![Build](https://github.com/white-axe/tableturf-grinder/actions/workflows/build.yml/badge.svg)](https://github.com/white-axe/tableturf-grinder/actions/workflows/build.yml)

This is a program for AVR microcontrollers that uses the [LUFA library](https://github.com/abcminiuser/lufa) to control a Nintendo Switch console to automatically lose repeatedly in Splatoon 3's Tableturf Battle gamemode. Since every non-resign loss against a computer-controlled opponent gives you 40 experience points, this is a valid way to grind for experience points. This program is based on [progmem/Switch-Fightstick](https://github.com/progmem/Switch-Fightstick).

### Alternatives

* [fga401/AutoTableTurf](https://github.com/fga401/AutoTableTurf) uses a capture card to capture the console screen and a Bluetooth adapter to act as a controller. Similar to this program but attempts to actually win at Tableturf.

## Required hardware

* An 8-bit AVR microcontroller board with a USB port, such as an Arduino Uno, Leonardo, Mega 2560 or Micro, or a Teensy++ 2.0. 
* A USB-A cable that can connect to the board. Most boards either require a USB-A to micro USB cable, USB-A to mini USB cable or USB-A to USB-C cable.
* If your computer lacks USB-A ports (for example if you're using a Mac), you'll also need an adaptor that can connect USB devices to your computer.

If your microcontroller board is not one of the ones mentioned above, make sure that the board has one of the following supported microcontrollers connected to the USB port:

* AT90USB82
* AT90USB162
* AT90USB646
* AT90USB647
* AT90USB1286
* AT90USB1287
* ATmega8U2
* ATmega16U2
* ATmega16U4
* ATmega32U2
* ATmega32U4

Although the official Arduino Uno R3 is compatible because it has an ATmega8U2 or ATmega16U2 acting as a secondary USB processor, most Arduino Uno clones are not compatible as they usually replace it with an FT232RL which is not capable of being programmed to act as a human interface device. Make sure to thoroughly check the documentation of your board.

## Software installation

If you are using an Arduino Uno, Leonardo, Mega 2560, Micro or a Teensy++ 2.0 or any board with the same microcontrollers, you can download a prebuilt version of this program from [the releases page on GitHub](https://github.com/white-axe/tableturf-grinder/releases/tag/latest). The files on the releases page are automatically recompiled every time this repository is updated. Make sure to download the correct file for your board:

| Board | File |
| -: | :- |
| Teensy++ 2.0 | Joystick-at90usb1286.hex |
| Arduino Leonardo / Arduino Micro | Joystick-atmega32u4.hex |
| Arduino Uno / Arduino Mega 2560 | Joystick-atmega16u2.hex |

N.B. if you have an older revision of the Arduino Uno or Arduino Mega 2560 board that uses an ATmega8U2 instead of an ATmega16U2 (you can check by reading the label on the surface of the chip), you can still use Joystick-atmega16u2.hex as the ATmega8U2 and ATmega16U2 are binary-compatible.

If you prefer to manually compile programs or if there is no prebuilt binary for your board, see the [manual compilation instructions](#manual-compilation).

Rename the downloaded file to "Joystick.hex". Now you have to flash Joystick.hex onto your microcontroller. This step varies for different boards.

### Teensy++ 2.0

The [Teensy Loader](https://www.pjrc.com/teensy/loader.html) application can be used to upload .hex files onto Teensy boards through the USB port.

### Arduino Leonardo / Arduino Micro

The [Arduino IDE](https://www.arduino.cc/en/software) can be used for these boards to upload .hex files through the USB port. Go to File > Preferences and check the checkbox for "Show verbose output during upload". Use the Arduino IDE to upload any sketch, and then copy the avrdude command that it uses to upload the compiled .hex file to your Arduino board. Copy that command to a terminal, replacing (in the command) the path to the .hex file from Arduino IDE with the path to Joystick.hex. Make sure you double-press the reset button on the board a few seconds before uploading Joystick.hex so that avrdude can communicate with the bootloader.

### Arduino Uno / Arduino Mega 2560
The primary microcontroller on these boards does not have USB capabilities, so you'll have to flash Joystick.hex onto the USB microcontroller on the board by following [this guide](https://docs.arduino.cc/hacking/software/DFUProgramming8U2). Just use Joystick.hex as the firmware file. If you want to use your Arduino board for something else afterwards, you'll have to flash the official firmware .hex file back onto the USB microcontroller.

N.B. it is probably not possible to flash the ATmega16U2 or ATmega8U2 on other Arduino boards such as the Arduino Due in the same manner. An AVR programmer may be required for these boards.

## Usage

Now that you have the program uploaded to your microcontroller board, place your Nintendo Switch into its dock and open Splatoon 3. You can select any Rival (not players), but it is recommended to select the level 1 Baby Jelly in Tableturf Battle (in order to minimize the time per battle, we want a low level opponent so they don't use specials whose animations will prolong the battle). Once you get to the screen where you have to select your deck, plug the USB-A end of the cable into one of the USB ports in your dock and the other end of the cable into your microcontroller board. The program will keep running until you unplug the board.

## Manual compilation

Here are the instructions for manually compiling the Joystick.hex files.

First, you'll have to configure this program for your microcontroller board. Open the "makefile" file in this repository and change the `MCU` and `F_CPU` to correspond to the microcontroller that handles USB connections. `MCU` is the chip name of the target microcontroller chip in lowercase. `F_CPU` is the CPU frequency of the microcontroller in hertz. Example values:

| | `MCU` | `F_CPU` |
| -: | :- | :- |
| Teensy++ 2.0 | `at90usb1286` | `16000000` |
| Arduino Leonardo / Arduino Micro | `atmega32u4` | `16000000` |
| Arduino Uno / Arduino Mega 2560 | `atmega16u2` | `16000000` |

Next, you have to download the LUFA library. If you used `git clone` to download this repository, you can just run `git submodule update --init --recursive` to download LUFA into the correct location. If you downloaded this repository using the "Download ZIP" button on the GitHub website, you'll have to [download LUFA manually](https://github.com/abcminiuser/lufa/archive/refs/tags/LUFA-210130.zip) and then extract it into the LUFA folder in this repository so that the LUFA folder in this repository contains the README.txt from LUFA.

Next, compile this program. If you're using Linux or macOS, you'll need to install the `make` and `avr-libc` packages from your package manager (e.g. APT or RPM for Linux or Homebrew for macOS). If you're using Windows, the easiest way is to install Windows Subsystem for Linux 2 and then install and use `make` and `avr-libc` in there. Once you have `make` and `avr-libc` installed, just run the command `make` while inside this directory to compile the program. This will result in a file called "Joystick.hex".
