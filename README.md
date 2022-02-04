# Razer-Blade-Hackintosh
RZ09-03017E02 Hackintosh (semi-working)

## NOT WORKING Or Sort of Not Working:

- Airportitlwm.kext - When WiFi is in use (actively transmitting up/down) gives massive screen flickering relative to download or upload speed.
- Sleep - Probably just because I'm stupid and need to patch my DSDT better.
- You're gunna love this one - 64MB and Max DMVT settings - If I remove all of the device patches in `PciRoot(0x0)/Pci(0x2,0x0)`, and the `-igfxmlr` boot arg, and reflash my BIOS to give me access to DMVT settings, and then set them properly, I will get a Kernal Panic during boot (unfortunately not a stuck on GPio issue, an actual panic log before I'm in). Says something about processors not responding or something. It was hard to decode because most of the panic log went off the screen.
- Brightness settings - Cannot change brightness at all, no option, even with the BrightnessKeys.kext

I hope to have a working, bug free, build soon. Until then, sit tight <3
