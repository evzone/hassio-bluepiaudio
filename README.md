# hassio-bluepiaudio
Configuration files and notes to add bluetooth audio support in [Hass.io](https://www.home-assistant.io/hassio/)

## Purpose
The aim of this repository is to document the configuration steps to add bluetooth audio in Home Assistant running in the Hass.io ecosystem.

## Environment
The configuration is based on a [Home Assistant Supervised](https://www.home-assistant.io/hassio/installation/#alternative-install-home-assistant-supervised-on-a-generic-linux-host) installation and not on a Home Assistant image. Home Assistant Supervised gives the possibility to run Hass.io with all add-ons features but it also makes the Linux OS available to run other server applications in parallel but outside the Hass.io environment. 

## Prerequisites
- Raspberry Pi with bluetooth interface. This configuration is made on a Pi 4 that uses an embedded Bluetooth controller.
- A bluetooth speaker. In this case a JBL GO 2 has been used.
- Raspbian Buster Lite installed 
  - Version: February 2020
  - Release date: 2020-02-13
  - Kernel version: 4.19
- Home Assistant Supervised installed and running on Raspbian.

## Setting up the Bluetooth speaker
Raspbian Buster comes preinstalled with the official Bluetooth protocol stack BlueZ. However the following steps are needed to stream audio to a Bluetooth speaker.
1. Install BlueALSA proxy
   - pi@raspberrypi:~ $ `sudo apt install bluealsa`
2. Add pi to the bluetooth group
   - pi@raspberrypi:~ $ `sudo adduser pi bluetooth`
3. Edit the bluetooth unit file

   1. Item 3a
   1. Item 3b
