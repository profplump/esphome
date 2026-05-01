# Esphome

This is basically my live esphome config directory. It is not carefully arranged for others to use. But it contains useful 
configurations

Library code is in files that start with `zi3-` \
Board-specific libraries are in files that start with `zi3-board-` \
Library configs are typically meant to be included as `packages:`

Other files are configs for specific devices (or they are just cruft I haven't deleted yet)

Some devices are re-flashed commerical hardware. Some are chip-swapped commerial hardware. Some are custom circuits. Below 
are some that I have documented beyond their YAML config

## Aukey LT-T6

The Aukey LT-T6 is a decent lamp with terrible controls and no smart features. As sold it has no remote or color controls. 
It can not be dimmed at all in color mode and only has 3 brightness levels in white mode

It is easy enough to chip-swap the controller on this board. I used an esp32-c3-mini; anything with 5 pins and a 5V power 
input will work. The Aukey board has low-level PWM RGBW outputs and an active-low touch sensor input. There are also two 
status LEDs but I did not wire these

There's plenty of room inside, but note that the base is all metal so you'll want WiFi antenna as high as possible. I used 
a cabled WiFi antenna stuck to the highest point inside the base. On my unit the pad embedded in the middle of the base 
had to be torn out to be disassmbled; it was soldered in place after assembly. This pad is used for a local ground 
reference for the touch control; if it breaks just reconnect it to a ground point near the touch sensor chip (or let it 
float; touch often works even without this pad)

Example: `light-kennel.yaml`

## Elegrp RRR00

Elegrp-RRR00 is a power-monitoring dual-relay US outlet with a CBU board. These are cheap and have 16A relays and a fast 
bl0942 power monitor. They have 2 buttons, 3 LEDs, and are built well enough that I am not afraid they will burst into 
flame

But they are not perfect. The first flash requires moderately difficult soldering, as the necessary pins are both hard to 
access and labeled incorrectly on the silkscreen. It may be possible to OTA-chain-load from the vendor firmware to esphome, 
but I didn't check

I have also had bad luck with stability on these. Mostly they run fine and I do use several of them, but the Fallback AP 
always makes them crash (so you can't change WiFi via the AP), and they somtimes crash when processing actions. When they 
crash the status light blinks but they never reboot or recover. The relays stay on (or off) but the device doesn't respond 
to local or remote control or re-join WiFi until it is power cycled

Example: `outlet-guest-vanity.yaml` \
Library: `zi3-outlet-elegrp.yaml`

## Wyse Outdoor Plug

Wyse Outdoor Plug is a power-monitoring dual-relay US outlet with an ESP32-wrover board. These are cheap and have 16A 
realys and a slow hlw8012 power monitor. They have 2 buttons, 3 LEDs, and a lux sensor

There is easy access to the 3.3V, Gnd, Tx, and Rx pads on the main board and plenty of room inside for activities. You 
will need a triangle driver to open the case

Example: `outlet-outside.yaml` \
Library: `zi3-outlet-wyze.yaml`

## Shelly Dimmer 2

The Shelly Dimmer 2 is an AC dimmer rated up to 1.1A with an ESP8285 board. It is meant to go into an electrical box. It 
has 2 high-voltage switch inputs, a status LED, a temperature probe, and an SMT co-processor that does the actual AC 
dimming. It does not require a netural, supports leading and trailing edge dimming, power monitoring, and adjustable 
minimum and maximum power output settings

These directly expose the necessary flash pins on a female header, but you will need to buy or build an adapter for the 
1.27mm pitch of this tiny connector

Example: `light-office.yaml` \
Library: `zi3-dimmer-shelly.yaml`

## Shelly PM1 Gen4

The Shelly PM1 Gen 4 is a power-monitoring single-relay switch with an ESP32-c6 board. It is meant to go in an electrical 
box. It has a 16A relay, a fast bl0942 power monitor, a high-voltage switch input, and a tempature probe. It does not 
require a netural

These directly expose the necessary flash pins on a female header, but you will need to buy or build an adapter for the 
1.27mm pitch of this tiny connector

Example: `outlet-guest-bath.yaml` \
Library: `zi3-switch-shelly.yaml`


## Meikee 25W RGB LED Wall Washer Light

The Meikee RGB LED is a weatherproof RGB LED light bar. It includes a 433 MHz radio reciever and an AC zero-crossing 
sensor, in addition the 3 PWM LED outputs. It's easy to hook up to all the internal hardware, the RGB outputs are labeled 
on the board, there's enough physical space to hide a board behind the light guide, and the lamp already runs the control 
chip on an isolated 3.3V supply

Unforunately the low-voltage regulator draws power from the feedback loop of the high-voltage regulator, so there's only 
like 15 mA available &mdash; not enough for WiFi. I drilled a hole in the side of mine to add a USB power cord to the esp32 
inside; I am using this lamp indoors in a hidden location so extra wires are no problem. Less demanding controllers (i.e. 
things without built-in radios) would probably work

The zero-crossing indicator feels like a holdover from when this had an AC dimmer. As sold it was connected to the 
controller but was not synced with the PWM output. Since the LEDs are driven from high-voltage DC there is no reason they 
would be synced to AC. I tested this sensor as a frequency meter/pulse counter but decided not to wire it in the final 
version

The remote from this is pretty good. It sends clean rc_switch codes that all my recievers recognize. The receiver in the 
lamp is a module on the main board with a coiled wire antenna. It is not great but it works better than many cheap 433 MHz 
modules

Example: `light-guest-rgb.yaml`

## Vornado Transom Fan

The [Vornado Transom](https://vornado.com/products/transom-parent) fan is a blower fan designed to mount across the bottom 
of a hung window. I like the fan hardware. It includes an IR remote, runs at 4 discrete speeds, can be reversed 
electronically, closes when off, and includes a thermostatic function. I do not like the UI. It spends several seconds doing 
a light show every time you turn it on &mdash; which you have to wait for before you can change any settings &mdash; and it 
requires multiple state-dependent button pushes on both the remote and local interfaces. It's very difficult to use reliably 
if you cannot see the display panel when changing settings

Luckily it's possible to read and set most of the device state without any significant modification. There are separate 
motor control and UI boards and the connection between them uses a UART link. The IR receiver is easily accessible from the 
back of the UI board. With those two connections it's possible to simulate IR input (and/or read IR ambient signals 
from the sensor) and to determine the fan state, which allows for closed-loop automation

The device includes a thermostatic mode. That mode can be detected from the UART data when armed but not when triggered, nor 
can the setpoint or current or temperature be determined. The thermocouple is connected with long wire and would be simple 
to move to a smarter controller if you wanted to use the fan's existing sensor for a custom thermostatic control

The fan provides 5V power from the motor control board to the UI board, but there's less than 100 mA avaiable and it needs 
to drive the whole UI board in addition to any load you add. The 100 mA limit comes from the MX2003, a slightly unusual chip 
that combines an array of darlington transistors (for driving the fan's baffle motor) with a tiny 5V power supply driven 
from the higher voltage on the motor side of that array. The motor control board generates a higher current 12V supply for 
the baffle motor and I found plenty for room to stick in my own buck converter to provide more 5V current for the ESP32. 
There's a big 47 Ω resistor next to the MX2003 that you can tap for access to the 12V rail

Example: `fan-office.yaml` \
Library: `zi3-fan-transom.yaml`

## AFROG Desk Lamp

The [AFROG 2th Gen Multifunctional LED Desk Lamp With Wireless 
Charger](https://www.afroghome.com/products/afrog-multifunctional-led-desk-lamp-with-wireless-charger) is a (micro) USB-powered desk lamp with touch 
controls for cool and warm light options, fine-grained dimming, a sleep timer, wireless charging and a USB-A power port. It has small green and blue 
LEDs inside the timer touch control, and red and blue LEDs along the right side

The hardware and interface are mostly pretty good. The dimmer control has touch 5 pads and can blend so brightness control is fairly fine-grained. On my 
particular lamp the 4th brightness pad is busted, but I suspect that's an individual failure and not a design problem. The wireless charger is not 
great, but it does work, and I'm told later models include a different charger. The wireless charger is an indepedent circuit board connected only to 
power and the charge coil, so it would be easy to replace if you desired

In total there are 8 touch pads inputs (5 brightness, power, color and timer), cool and warm LED outputs, mutually-exclusive inverted outputs for the 
green and blue LEDs, and red and blue LEDs on (and indendently controlled by) charging board. The main control chip and LEDs all run at 3.3V, and have 
transistor-switched outputs for the main white lamps, so it's easy to drive everything directly. I did not check the 3.3V power rating as there is 
already 5V power from the USB input, which should be sufficient for any controller. There's lots of empty space inside the case (take out 5 screws from 
the metal plate under the adhesive foot pad), and the case itself is translucent black so that LEDs can be seen from inside. I used a Waveshare 
ESP32-S3-Nano as a controller and you can easily see the onboard RGB and red LEDs through the case. Be aware that the metal backing (and charging 
devices on top) can interfere with WiFi and other signals, so you may need careful antenna positioning or an external antenna

Example: `light-guest-bed.yaml`
