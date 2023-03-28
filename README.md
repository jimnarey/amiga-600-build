# Amiga 600 Build Notes

## Notes

ClassicWB is available for the stock 68000 but all versions require min 2MB RAM. The native IDE drive therefore needs a basic WB installation.

To use FS-UAE for provisioning real disks (flash cards) for the Amiga, the path to the hard disk(s) in the configuration can be set to block devices. Permissions might be an issue, in which case run

`sudo chmod 777 /dev/sdf`

## Setting up native HD

### Introduction

The purpose of this set of steps is to install a version of Workbench, which can run on an unmodified Amiga 600, on an SD/CF card which is attached - via an adapter - to the Amiga’s internal IDE connector.

This allows the Amiga to be run without the accelerator installed to check that everything is working normally and as a default, basic setup. It therefore keeps things as simple as possible, without any of the multitude of addons which can make Workbench more sophisticated.

### Requirements

* WinUAE running under Windows (native or VM) or under Linux/WINE.
* A Kickstart 2.05 ROM
* Full set of Workbench 2.1 disk images (Install, Workbench, Locale, Extras, Fonts)
* A 4GB SD/CF card

> It may be possible to use FS-UAE in place of WinUAE but it lacks many of the latter’s low-level options.

This process will save changes to at least the Install disk image, maybe others. Make sure you have backups before proceeding.


### Disk Selection

Without special hardware and drivers for it the A600 can use only one IDE device at a time.

The maximum partition size the A600 can see using the filesystem built into the Kickstart ROM (FastFileSystem) is 4GB, with a total disk size of 8GB. It’s recommended in several places to keep partitions under 2GB and [here](http://wiki.classicamiga.com/Installing_a_large_Harddrive_%284GB_or_larger%29#:~:text=The%20Amiga's%20FFS%20file%20system,total%20HD%20size%20of%208GB.) to keep the boot partition under 2GB. The boot partition must be within the first 4GB.

These instructions assume use of a 4GB card. If using the HDF method, outlined below, in theory you should be able to use a larger card as long as the HDF file flashed to it is &lt;= 4GB.


### Real Disk vs HDF File

Many guides suggest mounting a real SD/CF card in WinUAE, provisioning it and transferring it to the Amiga.

Overall, it’s much easier to do all the significant work on the drive (e.g. provisioning, installing WHDLoad/games, adding software) under WinUAE using an HDF file and re-flashing it to an SD/CF card after each round of work.

If running WinUAE under Linux/WINE this is the only option as WinUAE will not recognise either empty disks or Amiga partitioned/formatted (RDB) disks attached to the host machine.

Whether using an HDF file or working directly with a real disk, it’s best to keep the total size of all partitions a healthy margin under 4GB. Different 4GB cards from different manufacturers/product lines have different capacities, generally somewhere around 3.7GB.

An HDF file well under 4GB can be safely flashed to any 4GB card.

A real SD/CF card with partitions totalling well under 4GB can be backed up using dd and the resulting image flashed to any 4GB card. This applies whether the real drive was provisioned directly or was itself flashed from an HDF file.

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

Under ‘CD & Hard drives’ click ‘Add Hardfile’ and create a new HDF file by entering a value for the size and clicking ‘Create’.

Creating a 3072MB/3GB HDF file provides more than enough space and keeps things simple when backing up/restoring.

Ensure the ‘Full drive/RDB mode’ checkbox is selected.


### Partition the Disk

If re-doing this process, or repeating it with a new HDF file/SD/CF, it’s best to start with a fresh copy of the Install floppy image. It saves information about the disks it sees and this can cause confusion when working with what is already a less than user-friendly interface.

Click ‘Start’ in WinUAE.

In Workbench double click on the Install disk, the HD Tools folder and click once on HD Toolbox. From the ‘Icons’ menu select ‘Information’ and click ‘New’ under ‘Tool Types’.

Enter:

`SCSI_DEVICE_NAME=uaehf.device`

Note that if somehow you end up using this Install disk image in a real Amiga, this will have to be changed (e.g. `SCSI_DEVICE_NAME=scsi.device`) or removed for HDToolbox to work.

Run HDToolbox.

Select the SCSI disk and click ‘Change Drive Type’.

In the ‘Set Drive Type’ window select ‘SCSI’ as ‘Drive Type’ and click on ‘Define new drive type’.

In the box which appears, click on ‘Read Configuration’. Once this is complete, click on OK.

Select the newly defined disk in the ‘Set Drive Type’ window when it reappears and click on ‘OK’.

Back in the main HDToolbox window, with the SD/CF drive selected, click on ‘Save Changes to Drive’.

With the drive selected, click on ‘Partition Drive’

Choose the desired partition layout:

* Note that HDToolbox may throw a ‘Not a DOS disk’ error if attempting to later format a partition greater than 1GB when using an HDF file.
* If using a real SD/CF card then keep the total size of all partitions to approx 3GB
* If using an HDF file then the total available size should already be 3GB, assuming it was created as suggested above.

Whatever the chosen configuration, make the first partition bootable and the others not bootable. Label them DH0, DH1, etc where DH0 is the bootable partition.

Once the partitions have been defined, click OK.

Select ‘Save Changes to Drive’ and click ‘Exit’. Reboot the system if asked to do so. If the newly partitioned DH0 and DH1 do not appear in the main Workbench window then reboot the emulator.

If using an HDF file and the partitions still do not appear following a reboot, ensure that the ‘Full drive/RDB mode’ checkbox is selected in the HDF file’s properties dialog for the HDF file.


### Format the Partitions

Format DH0 by clicking on it and selecting ‘Format’ from the ‘Icons’ menu. Give it a name (e.g. ‘System’, it will be the boot partition) and select ‘Quick Format’. Carry out the same process for the other partitions, giving each a different name.


### Install Workbench

To install Workbench to the newly formatted boot partition double-click the Install2.1 disk, double-click the Install2.1 directory and double-click ‘English’.

Select ‘Intermediate’ and click on ‘Proceed with Install’

Click through the following dialog boxes until the installer states which partition it intends to install Workbench to. If this is not the DH0/System partition then change it.

Click through the remaining dialog boxes without changing anything until ‘Installation Complete!’ is displayed. Swap the Workbench 2.1 disk images in the WinUAE settings as required during the process.

To ensure Workbench is installed correctly, enter the WinUAE settings, eject all the floppy images and reset. Workbench should load almost instantly.


### Running on the Amiga

If you’ve installed Workbench directly on an SD/CF card, mounted in WinUAE, it can be directly used in the Amiga.

If you’ve installed Workbench on an HDF file this needs to be flashed to an SD/CF card.

HDFs can be flashed with dd under Linux. HDFs are raw images and there are various tools under Windows which can flash these.

To flash the image under Linux run the following command where ‘workbench.hdf’ is the name of the HDF file and ‘/dev/sdf’ is the SD/CF card:

`dd if=workbench.img of=/dev/sdf status=progress bs=4M`

### Backing up the SD/CF Card

To backup work done in WinUAE using an HDF file it’s just a matter of making a copy of it after any significant changes.

When a backup is taken of a real SD/CF card it’s important it can be reflashed to other SD/CF cards of the same (notional) capacity.

Assuming a 4GB SD/CF card with partitions totalling approx 3GB, this can be achieved with the following calculation:

* 4GB = 4294967296 bytes
* 4294967296 bytes / 512 (dd default block size) = 8388608
* To backup 3.5GB of raw data, we need 7/8 or 0.875 *  8388608 = 7340032

Assuming dd’s default block size of 512 bytes, there are 8388608 blocks in a 4GB card.

Around 2GB of the card is partitioned. Telling dd to copy approx 3GB will provide a large safety margin while ensuring the resulting image is writable to any 4GB card. By noting down the exact size of the partitions (use `lsblk -b`) it’s possible to be more precise and save a bit of space.

### Install PiStorm

Flash a Raspberry Pi OS, 32bit, Lite image to an SD card.

Using a relatively small card (4GB or 8GB) will enable relatively small backups which can be flashed to larger cards where necessary.

Add the following to the root of the bootfs partition:

* An empty file called ‘ssh’
* A ‘userconf.txt’ file with the following on a single line:

`pistorm:$6$jfIb53JooH7fOOKQ$MagIkaxf/2Aa2GicYgtuS1gUp2mOoKiKQNIbzG2MJ3DYPYAI6/XGp48VuPy.zm5eW3BijiIZDuQrGtJL58qWB1`

* A ‘wpa_supplicant.conf’ file with the following content:

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev

country=GB

update_config=1

network={

 ssid=""

 psk="XXXXXX"

}
```

Boot up the Raspberry Pi and log in with ssh. The first boot will take some time. The Raspberry Pi’s IP address can be seen with:

`sudo nmap -sn 192.168.0.1/24`

The CIDR block may vary according to the IP range of devices on the LAN.

On the Raspberry Pi, run:

`sudo apt install git libsdl2-dev openocd libdrm-dev libegl1-mesa-dev libgles2-mesa-dev libgbm-dev libasound2-dev`


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
