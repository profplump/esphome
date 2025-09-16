# Aukey LT-T6

This is a decent lamp with terrible controls and no smart features.
As sold it has no remote or color control.
It couldn't be dimmed at all in color mode and only had 3 brightness levels in white mode

If you're willing to pull out the microprocessor and solder 7 wires to its pads you can make it better.
Any microprocessor or dev board would do, if it has 5 pins and could take 5V input power. I used an esp32-c3-mini.
Here's an implementation for a better local touch interface and smart control via HomeAssisant

### Touch Controls

#### Single-Click
Toggles RGB mode

If the lamp is off or in White mode this turns it on in RGB mode.
If the lamp is already in RGB mode this turns off the lamp.

The last brightness and color settings are restored (but not saved across reboot)

The effect mode is not restored, but is set to "random" anytime RGB mode is activated.
To change this behavior modify the `on_turn_on` trigger for `id: light_rgb`

#### Double-click

If the lamp is off or in RGB mode this turns it on in White mode.
If the lamp is already in White mode this turns off the lamp.

The last brightness setting is restored (but not saved across reboot)

#### Click-hold

In both RGB and White modes, dim 1% every 0.1 seconds (i.e. dim from 100% to 0% over 10 seconds)

Dimming starts from the current brightness setting and always goes down.
To return to a higher brightness dim all the way to off. When next activated the lamp will be at 100% brightness

Effects are disabled durning dimming to provide better feedback and avoid rapid color changes
