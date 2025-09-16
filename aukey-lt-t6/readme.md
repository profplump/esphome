# Aukey LT-T6

This is a decent lamp with terrible controls and no smart features.
As sold it has no remote or smart features and can only run in the following modes:
- White 100%
- White 50%
- RGB color cycle

If you're willing to swap the microprocessor and solder 7 wires in its place you can make it better

### Touch Controls

#### Single-Click
Toggles RGB mode

If the lamp is off or in White mode this turns it on in RGB mode.
If the lamp is already in RGB mode this turns off the lamp.

The last brightness and color settings are restored (but not saved across reboot)

The effect mode is not restored, but is set to "random" anytime RGB mode is activated.
To change this behavior modify the on_turn_on trigger for `id: light_rgb`

#### Double-click

If the lamp is off or in RGB mode this turns it on in White mode.
If the lamp is already in White mode this turns off the lamp.

The last brightness setting is restored (but not saved across reboot)
  
#### Click-hold

In both RGB and White modes, dim 1% every 0.1 seconds (i.e. dim from 100% to 0% over 10 seconds)

Dimming starts from the current brightness setting and always goes down.
To return to a higher brightness dim all the way to off. When next activated the lamp will be at 100% brightness

Effects are disabled durning dimming to provide better feedback and avoid rapid color changes
