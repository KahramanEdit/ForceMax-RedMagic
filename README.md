# ForceMax - RedMagic Fan & Pump Max Locker

A Magisk/KernelSU module for the RedMagic 11 Pro that locks the cooling fan and the liquid cooling pump (AWP1921) to maximum. A 500 ms watchdog prevents conflicts with other fan modules (FanExtreme, etc.).

## Features

- Fan locked to level 5 (23,160 RPM, max)
- Liquid cooling pump locked to speed=90, freq=8 (max)
- 500 ms watchdog → overwrites whatever the system or other modules write
- Root + Magisk/KernelSU required

## Tested on

- RedMagic 11 Pro (NX809J, REDMAGICOS 11.0.19)

## Install

1. Download `force_max.zip`
2. Magisk/KernelSU Manager → Modules → Install from storage
3. Reboot
4. Wait 15 s, check `/data/local/tmp/force_max.log`

## Configuration

Target values live in `/data/adb/modules/force_max/bin/force_max.sh`:

| Variable | Default | Meaning |
|---|---|---|
| TARGET_L | 5 | Fan level (0-5) |
| TARGET_E | 1 | Pump enable |
| TARGET_S | 90 | Pump speed (0-90) |
| TARGET_F | 8 | Pump freq (0-8) |

After editing, reboot or restart the watchdog:

    pkill -f force_max.sh
    setsid sh /data/adb/modules/force_max/bin/force_max.sh </dev/null >/dev/null 2>&1 &

## Uninstall

Magisk Manager → Modules → Remove → Reboot

## How it works

The script writes to four sysfs/proc nodes every 500 ms:

    /sys/kernel/fan/fan_speed_level          → 5
    /proc/driver/micropump/enable            → 1
    /proc/driver/micropump/speed             → 90
    /proc/driver/micropump/freq              → 8

If any process (system, FanExtreme, or another module) changes one of these values, the watchdog restores it within half a second.

## Warning

Running the pump at maximum continuously shortens its lifespan (typical pump lifetime: 2,000–5,000 hours). Long-term heavy use (1+ year) carries risk. Lower `TARGET_S` and `TARGET_F` if you want a gentler profile (e.g. speed=70, freq=6 still delivers strong cooling but is less stressful).

## License

MIT

## Author

Clenix
MIT License

Copyright (c) 2026 Clenix

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
