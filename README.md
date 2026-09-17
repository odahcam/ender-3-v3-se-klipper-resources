# Ender 3 v3 SE Klipper

This is my repository of configurations that work with my Ender 3D printer. Mine is the first version motherboard, so keep that in mind.

I learnt how to install Klipper by following this:
- How-to install article: https://athemis.me/projects/klipper_guide 
- YouTube video for custom Klipper builds, necessary for things like Auto Z Offset: https://youtu.be/2bOx0buOJaM?si=oqvXlv7VKYWRy902

I'm new to Klipper but I've already made a few improvements to my configuration and I will keep updating this in the future so I can come back here in case anything happens with my configuration or anyone else wants to leverage these files (at your own responsibility and risk) for their printer.

I've invested many hours into making the printer work and I will probably invest many more with the upgrades I have planned, so I hope this becomes a place where I can always rely on a working config in case I need one.

## Mods

3D models for the mods I add to my print will also be available here as well as links to buy parts and also instructions when plausible.

## Extra Tips

Based on my experience with Ender 3 in Marlin and a Bambu Lab A1, I have some idea of what makes a good difference and what's not.

- **Auto Z Offset**: it is a must, you have to setup this if you want to use the full potential of the Ender 3 v3. Otherwise you're just loosing an awesome feature and waisting your time with confusing manual probe calibration using paper.
- **Original LCD screen with Klipper**: I think this is useless and I intend to use Creality's cable for the Nebula display to connect my Raspberry and do the infamous LCD delete.
- **Extra sensosrs**: XYZ sensor for input shaping and Filament runout sensor are a really worth upgrade.
- **Bed mashing/levelling**: Ender 3's have a scewed X axis and you shall be able to fix it searching online. It will not prevent you from printing though, and is my lowest priority in the list of items to fix. I rather prefer upgrading the axis with rails first.

## CR Touch: "BLTouch failed to deploy"

If homing fails with `BLTouch failed to deploy`, clean the pin before suspecting anything
else. In my case the sensor failed 14 of 15 homing attempts and went back to 15 of 15 after
this, with no other change:

1. Clean the photointerrupter (the black U-shaped part on the sensor board) with isopropyl
   alcohol on a cotton swab, both inner faces of the slot. Blow it dry and check no cotton
   fibres stayed in the slot. Do not use contact cleaner here, it leaves a film on the optics.
2. Extend the pin, spray contact cleaner on it, and release it so the pin carries the fluid
   inside the body.
3. Work the pin up and down by hand, several times, to spread the fluid along its travel.
4. Repeat 2 and 3 a few times.

The failure is intermittent, which sends you chasing the wrong things. I lost a day on the
mains outlet, the power strip, a smart plug and the solenoid before testing the pin contact.
Run homing 15 times to judge a fix: a single pass means nothing, and a marginal contact still
succeeds once in a while.

The LED helps: steady means healthy, blinking red means the sensor is in alarm.

## Host setup (Raspberry Pi 3B)

`dwc_otg.speed=1` must be in `/boot/firmware/cmdline.txt`. It is not in this repo because
`cmdline.txt` carries the SD card's own `PARTUUID`, so re-add the flag by hand after a reflash.

Without it, prints die after a few hours with `Lost communication with MCU` or `Timer too
close`. The webcam is high-speed (480M) and the CH340 is full-speed (12M) behind the same
LAN9514 hub, so the Pi 3B's `dwc_otg` has to issue split transactions for the serial link and
loses them under the camera's isochronous load. The flag forces the whole bus to full-speed,
which removes split transactions entirely.

Measured with the same 43min movement-only gcode, counting `bytes_retransmit` in `klippy.log`:

| webcam | `dwc_otg` | retransmit |
| --- | --- | --- |
| 640x480@15 | default | 913 |
| 320x240@5 | default | 418 |
| off | default | 0 |
| 320x240@5 | `speed=1` | 0 |
| 640x480@15 | `speed=1` | 0 |

Lowering the camera's resolution only halves the failure rate, so it is not a fix on its own.
Ethernet also drops to 12Mbps, which does not matter here because the Pi is on WiFi.

## Firmware builds

I'm adding `.bin` files that works in my printer to the repo so I don't have to build them again. They can be helpful in case it is not possible to build them in the future anymore.

## Roadmap

- [X] Add webcam (Logitech C170).
- [X] Side mounted spool (for stability) with bowden tubes and guides.
- [X] Printer enclosure (using Sunlu).
- [X] Install Klipper.
- [ ] Add XYZ sensor.
- [ ] Add Filament runout sensor.
- [ ] Add Creality LED light bar.
- [ ] LCD delete.
- [ ] Rails upgrade.
