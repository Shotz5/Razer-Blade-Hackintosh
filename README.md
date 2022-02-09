# Razer-Blade-Hackintosh
Razer Blade Advanced 2019 RZ09-03017E02 Hackintosh
Running macOS 12.2 (Yes, Monterey. It works beautifully)
Insert the thing that says I don't claim responsibility if I brick your computer as this is for educational purposes only, and not made for every model of every laptop. I personally bricked my BIOS attempting this project, luckily a CMOS reset fixed it.

## SPECS
Computer specs, click on component to skip to component implementation.
|Component|Description|Functionality|
|--|--|--|
|[Display](#display)| 15.6" Built-in 1080p 240Hz Display|Working (Unsure if running in 144Hz or 240Hz, but appears to be high refresh rate)
|[Processor](#processor)|Intel Core i7-9750H 6-Core Hyperthreaded|Functional|
|[Graphics 1](#graphics-1)|Intel UHD 630 iGPU|Functional with heavy WhateverGreen modifications|
|[Graphics 2](#graphics-2)|NVIDIA GeForce RTX 2070 Max-Q|Non-functional (No applicable drivers)|
|[Memory](#memory)|16GB DDR4 2667MHz|Functional|
|[Storage](#storage)|512GB NVMe SSD CA5-8D512|Functional|
|[Comm](#comm)|Intel Wireless AX200 WiFi 6 / BT 5.0|Functional|
|[Webcam](#webcam)|Windows Hello IR Camera|Webcam functional, not Windows Hello (FaceID Hackintosh soon?)|
|[Keyboard](#keyboard)|Built-in Keyboard|Functional|
|Trackpad|Build-in Trackpad|Functional with DSDT edits|
|Display Out|1xHDMI, 1xMiniDP|Non-Functional (Tied to 2070)|
|I/O|3xUSB3, 1xUSBC|USB-3 Functional, USB-C Functional (unknown if TB-3 functional)|
|Audio|Built-in Speakers and Audio Out|Functional|
|Battery|Built-in 80Wh Rechargable|Functional (Battery life yet to be tested)|
|Power Adapter|230W Included Power Adaper|If this didn't work, we'd be screwed|
|Size/Weight|0.78 in / 19.75 mm x 9.25 in / 235 mm x 13.98 in / 355 mm 5.07 lbs / 2.30 kg|Could be patched with a heavy enough brick?|

## NOT WORKING Or Sort of Not Working:

- ~~Airportitlwm.kext - When WiFi is in use (actively transmitting up/down) gives massive screen flickering relative to download or upload speed.~~ Issue was actually related to WhateverGreen.kext, and has been patched with extra DeviceProperty settings in config.plist.
- ~~Sleep - Probably just because I'm stupid and need to patch my DSDT better - Laptop will go to sleep and come back to a backlight with no image. Requires a reboot to get back.~~ Working when using Apple -> Sleep. Not working on clamshell open/close. Fixed by disable dGPU.
- ~~Brightness settings - Cannot change brightness at all, no option, even with the BrightnessKeys.kext~~ Required to add DeviceProperty settings to enable backlight settings.

## BIOS Settings
Remarkably, I managed to get this working with no BIOS flashing. In fact, I would kernel panic when I turned up my DMVT settings. So changes were only required for the few recommended BIOS settings:
### `Advanced`
`CPU Configuration`
| Description | Setting |
| --- | --- |
|Software Guard Extensions (SGX)|Disabled|
|Intel (VMX) Virtualization Technology|Enabled|
|Hyper-Threading|Enabled|
`Power & Performance -> CPU - Power Management Control`
| Description | Setting |
| --- | --- |
|Intel SpeedStep|Enabled|
|Turbo Mode|Enabled|
|Hyper-Threading|Enabled|
`Power & Performance -> CPU - Power Management Control -> CPU Lock Configuration`
| Description | Setting |
| --- | --- |
|CFG Lock|Disabled|
|Overclocking Lock|Disabled|
`Advanced -> Thunderbolt Configuration`
| Description | Setting |
| --- | --- |
|Discrete Thunderbolt Support|Disabled|
Probably can get away with it being turned on with `No Security`, but this is what worked for me.
`Trusted Computing`
| Description | Setting |
| --- | --- |
|Security Device Support|Disabled|
`USB Configuration`
| Description | Setting |
| --- | --- |
|Legacy USB Support|Enabled|

### `Chipset`
| Description | Setting |
| --- | --- |
|Above 4G MMIO BIOS assignment|Enabled|
|Enable USB Charge Function|Disabled|
`System Agent (SA) Configuration`
| Description | Setting |
| --- | --- |
|VT-d|Disabled|
`SATA and RST Configuration`
| Description | Setting |
| --- | --- |
|SATA Controllers|Enabled|
|SATA Mode Selection|ACHI|

### `Security`
`Secure Boot`
| Description | Setting |
| --- | --- |
|Secure Boot|Disabled|

### `Boot`
| Description | Setting |
| --- | --- |
|Fast Boot|Disabled|

`CSM Configuration`
| Description | Setting |
| --- | --- |
|CSM Support|Disabled|

Most of these settings can be optional or left unchanged, do what's best for your specific model of laptop. If you're curious what options are best for your processor/model. See [Dortania's Setup Guide for your specific processor.](https://dortania.github.io/OpenCore-Install-Guide/config.plist/)

## Display
Controlled by iGPU settings, see [Graphics 1](#graphics-1) for steps.

## Processor
Instructions from [Dortania's setup for Laptop Coffee Lake Plus and Comet Lake](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake-plus.html) followed. Settings applied in config.plist.

## Graphics 1
The graphics setup for this computer was remarkably tricky. Most things I tried didn't work, or would result in heavy screen flickering. Every attempt to reflash the BIOS to enable DVMT resulted in a panic log before the CPU could be properly initialized. So, I found a solution that would work without re-flashing the BIOS.

> Using WhateverGreen.kext

Under `PciRoot(0x0)/Pci(0x2,0x0)`, these settings were added to get steady display output:
| Key | Type | Value
| --- | --- | --- |
|`AAPL,ig-platform-id`|Data|0900A53E|
|`device-id`|Data|923E0000|
|`framebuffer-patch-enable`|Data|01000000|
|`framebuffer-stolenmem`|Data|00003001|
|`framebuffer-fbmem`|Data|00009000|
|`igfxfw`|Data|02000000|
|`hda-gfx`|String|onboard-1|
|`AAPL,GfxYTile`|Data|01000000|
And these settings were added to both fix and smooth out the brightness slider:
| Key | Type | Value
| --- | --- | --- |
|`@0,backlight-control`|Data|01000000|
|`applbkl`|Data|01000000|
|`AAPL,backlight-control`|Data|01000000|
|`AAPL00,backlight-control`|Data|01000000|
|`enable-backlight-smoother`|Data|01000000|
|`backlight-smoother-steps`|Data|23000000|
|`backlight-smoother-interval`|Data|07000000|
|`backlight-smoother-threshold`|Data|2C010000|

## Graphics 2
The NVIDIA GeForce RTX 2070 is not compatible with macOS because there are no drivers made for it for macOS. It's best off completely disabling this card using `SSDT-NoHybGfx.aml`.

> Completely disabling this card may also fix some sleep issues you may experience.

## Memory
No modifications required here. Somehow current macOS plays nice with most kinds of RAM.

## Storage
I've heard there are some people that were having trouble with macOS and their included NVMe drive. I didn't have any of these issues... I was using NVMeFix from the beginning of the project, so that could have had a positive effect on my results. As far as this guide is concerned, no modifications are required.

## Comm

### WiFi

This was a little messy. Using `AirportItlwm.kext`, there were occasions when I was running Big Sur when I would boot and the WiFi module wasn't detecting properly. I have since updated the computer to Monterey and swapped it out for the macOS Monterey version of the module, and haven't found any issues.
Friendly reminder if you're going to use this module you will need to have `SecureBootModel` set to String `Default`.

### Bluetooth

The only issues I had was when I upgraded from Big Sur to Monterey, I needed to swap out `IntelBluetoothInjector.kext` for `BlueToolFixup`, and then we were back in business. I recommend disabling all Bluetooth handlers before updating your OS to Monterey, as I didn't and it hung twice. Once installed you can re-enable the Bluetooth kexts and be on your way.

## Webcam
Worked right out of the box, even the webcam light turns on!

## Keyboard
Obviously, this is a Razer laptop. The keyboard lights up and does all sorts of cool things. The keyboard's brightness settings will work without any modification, but it cannot be customized. The caps lock LED also doesn't work fresh out of install. Thus, the tools `Karabiner-Elements` and `Razer.macOS` exist, to help customize your keyboard.

## Trackpad