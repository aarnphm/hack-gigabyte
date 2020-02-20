# Hackintosh
Gigabyte Aero 15W with a spice of MacOS Mojave

Full credits goes to [zacmks](https://github.com/zacmks/Hackintosh-Aero-15X) for his/her amazing work. I have some addon to fix some existing problems.

Please see his post on [tonymacx86](https://www.tonymacx86.com/threads/guide-aero-15x-v8-mojave-catalina.287164/) for macOS Mojave. I haven't tested with Catalina yet so I can't guarantee that the files I provided will work. 

# Table of Contents
* [Reasons why I choose Mojave](#reasons-why-i-choose-mojave)
* [Specs](#specs)
* [How to use this repository](#how-to-use-this-repository)
* [Working](#working)
* [Issues](#issues)
    * [Thunderbolt 3 notes](#thunderbolt-3-notes)
* [Patches](#patches)
    * [BIOS](#bios)
    * [CFG-Lock](#cfg-lock)
* [Special thanks to](#special-thanks-to)


## Reasons why I choose Mojave
* Dark mode (YES)
* Eventhough there is no GPU support for Mojave at the moment (Probably not in the forcoming future), the battery is seeming better with Mojave. (Obviously because of no NVIDIA GPU)
* Catalina is still in working in progress since WEG and Lilu needs some fixes. Zacmks's posts on tonymacx86 says that Catalina is working if you tried to upgrade from Mojave. If I was you, I wouldn't do so. I tried myself, not working.

## Specs
```
- Processor: i7-8750H
- Memory: 32GB 2667 MHz DDR4 (Upgraded, originally was 16GB)
- Panel: LCD FHD 144Hz 1920x1080 IPS
- Graphics: Intel UHD Graphics 630 + NVIDIA GeForce GTX 1060 GDDR5 6GB

I/O | Ports:

- 3x USB 3.1 Gen1 (Type-A)
- 1x Thunderbolt™ 3 (USB Type-C)
- 1x HDMI 2.0
- 1x mini DP 1.4
- 1x 3.5mm Headphone/Microphone Combo Jack
- 1x SD Card Reader
- 1x DC-in Jack
- 1x RJ-45
```
_Notes_: I'm ordering a different wifi card that is Broadcom. MacOS just doesn't work with Intel card unfortunately (Mojave, High Seirra used to work). However, it is recommended to get a Broadcom-compatible card for future compatibility. 

## How to use this repository
- I uses OpenCore instead of Clover, so essentially just change everything in your EFI folder with mine. Quicknotes that I have OpenCore **DEBUG** version. 
- Folder structure:
    * `EFI/OC`: Compatible Kext, .efi, APCI patches, etc.
    * `EFI/BOOT`: BOOTx64.efi 
    * `BIOS/`: Some of the bios mod for FB0A (specifically for Aero 15)

## Working
**USB Based Devices**
- [x] All USB ports (2.0 + 3.0)
- [x] Card Reader (3.0)
- [x] HD Camera (2.0)
- [x] Keyboard (2.0)
- [x] Bluetooth (Internal 2.0)

**Network**
- [x] Ethernet card
- [ ] WiFi + Bluetooth

**Power**
- [x] CPU power management
- [x] Battery indicator
- [x] USB PM
- [x] Shutdown/Sleep/Restart
- [x] Saving/Restoring screen brightness on reboot

**Graphics**
- [x] Intel graphics card

**Misc**
- [x] Sound (internal speakers + mic jack on/off)
- [x] Touchpad

## Issues
- [x] Thunderbolt hotplug (does work if plugged in on boot) (refers to below for [BIOS Patches](#patches))
- [x] Blackscreen after boot for 3 minutes (appears on this [post](https://www.tonymacx86.com/threads/bug-black-screen-3-minutes-after-booting-coffeelake-uhd-630.261131/))
    * Added AppleBacklightFixup.kext to L/E with `sudo cp -R ~/Downloads/RehabMan-BacklightFixup*/Release/AppleBacklightFixup.kext /Library/Extensions`. Download the kext [here](https://bitbucket.org/RehabMan/applebacklightfixup/downloads/)

~~Nvidia Graphics card (Only High Sierra with WebDrivers, this will probably one of the downside)~~

### Thunderbolt 3 notes:
- Hotplug can be implemented with unlocked bios (changing the BIOS settings) + SSDT
- In order to fix the blackscreen problem appeared with CoffeeLake processor, we have to use OpenCore Debug version, which is also included in the repository
- One can disable some debug `boot-args`, however, it is nice to see what happens and easier for debugging.

## Patches
### BIOS
**DISCLAIMERS:DO THIS WITH YOUR OWN RISK.YOU CAN DEFINITELY BREAK YOUR LAPTOP. I'M NOT RESPONSIBLE FOR ANYONE BREAKING THEIR MACHINE**

_This patches won't do anything sketch to your laptop, just open more options for the sake of customizing it for OpenCore_

With that out of the way, make sure that your bios is FB0A. You can get it [here](https://www.gigabyte.com/us/Laptop/AERO-15X--i7-8750H/support#support-dl-bios), and flashed it first before doing any modification. I can confirm that everything worked flawlessly with my laptop and this version of BIOS.
For the sake of not ruining your laptop please do all BIOS work on **WINDOWS**
Some of the tools needed for this steps:
* [AMIBCP5](https://www.mediafire.com/file/ckao23pe57ny7jm/AMIBCP_5.02.0031.rar/file)
* [Intel CSME System Tools](https://mega.nz/#!XVkVXAaK!dFW5kcWoCCsxR1cif5EdaQ-c1jKYePDCxKrK9JV_Whw)
* [UEFI Tools](https://github.com/LongSoft/UEFITool/releases/tag/0.26.0)
* [ifrextract](https://github.com/LongSoft/Universal-IFR-Extractor/releases/)

#### Steps
1. Run CMD as Adminstrators and run `FPTw.exe -bios -d biosreg.bin` (FPTw can be found inside `Intel CSME System Tools\Flash Programming Tools`)
2. Run **UEFITools** and open `biosreg.bin`. Search for `AMITSE`, choose `Unicode text` checkbox, then choose `"Unicode text "AMITSE" found in User interface section at offset 0h"`. **CHOOSE THE FIRST CHOICE**, shown in [here](https://i.imgur.com/82E3uSV.jpg), right click on `PEP32 image section` and choose `Replace as is...`. Replace with the included [file here](BIOS/Section_PE32_image_AMITSE_unlocked_bios_settings_FB0A.sct). Then save the image file, called it as `biosmod.bin`.
(You can see the file within BIOS dir)
3. Open **AMIBCP5** and open `biosmod.bin` and set the menu access to USER like [so](https://i.imgur.com/BnkU0RW.jpg)
4. Once save run `FPTw.exe -bios -f biosmod.bin`
    * If you get following error: `Error 167: Protected Range Registers are currently set by BIOS, preventing flash access.`. This is caused by BIOS Lock variable set to 0x01
    #### How to unlocked BIOS
    1. With Gigabyte Aero just reboot into setup, Choose `Delete all secure boot variable` (If you install Hackintosh you should already done this already)
    2. Format a USB stick to FAT32, create directory `EFI\BOOT`, then put BOOTx64.EFI from [here](brains.by/posts/bootx64.7z) into the directory
    3. Boot from this USB stick, run `setup_var 0xA12 0x00` (I would still keep this USB stick around if you want to apply CFG-Lock patches found [here](https://khronokernel.github.io/Opencore-Vanilla-Desktop-Guide/extras/msr-lock.html))
    * Then run the command again.
5. If successful then run `FPTw.exe -greset`
6. Press F2 and voila!
### CFG-Lock
Please follow [this](https://khronokernel.github.io/Opencore-Vanilla-Desktop-Guide/extras/msr-lock.html), but I did on Windows. Same applies.

Follows recommend BIOS settings from OpenCore guide for the file inside this repo to work.

## Special thanks to 
* [zacmks](https://github.com/zacmks) for the working-out-of-the-box config
* [OpenCore](https://khronokernel.github.io/Opencore-Vanilla-Desktop-Guide/) straightforward guidebook
* [RehabMan](https://github.com/RehabMan) for his intuitive guide for DSDT patches
* [acidanthera](https://github.com/acidanthera) for the huge brain himself
* [corpnewt](https://github.com/corpnewt) for his amazing toolbox
* [Lost_N_BIOS](https://www.bios-mods.com/forum/Thread-REQUEST-Unlock-BIOS-for-Gigabyte-Aero-15x-v8?pid=154664#pid154664) for the sick BIOS modding
* [headkaze](https://www.bios-mods.com/forum/Thread-Gigabyte-Aero-15-v8-FB0A-BIOS-Unlocked)for his tutorials, I just basically put it in here for easy access
