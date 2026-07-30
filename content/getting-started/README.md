# Getting Started with the Qidi Q2 / Q2C

> This guide picks up after first boot — printer is online, connected to your network, and accessible via Fluidd.

> **New to SSH?** Most steps in this guide require terminal access to your printer. If you haven't set that up yet, start here: [Connecting to Your Printer via SSH](../ssh-guide/README.md)

> **Q2C Note:** The Q2 and Q2C are nearly identical in hardware and software. Differences are noted inline where they exist. The only hardware difference is the Q2C does not include a chamber heater.

---

## 1. Add `screws_tilt_adjust` to printer.cfg

The stock printer.cfg defines screw positions early in the file but is **missing the `[screws_tilt_adjust]` section entirely**. Without it, Klipper's assisted bed leveling won't work.

Add the following block to the **end of printer.cfg**, just before the `#*# <---------------------- SAVE_CONFIG ---------------------->` section:

```ini
[screws_tilt_adjust]
horizontal_move_z: 10
screw_thread: CW-M4
speed: 50

screw1: 30,30
screw1_name: front left
screw2: 240,30
screw2_name: front right
screw3: 240,240
screw3_name: rear right
screw4: 30,240
screw4_name: rear left
```

After saving, do a firmware restart. You can now run `SCREWS_TILT_CALCULATE` from the Fluidd console to get turn-by-turn adjustment instructions for each bed screw.

---

## 2. Bed Leveling Sequence

Getting a flat, properly trammed bed takes a few steps done in the right order. Skipping steps or doing them out of order wastes time.

**Recommended order:**

1. **Z Tilt Adjust** — Run `Z_TILT_ADJUST` from the Fluidd console. This corrects any tilt between the two Z lead screws using the load cell probe.

2. **Platform Calibration** — Run this from the Qidi menu on the touchscreen. This is Qidi's built-in bed calibration routine.

3. **Manual bed screw adjustment if needed** — If the bed is significantly crooked, loosen the bed screws and physically reposition the bed so it doesn't contact the frame during travel, then repeat from step 1. See: [Bed Tramming with Nylok Nuts](../bed-tramming/README.md)

> **Tip:** `SCREWS_TILT_CALCULATE` (added in step 1 above) gives you clockwise/counterclockwise turn amounts per screw corner. Use it before and after manual adjustments to verify.

---

## 3. Disable Unused System Processes

The Q2 runs several background services that have no function during normal printing. Disabling them frees memory and can make the Fluidd interface feel more responsive.

See [Bluedrool's process cleanup guide](https://github.com/bluedrool/Qidi-Q2-tuning-tweaks-and-mods/blob/main/docs/processes.md) for the full list and commands.

> **⚠️ Qidi Link App Users:** If you use the Qidi Link mobile app, **do not disable** `algo_app`, `QIDILink-client`, `xl2tpd`, `strongswan`, or `openvpn`. These services are likely required for app connectivity. Disable only what you're confident you don't need.

> **Q2C Note:** Most services on that list are already absent on the Q2C due to its newer firmware image (board revision dated May 2025). Only `pulseaudio` and `bluetoothd` have been confirmed still running — address those with:
>
> ```bash
> sudo systemctl disable --now bluetooth
> systemctl --user mask pulseaudio.service
> systemctl --user mask pulseaudio.socket
> ```
>
> That said, **it's still worth going through the full list yourself** using `htop` or `ps aux`. Firmware varies and something may have slipped through. If you find anything still running that isn't on the confirmed list, please share your findings in the Discord — this data helps keep this guide accurate for everyone.
>
> After a reboot, a clean Q2C idles at approximately **132MB used (~29% of 512MB RAM)** — significantly lower than a Q2 at the same point (~50%+). This headroom matters if you plan to run Spoolman, KlipperScreen, or other addons.

---

## Next Steps

Once your bed is trammed and your config is clean, good next steps are:

- [Randomizing Z Homing Probe Location](../randomize-probing/README.md) — reduces wear on one spot of the bed surface
- [Displaying Toolhead and Host CPU Temperatures](../temperature-monitoring/README.md) — useful for monitoring
- [Installing Spoolman](../spoolman-installation/README.md) — filament tracking
