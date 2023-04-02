# Amiga 600 Build Notes

## Introduction

This page covers the process for taking a stock Amiga 600 and building a modern hardware and software setup. 

This includes:

* Cleaning recapping the Amiga mainboard
* Addition of an adapter to use an SD card as an IDE hard disk
* Installation of Workbench 2.1 on the internal SD card
* Replacement of the floppy drive with a GoTek floppy drive emulator
* Modification of the GoTek FDE to use a rotary encoder and OLED screen to select floppy images
* Installation and setup of a PiStorm accelerator
* Creation of SD cards for the Musashi and Emu68 68000-series emulators and a third SD card for changing the PiStorm firmware
* (Configuration of Musashi and Emu68, including adding kickstart roms and hard disk images)
* Installation of the mainboard in a tower case
* Exposure of the following on the case front panel: 
    * The IDE SD card and PiStorm SD card slots on the case front panel
    * USB ports attached to the PiStorm on the front panel
    * The Amiga's 9-pin mouse/joystick ports on the front panel, with the joystick port made safe for Sega Megadrive/Genesis compatible controllers
    * A separate USB port for direct connection of a USB keyboard to the Amiga mainboard (needed when using Emu68 insteal of Musashi)
* (Ensuring the system is receiving an adequate voltage with the accelerator enabled)

(Installation of RGBtoHDMI)
(Installation of a switch between USB mouse and joysticks)

Clearly not all of these steps are required to get an Amiga 600 up and running. Some are covered in more detail than others.

The result is a system which can:

* Be interfaced with more modern/convenient hardware
* Run productivity and other Workbench applications at many times the speed of an Amiga 600
* Run nearly all original Amiga games from hard disk, using WHDLoad
* Run games with much reduced loading times and, in some cases, with much faster in-game processing (e.g. execution of the computer turn in Xcom)
* Run newer homebrew or modded games designed for use with accelerators

It **will not** be well-suited to running original Amiga games from floppy images. WHDLoad does a lot of work to ensure compatibility with accelerated systems. It will also **not** be able to run AGA-only games as none of this provides the A600 with an AGA chipset.

### Parts and Tools

The following parts and tools are needed to complete all of these steps

* An Amiga 600. The case, keyboard and floppy drives are not needed.
* A working PSU capable of powering the Amiga, accelator and accessories, e.g. [this one from RetroPassion](https://www.retropassion.co.uk/product/amiga-psu-a600-boost/). Do not, for the love of God, try to use a bog-standard, 30 year old PSU for this.
* An SD card to 2.5in (44 pin) IDE adapter.
* A 2.5in (44 pin) IDE cable.
* A suitable tungsten carbide drill bit. E.g. if using nylon standoffs with a 3mm thread you need a 3.5mm drill bit.
* A set of nylon hex spacer standoffs/screws (example: [Amazon](https://www.amazon.co.uk/Standoff-Assorted-Arduino-Circuit-Motherboard/dp/B097LGSKXG/ref=sr_1_1?crid=BLZCDFRR6BK&keywords=motherboard+mounting+posts+plastic&qid=1680393704&sprefix=motherboard+mounting+posts+plastic%2Caps%2C66&sr=8-1)).
* A suitable tower case, e.g. a Micro ATX, with large enough internal dimensions to fit the A600 mainboard with clearance around the ports (at least 25cm x 35cm). This guide was compiled using [this one from Amazon](https://www.amazon.co.uk/dp/B076DFHYC1?psc=1&ref=ppx_yo2ov_dt_b_product_details). A different case will require some of the steps to be modified, but probably no by much.
* [A PiStorm 600 accelerator board](https://github.com/LemaruX/PiStorm600)
* A Raspberry Pi Zero W 2 or Raspberry Pi 3A+. Others may be usable, including a 3B with some desoldering, but check the documentation.
* A USB 2.1 hub
* Various screws and nuts, including two 18-20mm x 1.6mm screws and nuts.
* (Several SD and micro SD cards)
* (A USB mass storage drive, 8GB or larger)
* An SD to micro SD extender (e.g. [this from Amazon](https://www.amazon.co.uk/Extension-LANMU-Extender-Converter-Monoprice-black/dp/B0855DBVXF/ref=sr_1_1_sspa?keywords=sd+card+extender&qid=1680394751&sprefix=SD+card+ext%2Caps%2C81&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1))
* An SD to SD extender (e.g. [this from Amazon](https://www.amazon.co.uk/Extension-LANMU-Convertor-Extender-Raspberry/dp/B07H2RQFNN/ref=sr_1_5?keywords=sd+card+extender&qid=1680394751&sprefix=SD+card+ext%2Caps%2C81&sr=8-5))
* An Amiga 600 USB keyboard adapter (e.g. [this from eBay](https://www.ebay.co.uk/itm/232238713511)). These are increasingly hard to find but are not needed if not installing in a tower case or if only using the Musashi emulator.
* (MegaDrive to Amiga pad adapter)

## Requirements



## Setting up native HD

### Introduction

The purpose of this set of steps is to install a version of Workbench, which can run on an unmodified Amiga 600, on an SD/CF card which is attached - via an adapter - to the Amiga’s internal IDE connector.

This allows the Amiga to be run without the accelerator installed to check that everything is working normally and as a default, basic setup. It therefore keeps things as simple as possible, without any of the multitude of addons which can make Workbench more sophisticated. E.g. ClassicWB is available for the stock 68000 but all versions require min 2MB RAM, which an unmodified A600 does not. The native IDE drive therefore needs a basic WB installation.

### Requirements

* WinUAE running under Windows (native or VM) or under Linux/WINE.
* A Kickstart 2.05 ROM
* Full set of Workbench 2.1 disk images (Install, Workbench, Locale, Extras, Fonts)
* A 4GB SD/CF card

> It may be possible to use FS-UAE under Linux in place of WinUAE but it lacks many of the latter’s low-level options. To mount a real disk in FS-UAE, if that's the approach you choose to take, enter the name of the corresponding block device (e.g. '/dev/sdf') in the hard disk field. To make it usable by FS-UAE you will probably have to change the permissions with e.g. `sudo chmod 777 /dev/sdf`

This process will save changes to at least the Install disk image, maybe others. Make sure you have backups before proceeding.


### Disk Selection

Without special hardware and drivers for it the A600 can use only one IDE device at a time.

The maximum partition size the A600 can see using the filesystem built into the Kickstart ROM (FastFileSystem) is 4GB, with a total disk size of 8GB. It’s recommended in several places to keep partitions under 2GB and [here](http://wiki.classicamiga.com/Installing_a_large_Harddrive_%284GB_or_larger%29#:~:text=The%20Amiga's%20FFS%20file%20system,total%20HD%20size%20of%208GB.) to keep the boot partition under 2GB. The boot partition must be within the first 4GB.

These instructions assume use of a 4GB card. If using the HDF method, outlined below, in theory you should be able to use a larger card if the HDF file flashed to it is &lt;= 4GB. Don't try to flash an 8GB image to an '8GB' card. It may be too big.


### Real Disk vs HDF File

Many guides suggest mounting a real SD/CF card in WinUAE, provisioning it and transferring it to the Amiga.

Overall, it’s much easier to do all the significant work on the drive (e.g. provisioning, installing WHDLoad/games, adding software) under WinUAE using an HDF file and re-flashing it to an SD/CF card after each round of work.

If running WinUAE under Linux/WINE this is the only option as WinUAE will not recognise either empty disks or Amiga partitioned/formatted (RDB) disks attached to the host machine.

Whether using an HDF file or working directly with a real disk, it’s best to keep the total size of all partitions a healthy margin under 4GB. Different 4GB cards from different manufacturers/product lines have different capacities, generally somewhere around 3.7GB.

An HDF file well under 4GB can be safely flashed to any 4GB card.

A real SD/CF card with partitions totalling well under 4GB can be backed up using `dd` and the resulting image flashed to any 4GB card. This applies whether the real drive was provisioned directly or was itself flashed from an HDF file.

### Preparing a read SD/CF Card

Ensure that all partitions are removed from the SD/CF card.

This can be done in diskpart under Windows by running `diskpart` from the command prompt. Enter `list disk` to see the disks. If the SD/CF card is `disk 1` then run `select disk 1` and then `clean`.

Under Linux run the following, where ‘/dev/sdf’ is the SD/CF card:

`sudo dd if=/dev/zero of=/dev/sdf bs=512 count=1 conv=notrunc`

### WinUAE Configuration

Whichever approach is taken (real drive or HDF), create a WinUAE configuration with the following settings. These are designed for making the process as fast as possible:

* 68040 CPU
* JIT enabled if running under Windows, disabled if running under Linux/WINE
* CPU Emulation Speed to ‘Fastest Possible’
* FPU to ‘CPU Internal’
* Chipset to ‘Full ECS’
* 2MB Chip RAM, 8MB Z2 Fast RAM, 64MB Z3 Fast RAM
* Floppy drive speed to ‘Turbo’

Mount the Install, Workbench and any two of the other disks as DF0-DF3. It’s essential that the emulator boots from the Install image (i.e. it’s attached to DF0) and not from one of the others. If HDToolbox later displays an error message saying it cannot find ‘L:FastFileSystem’ it’s because the emulator booted from another disk.

### Hard Disk Settings

#### Use a real disk (WinUAE under Windows only)

Under ‘CD & Hard drives’ click ‘Add Hard Drive’ and select the empty SD/CF card.

#### Use a hard disk image

Under **CD & Hard drives** click **Add Hardfile** and create a new HDF file by entering a value for the size and clicking **Create**.

Creating a 3072MB/3GB HDF file provides more than enough space and keeps things simple when backing up/restoring.

Ensure the **Full drive/RDB mode** checkbox is selected.


### Partition the Disk

If re-doing this process, or repeating it with a new HDF file/SD/CF, it’s best to start with a fresh copy of the Install floppy image. It saves information about the disks it sees and this can cause confusion when working with what is already a less than user-friendly interface.

Click **Start** in WinUAE.

In Workbench double click on the Install disk, the HD Tools folder and click once on HD Toolbox. From the **Icons** menu select **Information** and click **New** under **Tool Types**.

Enter:

`SCSI_DEVICE_NAME=uaehf.device`

Note that if somehow you end up using this Install disk image in a real Amiga, this will have to be changed (e.g. `SCSI_DEVICE_NAME=scsi.device`) or removed for HDToolbox to work.

Run HDToolbox.

Select the SCSI disk and click **Change Drive Type**.

In the **Set Drive Type** window select **SCSI** as **Drive Type** and click on **Define new drive type**.

In the box which appears, click on **Read Configuration**. Once this is complete, click on OK.

Select the newly defined disk in the **Set Drive Type** window when it reappears and click on **OK**.

Back in the main HDToolbox window, with the SD/CF drive selected, click on **Save Changes to Drive**.

With the drive selected, click on **Partition Drive**.

Choose the desired partition layout:

* Note that HDToolbox may throw a ‘Not a DOS disk’ error if attempting to later format a partition greater than 1GB when using an HDF file.
* If using a real SD/CF card then keep the total size of all partitions to approx 3GB
* If using an HDF file then the total available size should already be 3GB, assuming it was created as suggested above.

Whatever the chosen configuration, make the first partition bootable and the others not bootable. Label them DH0, DH1, etc where DH0 is the bootable partition.

Once the partitions have been defined, click OK.

Select **Save Changes to Drive** and click **Exit**. Reboot the system if asked to do so. If the newly partitioned DH0 and DH1 do not appear in the main Workbench window then reboot the emulator.

If using an HDF file and the partitions still do not appear following a reboot, ensure that the **Full drive/RDB mode** checkbox is selected in the HDF file’s properties dialog for the HDF file.


### Format the Partitions

Format DH0 by clicking on it and selecting **Format** from the ‘Icons’ menu. Give it a name (e.g. ‘System’, it will be the boot partition) and select **Quick Format**. Carry out the same process for the other partitions, giving each a different name.


### Install Workbench

To install Workbench to the newly formatted boot partition double-click the **Install2.1** disk icon, double-click the **Install2.1** drawer icon and double-click on the icon for your chosen language.

Select **Intermediate** and click on **Proceed with Install**

Click through the following dialog boxes until the installer states which partition it intends to install Workbench to. If this is not the DH0/System (bootable) partition then change it.

Click through the remaining dialog boxes without changing anything until ‘Installation Complete!’ is displayed. Swap the Workbench 2.1 disk images in the WinUAE settings as required during the process.

To ensure Workbench is installed correctly, enter the WinUAE settings, eject all the floppy images and reset. Workbench should load almost instantly.


### Running on the Amiga

If you’ve installed Workbench directly on an SD/CF card, mounted in WinUAE, it can be directly used in the Amiga.

If you’ve installed Workbench on an HDF file this needs to be flashed to an SD/CF card.

HDFs can be flashed with dd under Linux. HDFs are raw images and there are various tools under Windows which can flash these.

To flash the image under Linux run the following command where ‘workbench.hdf’ is the name of the HDF file and `/dev/sdf` is the SD/CF card:

`dd if=workbench.img of=/dev/sdf status=progress bs=4M`

### Backing up the SD/CF Card

To backup work done in WinUAE using an HDF file it’s just a matter of making a copy of it after any significant changes.

When a backup is taken of a real SD/CF card it’s important it can be reflashed to other SD/CF cards of the same (notional) capacity.

Assuming a 4GB SD/CF card with partitions totalling approx 3GB, this can be achieved with the following calculation:

* 4GB = 4294967296 bytes
* 4294967296 bytes / 512 = 8388608 (512 is the `dd` default block size)
* 7/8 or 0.875 * 8388608 = 7340032 (3.5GB worth of blocks)

Around 3GB of the card is partitioned. Telling dd to copy approx 3.5GB will provide a large safety margin while ensuring the resulting image is writable to any 4GB card. By noting down the exact size of the partitions (use `lsblk -b`) it’s possible to be more precise and save a bit of space.

### Setting up the Musashi Emulator

These steps cover setting up Musashi, one of the two Motorola 68000-series emulators which can be used to run the PiStorm. Unlike the alternative, Emu68, it runs on top of a full Linux OS (Raspberry Pi OS). This has advantages and disadvantages. To avoid crashes on reset, it also needs a particular firmware to be flashed to the PiStorm.

> Even if you only plan on using Emu68, it's worth going as far as building and running `buptest.sh` which will tell you whether the PiStorm is properly attached to the Amiga 600 mainboard and the CLPD is properly flashed.

Download the Raspberry Pi OS Buster Lite image from [here](https://www.raspberrypi.com/software/operating-systems/#raspberry-pi-os-legacy). The [main PiStorm GitHub page](https://github.com/captain-amygdala/pistorm) includes specific instructions for using Raspberry Pi Bullseye but doing so may fail when it comes to flashing the CLPD built into the PiStorm. The easiest solution is just to use the older, Buster version of the OS.

> The problem with Bullseye is that it includes a newer version of openocd which will not flash at least some of the CLPDs which may be used in the PiStorm (it can be built with any of several). One solution is to install the older, working version of openocd in Bullseye (0.10.0) but it's easier to simply use Buster while it remains easily available.

Flash the image to to the SD card. Rememeber that if using Linux/dd, you will need to extract the .xz archive and `unxz` will delete the original archive unless it's passed the `-k` option.

Using a relatively small card (4GB or 8GB) will enable relatively small backups which can be flashed to larger cards where necessary.

Add the following to the root of the bootfs partition:

* An empty file called ‘ssh’
* A ‘userconf.txt’ file with the following on a single line:

`pistorm:$6$jfIb53JooH7fOOKQ$MagIkaxf/2Aa2GicYgtuS1gUp2mOoKiKQNIbzG2MJ3DYPYAI6/XGp48VuPy.zm5eW3BijiIZDuQrGtJL58qWB1`

* A ‘wpa_supplicant.conf’ file with the following content (replace X's with wifi network name and password):

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev

country=GB

update_config=1

network={

 ssid="XXXXXX"

 psk="XXXXXX"

}
```

Boot up the Raspberry Pi and log in with ssh. The first boot will take some time. The Raspberry Pi’s IP address can be seen with:

`sudo nmap -sn 192.168.0.1/24`

The CIDR block may vary according to the IP range of devices on the LAN.

On the Raspberry Pi, run:

`sudo apt install git libsdl2-dev openocd libdrm-dev libegl1-mesa-dev libgles2-mesa-dev libgbm-dev libasound2-dev`

To test the GPIO pins, you can run the following. Ensure nothing is attached to the pins. The output should include `Failed user gpios: None`.

```
sudo apt -y install pigpio
wget http://abyz.me.uk/rpi/pigpio/code/gpiotest.zip
unzip gpiotest.zip
sudo pigpiod
./gpiotest
```

Kill the pigpiod process so it doesn't delay shutdown.

`sudo killall -9 pigpiod`

Clone the main PiStorm repository.

```
git clone https://github.com/captain-amygdala/pistorm.git
cd pistorm
make
```

> If you need to re-compile the emulator for any reason enter the pistorm directory and run
>
> `make clean`
>
> `make`

Clone the LemaruX PiStorm firmwares, which resolve an issue with the Mushashi emulator crashing/hanging on a soft reboot. See the [PiStorm600 repo](https://github.com/LemaruX/PiStorm600) for more information.

`git clone https://github.com/LemaruX/PiStorm-Firmware`

Either shutdown or turn off the Pi. 

Connect the PiStorm to the Raspberry Pi, being careful to ensure it is the right way round.

At this stage the PiStorm/Pi assembly can either be powered using the micro-USB port on the Pi or by installing it on the Amiga mainboard. It **must not** be powered using both simultaneously. Because subsequent steps require attachment to the Amiga, it's probably easiest to to this now. See the instructions [here](https://github.com/LemaruX/PiStorm600) for attaching the PiStorm to the Amiga mainboard.

Power on the Pi. Musashi on the Amiga 600 version of the PiStorm needs a particular firmware (PiStormX) to run without crashes on reboot. Once booted up and logged in with ssh, run:

```
cd PiStorm-Firmware
./flash_PiStormX_Basic.sh
```

Now test that that the PiStorm is properly attached to the Amiga:

```
cd ~/pistorm
chmod +x ./build_buptest.sh
./build_buptest.sh
sudo ./buptest
```

You should see the following output:

```
Writing garbege datas.
Reading back garbege datas, read8()...
read8 errors total: 0.
Reading back garbege datas, read16(), even addresses...
read16 even errors total: 0.
Reading back garbege datas, read16(), odd addresses...
read16 odd errors total: 0.
Reading back garbege datas, read32(), even addresses...
read32 even errors total: 0.
Reading back garbege datas, read32(), odd addresses...
read32 odd errors total: 0.
Clearing 512 KB of Chip again
[WORD] Writing garbege datas to Chip, unaligned...
Reading back garbege datas, read16(), odd addresses...
read16 odd errors total: 0.
Clearing 512 KB of Chip again
[LONG] Writing garbege datas to Chip, unaligned...
Reading back garbege datas, read32(), odd addresses...
read32 odd errors total: 0.
```

> 'unaligned' in this context refers to a feature of the 68020 whereby it can read
and write values comprising multiple bytes without these being aligned to an even
address. It's not referring to the alingment of the physical chip.

If, instead, you receive read and write errors, turn off the Amiga's power supply, remove the PiStorm (note the instruction [here](https://github.com/LemaruX/PiStorm600) to lift it from one side first) and re-attach it, being sure to press down vertically rather than one side first. The PiStorm can be fully pressed down and flush with the mainboard and still throw errors. Re-attaching it will often fix this, even though there appears to be no difference in the quality of the fit.

Test the emulator by running:

```
sudo ./emulator
```

#### Autostart the Emulator

With the Raspberry Pi powered on and connected via ssh, enter a superuser command prompt:

```
sudo -s
cd /lib/systemd/system
nano ./pistorm.service
```

Paste the following into the new file, save and exit (`Ctrl-X`):

```
[Unit]
Description=Start piStorm 68k emulator
After=local-fs.target

[Service]
ExecStart=/home/pi/start-emulator.sh

[Install]
WantedBy=local-fs.target
```

Then:

```
cd /home/pi
nano start-emulator.sh
```

Paste in the following:

```
#!/bin/sh
cd /home/pi/pistorm/
sudo ./emulator
exit 0
```

Save and exit. 

Make the startup script executable and set it to start automatically:

```
chmod 755 /home/pi/start-emulator.sh
systemctl daemon-reload
systemctl enable pistorm.service
systemctl restart pistorm
```

#### Backup the Pi SD card

With everything working, this is a good point to backup the Musashi SD card. Assuming you used a 4GB/8GB card it can now be backed up using dd and then reflashed to a bigger card. By expanding the rootfs partition, or adding a new partition, on the bigger card there'll be plenty of space for HDF files.

> This is also a good point at which to screw down the PiStorm onto the mainboard, if you haven't already. Make sure to run the emulator or `buptest.sh` after screwing down the PiStorm to ensure it wasn't moved out of place during the process. 


#### Addiing a custom configuration




### Removed

Workbench, Install and Storage images from Workbench 3.x set

PFS3 All-in-One filesystem ([https://aminet.net/package/disk/misc/pfs3aio](https://aminet.net/package/disk/misc/pfs3aio))

Select ‘Add hardfile/archive’ and select the pfs3aio.lha file. Give it the volume name ‘f’.

Check the ‘Advanced Options’ checkbox and click on ‘Add/Update’. In the new window, select ‘Add New File System’

When asked for the filename we need to point HDToolbox to the pfs3aio file in the mounted LHA volume. If you labelled this volume as ‘f’ then you would enter:

f:pfs3aio

When asked for a DosType or Identifier for the filesystem, enter:

0x50465303

Set the version of the filesystem to v19, revision 2.


### Check

What size disk images can it accept via the accelerator?

Why can’t I write 4GB to SD card?


### Software

ClassicWB


### Case Installation

Determine how to attach the mainboard to the internal panel.



* One screw
* Several adhesive mounts
* _What stops the board falling at an angle._

USB 2.0 hub, with the following soldered to it:



* USB port, front panel 1
* USB port, front panel 2
* SD card reader

Remove USB 3.0 port

Consider removing power button.



* _Can this be used for another USB port?_

Mount for IDE cable/SD card reader.

Floppy power splitter cable

Connect case LEDs to Amiga LED connector



* Check voltage

Gotek modifications



* OLED
* Encoder
* _Are the buttons required if the encoder is installed_

5.25in front panel:



* OLED screen
* Encoder
* Gotek USB port (could be via extender, card reader, or case card reader)

SD card extender for mainboard SDcard/IDE port.


## References

https://github.com/captain-amygdala/pistorm
https://github.com/LemaruX/PiStorm600

https://tech.webit.nu/pistorm-getting-started/
