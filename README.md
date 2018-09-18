# ACPI Brightness

A small script with minimal requirements for interacting with ACPI backlights
via *sysfs*, intended as a stand-in for `xbacklight` on systems which it does
not support. Interface uses percentages. Supports linear fading between values,
relative brightness changes, and only-decrease and only-increase options.

## Usage

    acpi-brightness
    acpi-brightness [-h|--help]
    acpi-brightness [--time|-t TIME [--block|-b]] [--COND] [+-]TARGET

With no argument, report current brightness as % of maximum.

With `TARGET` %, alter brightness. If `--time` is set, fade over `TIME` seconds.
If `--block` is also specified, subsequent attempts to change brightness will fail
until this invocation is finished. `--force` can be used to ignore previously
`--blocking` invocations.

`COND` is either `inc`[rement] or `dec`[rement] -- if decrement is specified,
but `TARGET` is brighter than the current brightness, there will be no change.

### Flags

* `-h`, `--help` : Display usage instructions
* `-t`, `--time` `TIME` : Fade brightness over `TIME` seconds (positive decimal)
* `-b`, `--block` : With `--time`, prevent other brightness changes until complete
* `-f`, `--force` : Stop previous blocking invocations
* `--COND` : Only change if increasing or decreasing brightness. `COND` is `inc` or `dec`

### Examples

* Get current brightness in %

      $ acpi-brightness 
      100

* Set brightness to 40%

      $ acpi-brightness 40

* Set brightness to 40% unless current brightness is lower than 40%

      $ acpi-brightness --dec 40

* Decrease brightness by 10pp

      $ acpi-brightness -20

* Increase brightness by 20pp over 2.5 seconds

      $ acpi-brightness -t 2.5 +20

* Set brightness to 100% over 2 seocnds, and prevent any brightness changes until
complete

      $ acpi-brightness --block -t 2 100

* Set brightness to 40%, ignoring previous `--block`ing invocations

      $ acpi-brightness -f 40

## Dependencies

* awk

## Installation

* **Add `$USER` to `video` group**

      $ sudo usermod -a -G video $USER

* **Give `video` group write-access to ACPI brightness file**

  The following udev rule will do this at startup:

      SUBSYSTEM=="backlight", RUN+="/usr/bin/chgrp video /sys/class/backlight/%k/brightness", RUN+="/usr/bin/chmod 660 /sys/class/backlight/%k/brightness"

  *e.g.* with the following command:

      $ sudo tee /etc/udev/rules.d/60-acpi-backlight.rules <<< 'SUBSYSTEM=="backlight", RUN+="/usr/bin/chgrp video /sys/class/backlight/%k/brightness", RUN+="/usr/bin/chmod 660 /sys/class/backlight/%k/brightness"'

* **Place `acpi-brightness` on your `$PATH`**
* **Test everything is working**

      $ acpi-brightness 100
      $ acpi-brightness
      100
      $ acpi-brightness 30
      $ acpi-brightness
      30

## Related Projects

Hook for [redshift](https://github.com/jonls/redshift) to decrease brightness
throughout the day: [redshift-hooks](https://github.com/qualiaa/redshift-hooks)

## Licence

`acpi-brightness` is licensed under the GNU General Public Licence v3.0.
