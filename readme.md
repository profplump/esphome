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

The [Aukey LT-T6](https://www.amazon.com/foo/dp/B0B3MBWS84/) is a decent lamp with terrible controls and no smart features. 
As sold it has no remote or color controls. It can not be dimmed at all in color mode and only has 3 brightness levels in 
white mode

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

The [Elegrp-RRR00](https://www.elegrp.com/products/wiring-devices/smart-home/smart-in-wall-outlets/1088) is a 
power-monitoring dual-relay US outlet with a CBU board. These are cheap and have 16A relays and a fast bl0942 power monitor. 
They have 2 buttons, 3 LEDs, and are built well enough that I am not afraid they will burst into flame

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

The [Wyse Outdoor Plug](https://www.wyze.com/products/wyze-plug-outdoor) is a power-monitoring dual-relay US outlet with an 
ESP32-wrover board. These are cheap and have 16A realys and a slow hlw8012 power monitor. They have 2 buttons, 3 LEDs, and a 
lux sensor

There is easy access to the 3.3V, Gnd, Tx, and Rx pads on the main board and plenty of room inside for activities. You 
will need a triangle driver to open the case

Example: `outlet-outside.yaml` \
Library: `zi3-outlet-wyze.yaml`

## Shelly Dimmer 2

The [Shelly Dimmer 2](https://us.shelly.com/products/shelly-dimmer-gen4) is an AC dimmer rated up to 1.1A with an ESP8285 
board. It is meant to go into an electrical box. It has 2 high-voltage switch inputs, a status LED, a temperature probe, and 
an SMT co-processor that does the actual AC dimming. It does not require a netural, supports leading and trailing edge 
dimming, power monitoring, and adjustable minimum and maximum power output settings

These directly expose the necessary flash pins on a female header, but you will need to buy or build an adapter for the 
1.27mm pitch of this tiny connector

Example: `light-office.yaml` \
Library: `zi3-dimmer-shelly.yaml`

## Shelly PM1 Gen4

The [Shelly PM1 Gen4](https://us.shelly.com/products/shelly-1pm-gen4) is a power-monitoring single-relay switch with an 
ESP32-c6 board. It is meant to go in an electrical box. It has a 16A relay, a fast bl0942 power monitor, a high-voltage 
switch input, and a tempature probe. It does not require a netural

These directly expose the necessary flash pins on a female header, but you will need to buy or build an adapter for the 
1.27mm pitch of this tiny connector

Example: `outlet-guest-bath.yaml` \
Library: `zi3-switch-shelly.yaml`


## Meikee 25W RGB LED Wall Washer Light

[Meikee 25W RGB LED Wall Washer Light](https://zi3.dev/meikee-25w-rgb-led-wall-washer-light/)

Example: `light-guest-rgb.yaml`

## Vornado Transom Fan

[Vornado Transom Fan](https://zi3.dev/vornado-transom-fan/)

Example: `fan-office.yaml` \
Library: `zi3-fan-transom.yaml`

## AFROG Desk Lamp

[AFROG 2th Gen Multifunctional LED Desk Lamp With Wireless Charger](https://zi3.dev/afrog-desk-lamp/)

Example: `light-guest-bed.yaml`
