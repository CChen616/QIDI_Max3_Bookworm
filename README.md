### Disclaimer:
After careful consideration, our team has decided that system upgrades will not be covered under the after-sales warranty. Therefore, this image is released by me personally. Before proceeding with the upgrade, please ensure you have the necessary basic tools, such as an SD card, an SD card reader and an eMMC adapter. Due to cost considerations (mainly shipping costs), please do not request free tools or other items from our after-sales service.

Before starting, please make sure you have basic knowledge of the Linux system and have made backups to prevent any accidents during the upgrade. For instructions on how to create a backup image, please refer to this link.
https://github.com/QIDITECH/QIDI_MAX3/issues/46#issuecomment-2118563353

### About the system
This system is built on Debian 12 Bookworm. In addition to the basic components like Klipper, Moonraker, and Fluidd, it also includes some plugins such as Klipper_ShakeTune, Moonraker-Timelapse, Spoolman, Klipper-Adaptive-Meshing-Purging, Crowsnest, and others. Due to the space taken up by some plugins and related libraries, if you are using an 8GB eMMC, the available space may be only about 1GB. It is recommended to switch to a larger eMMC or clean up unnecessary plugins.

The user name and password are
```
usr: root
pwd: makerbase

usr: mks
pwd: makerbase
```

### Update Process:
If you don't have the option to connect via Ethernet, it is recommended to flash the MCU firmware first before writing the image. This will prevent MCU errors that could cause the UI to fail to navigate to the network connection screen.

Due to the large size of the image, it is compressed using the highly efficient 7z format. After decompressing, connect the eMMC to the computer using an SD card reader and use an image writing tool (such as BalenaEtcher) to write the image. You will also need to flash the onboard MCU firmware and update the UI. Refer to this link for instructions on flashing the MCU firmware (Use the bin and uf2 files provided in this 7z file).
https://github.com/QIDITECH/QIDI_PLUS3/issues/27#issuecomment-2073932891

**How to Upgrade the UI:**

Method 1: Place the `QD_Max3_UI5.0` UI files into the `QD_Update` folder, then insert the folder into a USB drive. Plug the USB drive into the printer. On the "About" page, a "Update" button will appear. Click "Update" and follow the prompts to restart the printer. The screen will turn white and display the update progress.

Method 2: Rename the UI file to `800_480.tft` and place it in the `/root` directory of the printer system. Restart the printer.

**Note:** After successfully flashing the new image, please avoid upgrading firmware and hardware-related packages during `apt upgrade`. This may cause the screen control program to fail to start properly.

### Modifications to Klipper and Moonraker:
This image still modifies some Klipper and Moonraker files to adapt to the screen control system. The related modifications and instructions are placed in folders under /root.

### Thumbnails:
To display thumbnails on the screen, you need to add a 110x110 size thumbnail setting in the slicing software.

### Z Offset Setting:
The standard Klipper process is to test the z_offset with leveling paper and save it to printer.cfg, then restart Klipper to apply the new configuration.

However, during the initial system development, to avoid restarting Klipper, the z_offset was saved to an external file (config.mksini) and then added to the runtime variables using SET_GCODE_OFFSET. The printer control system applies this value only at startup and when modifying the Z_offset, which can cause the z_offset to reset to zero when restarting Klipper without restarting the system.

I have modified the relevant Klipper files to store the z_offset in save_variable.cfg when the SET_GCODE_OFFSET macro runs and read it at Klipper startup to solve part of the problem. To completely solve the z_offset related issues, the screen control system may need to be rewritten.

### About Fluidd:
The Fluidd used in the original Max3 has been adjusted to make the platform movement direction consistent with the Z-axis movement arrow direction. This image has not been adjusted, so it will be reversed.

### Power Loss Recovery:
Due to the Max3 series' inability to maintain Z-axis height during a power outage, it may drop. Testing shows that power loss recovery is not ideal and is currently not implemented.

### Network:
On the first boot, the WiFi function may not work properly because the network interface renaming has not yet been applied. Restarting should resolve this issue.

Edit these setting which are used for clash proxy (I forget to remove these, maybe make a new image later)
```bash
# Login as root

sudo nano /etc/environment
# remove these setting
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
export no_proxy="localhost, 127.0.0.1, *edu.cn"

sudo visudo
# remove these setting
Defaults env_keep+="http_proxy https_proxy no_proxy"

nano ~/.gitconfig
# remove these setting
[http]
  proxy=http://127.0.0.1:7890
[https]
  proxy=http://127.0.0.1:7890

# change apt source
rm /etc/apt/sources.list
mv /etc/apt/sources.list.bak /etc/apt/sources.list
```

### Future Updates:
There should be no further updates for this image, but I will try to answer related questions that arise during use.

### Adaptation to Other Models:
The Smart3 model may have different screen control systems from the Max3 and may not be compatible. 


The Plus3 model share the same boards and screen with Max3 so this image is also applicable to Plus3, as long as you modify the printer.cfg accordingly

### Open Source Software Repositories Used:
https://github.com/Klipper3d/klipper </br>
https://github.com/Arksine/moonraker </br>
https://github.com/fluidd-core/fluidd </br>
https://github.com/mainsail-crew/crowsnest </br>
https://github.com/mainsail-crew/moonraker-timelapse </br>
https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging </br>
https://github.com/Frix-x/klippain-shaketune </br>
https://github.com/Donkie/Spoolman

### Special Thanks:
Inspired by
https://github.com/billkenney/update_max3_plus3
