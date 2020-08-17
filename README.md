Connect Raspberry pi and PC
Connect Raspberry Pi and PC via USB-A to USB-A
Enable the USB driver
enable SSH
Next, need to enable the USB driver which provides the gadget modes, by editing two configuration files.
sudo nano /boot/config.txt
Scroll to the bottom and append the line below:
dtoverlay=dwc2
Press CTRL+O followed by Enter to save, and then CTRL+X to quit.
sudo nano /etc/modules
Append the line below, just after the i2c-dev line:
dwc2
Press CTRL+O followed by Enter to save, and then CTRL+X to quit.
Now reboot the Pi with the command:
sudo reboot
Gadget mode allows connections to USB Host Such has as a PC.
Create a container file
To enable mass storage device mode, we need to create a large file to act as the storage medium on the SD card. This file will emulate the USB flash drive that the PC sees.
The command below will create an empty 2GB binary file (change the count=2048 parameter if you want a different size). Please note that this will be limited by the available free space on your SD card (check the Avail column in df -h), and it may take a few minutes to complete the setup:
sudo dd bs=1M if=/dev/zero of=/piusb.bin count=2048
We now need to format the file as an ext4 file system so that this can operate on any system. Enter the command below:
sudo mkfs.ext4 /piusb.bin 
Mount the container file
Now mount the container file locally so we can copy test files. First, create a folder on which we can mount the file system:
sudo mkdir /mnt/usb_share
Now let’s add this to fstab, the configuration file that records our available disk partitions:
sudo nano /etc/fstab
Append the line below to the end of the file:
/piusb.bin /mnt/usb_share ext4 users,umask=000 0 2
Press CTRL+O followed by Enter to save, and then CTRL+X to quit.
The line we added to fstab allows the USB file system to be error-checked and mounted automatically at boot time. Instead of rebooting, we can manually reload fstab with the command below:
sudo mount -a
Test the Storage
The command below will enable USB mass storage device mode, and the PC should pop up a dialogue box
execute this command on Pi:
sudo modprobe g_mass_storage file=/piusb.bin stall=0 ro=1
Now Copy a test file to view on the Raspberry Pi.
since it is USB storage you can see it in media
Check the device in your PC and try to copy
execute this on pc
cp source_path Destination_path
after copying files from pc to USB storage from pc
open raspberry  pi and check the file is coming their or not
If the file is viewed on Pi then Automate with down steps
If you can see the copy files in raspberry pi then delete that file
from pi and create an autorun.sh file in usb_share
script
#!/bin/bash
# Recommend syntax for setting an infinite while loop
while :
do
   cp source_file_path .
done

it will autorun that when it is connected
Command to Unmount Device
sudo modprobe -r g_mass_storage

Reference links:
this is just reference link:
https://magpi.raspberrypi.org/articles/pi-zero-w-smart-usb-flash-drive
same commands we can use for raspberry pi 4
http://trac.gateworks.com/wiki/linux/OTG#device
https://www.infradead.org/~mchehab/rst_conversion/usb/gadget_configfs.html
Autorun reference link:
https://askubuntu.com/questions/642511/how-to-autorun-files-and-scripts-in-ubuntu-when-inserting-a-usb-stick-like-autor
