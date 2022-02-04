# Razer-Blade-Hackintosh
RZ09-03017E02 Hackintosh (semi-working)

## NOT WORKING Or Sort of Not Working:

- Airportitlwm.kext - When WiFi is in use (actively transmitting up/down) gives massive screen flickering relative to download or upload speed.
- Sleep - Probably just because I'm stupid and need to patch my DSDT better - Laptop will go to sleep and come back to a backlight with no image. Requires a reboot to get back.
- You're gunna love this one - 64MB and Max DVMT settings - If I remove all of the device patches in `PciRoot(0x0)/Pci(0x2,0x0)`, and the `-igfxmlr` boot arg, and reflash my BIOS to give me access to DMVT settings, and then set them properly, I will get a Kernal Panic during boot (unfortunately not a stuck on GPio issue, an actual panic log before I'm in). Says something about processors not responding or something. It was hard to decode because most of the panic log went off the screen.
- Brightness settings - Cannot change brightness at all, no option, even with the BrightnessKeys.kext

## Summary of Specs (For those who don't wanna Google)
15.6" LCD 240Hz Display (1920x1080)
Intel Core i7-9750H
Intel UHD 630 iGPU
GeForce RTX 2070 Max-Q (-wegnogpu command used)
Everything else doesn't much matter I suppose, I already have the trackpad working, the front facing camera works, battery statistics are shown properly, etc etc.

I hope to have a working, bug free, build soon. Until then, sit tight <3
