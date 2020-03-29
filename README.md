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
Raspbian Buster comes preinstalled with the official Bluetooth protocol stack BlueZ. However some additional steps are needed to stream audio to a Bluetooth speaker. The commands bellow are issued to Raspbery Pi either locally or remotely by using SSH.
1. Install BlueALSA proxy
   - `sudo apt install bluealsa`
2. Add pi to the bluetooth group
   - `sudo adduser pi bluetooth`
3. Edit the bluetooth unit file
   - Open the bluetooth.service for editing: `sudo nano /lib/systemd/system/bluetooth.service`
   - Add the option `--noplugin=sap` to the end of the ExecStart parameter and save the file.
4. Restart Raspberry Pi
   - `sudo reboot`
   - Check the bluetooth service is running: `sudo systemctl status bluetooth*`
5. Pair Bluetooth speaker
   - Issue the command `bluetoothctl`
   - Switch on the bluetooth speaker and turn one the discovery mode.
   - Scan the device: `scan on`
   - If the device is found bluetoothctl will return its address and name. For example 70:99:1C:88:B0:06 JBL GO 2
   - Pair the device by using the address found: `pair 70:99:1C:88:B0:06`
   - Connect to the device: `connect 70:99:1C:88:B0:06`
   - Trust the device for automatic reconnection: `trust 70:99:1C:88:B0:06`
   - Turn off scanning: `scan off`
   - Exit bluetoothctl: `quit`
6. Test Bluetooth speaker with some audio
   - Ensure the bluetooth speaker is connected as described above
   - Copy the [WAV sound file](piano2.wav) to Raspbery Pi or use your own
   - Play the sound to the bluetooth speaker: `aplay -D bluealsa:DEV=70:99:1C:88:B0:06 piano2.wav`

## Setting up the mpd server application
The host operating system (Raspbian in this case) hosts a media player server that is capable to play sounds and music to various audio outputs including the bluetooth speaker.
1. Install the mpd server
   - `sudo apt install mpd`
2. Configure the mpd.conf file
   - Open the mpd.conf file for editing: `sudo nano /etc/mpd.conf`
   - Add the following audio output:
     ```
     audio_output {
          type            "alsa"
          name            "JBL GO"
          device          "bluealsa:DEV=70:99:1C:88:B0:06,PROFILE=a2dp"
          mixer_type      "software"
          format          "44100:16:2"
     }
     ```
   - Change the directories to your own needs. For example
     ```
     music_directory         "/home/pi/music"
     playlist_directory      "/home/pi/playlists"
     ```
   - Restart the daemon: `sudo systemctl restart mpd`
3. Test the media player server
   - Ensure the bluetooth speaker is on and connected 
   - Install a media player client: `sudo apt install mpc`
   - Copy some sound files to the mpd music directory as defined in mpd.conf
   - Add a sound file to the mpd queue: `mpc add piano2.wav`
   - Update the mpd database: `mpc update`
   - Play the sound file: `mpc play 1'
4. Update hosts file in Raspbian (optional)
   - This step is only needed if TSL is used in Hass.io and the router cannot route the traffic to the local host when using a public DuckDNS domain name.
   - Edit hosts: `sudo nano /etc/hosts'
   - Add the text `127.0.0.1 xxx.duckdns.org` where xxx is the DuckDNS name used
   - Save hosts file
## Setting up Hass.io
Hass.io integrates an [mpd media player](https://www.home-assistant.io/integrations/mpd/) by default.
1. Add the mpd integration 
   - Edit the configuration.yaml file and add the following text
   ```
   media_player:
      - platform: mpd
        host: 127.0.0.1
   ```
2. Restart Home Assistant
3. Add a [Media Control Card](https://www.home-assistant.io/lovelace/media-control/) in Lovelace UI
4. Send a TTS sound to mpd from the Media Control Card.

## TODO list
- [ ] Stream internet radio stations from Hass.io
- [ ] Build a docker image with the above configuration

