# Quick Start Guide for Raspberry IMX327 IMX462 IMX290

## Hardware Description
  - [462 HW Manual](https://www.inno-maker.com/product/cam-mipi462mono/)
  - [290 HW Manual](https://www.inno-maker.com/product/cam-mipi290mono/)


## Quick Start For Raspberry PI
- Step1, Open the config.txt on Raspbian:
  - sudo nano /boot/firmware/config.txt
- Step2, Add the following text below the [all] line in the config.txt file
  - dtoverlay=imx290,clock-frequency=74250000
- Step3, change camera_auto_detect=1
  - camera_auto_detect=0
- Step4,reboot and use libcamera for preview
  - sudo reboot
  - libcamera-hello -t 0 
- Step5,downlod json file for imx290/imx426/imx327 sensor module
  - git clone https://github.com/INNO-MAKER/CAM-IMX462Mono.git
  - cd /home/pi/CAM-IMX462Mono
  - sudo chmod -R a+rwx *
- Step6,use libcamera for preview:
  - libcamera-still -t 0 --tuning-file /home/pi/CAM-IMX462Mono/innomakerpi5_imx290.json

## FAQ
### 1, Not works on PI3 with bullseys os
Reason: On Raspberry Pi 3 and earlier devices running Bullseye you need to Resolve Method: re-enable Glamor in order to make the X-Windows hardware accelerated preview window work. 

Open terminal window
  - sudo raspi-config

Choose 
  - Advanced Options
  - Glamor 
  - Yes. 
  
Finally quit raspi-config and let it reboot your Raspberry Pi.

### 2 Not works on Raspberry PI5/CM5 with lastest OS
Reason：lack of json file. 

Resolve Method: Download json file from legacy os or from our github.