# Aukey LT-T6

The Aukey LT-T6 is a decent lamp with terrible controls and no smart features.
As sold it has no remote or color controls.
It can not be dimmed at all in color mode and only has 3 brightness levels in white mode.

If you're willing to pull out the control chip and solder 7 wires to the exposed pads you can make it better.
Any microcontroller or dev board will do, if it has 5 pins and can take 5V input power. It does not need to support 5V logic.
I used an esp32-c3-mini. See [https://zi3.dev/](https://zi3.dev/) for details

This is an ESPHome implementation of a better local touch interface and optional smart control

## Touch Controls

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


## Technical Operation

#### Boot
This configuration initalizes all outputs to OFF.
Once the software starts the White output will be OFF and the RGB outputs will be amber (red + green).
Once the software is finsihed booting:
- RGB output will be ON at 50% brightness and runng the "random" effect
- White output will be OFF at 100% brightness

#### Remote Control
This ESPHome configuration defines dimmable RGBW outputs with seperate RGB and W lights,
and publishes the touch sensor state.

Remote and local controls update the same global state, and can be used together.
The lamp does not depend on remote control to operate, but the one-button touch interface only exposes a few controls.

I used HomeAssistant for remote control, but a similar configuration could interact with MQTT or another coordination system.
