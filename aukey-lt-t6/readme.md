# Aukey LT-T6

This is a decent lamp with terrible controls and no smart features.
As sold it has no remote or color control.
It couldn't be dimmed at all in color mode and only had 3 brightness levels in white mode

If you're willing to pull out the control chip and solder 7 wires to its pads you can make it better.
Any microcontroller or dev board would do, if it has 5 pins and could take 5V input power.
It does not need to support 5V logic. I used an esp32-c3-mini. See [https://zi3.dev/](https://zi3.dev/) for details

This is an esphome implementation of a better local touch interface and optional smart control via HomeAssistant

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


## Operation

#### Boot
This configuration initalizes all outputs to off.
On many microcontrollers bootsrap pins may alter this initialization before the software starts.
If the LED outputs are connected to these pins the lamp may light during this time.
Once the software starts the White output will be off and the RGB output will be amber (red + green).
Once the software finishes booting the RGB output will be active at 50% brightness with the "random" effect.
At boot the White brightness is set to 100% and OFF.

#### Remote Control
This ESPHome configuration defines dimmable RGBW outputs with seperate RGB and W lights,
and publishes the touch sensor state.

Remote and local control update the same global state, and can be used together.

The lamp will run without any remote control configured, but when configured it has more controls available.

I used HomeAssistant for remote control, but a similar configuration could interact with MQTT or another coordination systems.
