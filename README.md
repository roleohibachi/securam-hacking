# securam-hacking
I have a safe. I want it to be less safe.

## The components

### External

- Keypad. The keypad has 12 buttons on the front - 0-9, #, and \*. It has 11 pins in back, connected with a 12-pin header. It is NOT a matrix keypad! When a button is pressed, the corresponding pin is pulled down to ground. # and \* are not populated with contacts at all.
  - Pinout: From left to right, pins correspond to buttons 9, 0, 8, 7, 6, 4, 3, 2, 1, 5, (N/C), GND.
- Board. The board contains only analog components. A diode and voltage regulator give 3.3V from the 9V battery. A 3K resistor ladder is connected to the keypad, such that pressing any button gives a unique analog voltage between 0V and 0.5V. A four-pin header exposes this voltage to an internal ADC (presumably). The pins on this header are labelled, deceptively, SCL, SDA, VH, GND. SCL is the analog output, SDA is a pulse to indicate when a key is first pressed, VH is direct from the 9V battery, and GND is GND.

| Pin | Button | "SCL" |
|-----|--------|------ |
| 1   | 9      | 0.00  |
| 2   | 0      | 0.06  |
| 3   | 8      | 0.13  |
| 4   | 7      | 0.19  |
| 5   | 6      | 0.24  |
| 6   | 4      | 0.30  |
| 7   | 3      | 0.35  |
| 8   | 2      | 0.41  |
| 9   | 1      | 0.46  |
| 10  | 5      | 0.50  |
| 11  | N/C    |       |
| 12  | GND    |       |

One way or another, I'll need to implement a DAC with a 0.5V dynamic range and resolution of 10. Two quick ways come to mind:
- PWM and a capacitor. This can achieve arbitrary precision with a big-enough capacitor. Might be quicker.
- Resistor ladder, just like they did. Maybe even use theirs, and override the keypad signal?

### Internal

TODO. It's surely just a servo to do the unlocking and an MCU with ADC to do the keypad reading, code storing, resetting, etc.

## Cracking

To build a cracking tool for this safe, I'll simulate the entire external component. Using a 3.3V microcontroller with a DAC, I'll test unlocking the safe with the known code expressed as analog voltages. I'll experiment with how fast I can clock the "SDA" pulses and still successfully unlock the safe. Once I know how fast I can go, I'll write a simple loop to cycle through all 1 million codes at that rate. Back of the envelope: If it tolerates 9600 symbols per second, it'll take about 15 minutes. If they're smart, they'll have made the symbol rate much, much slower!

## Improving

I'd love to add a fingerprint reader. I imagine I'll do that by hardcoding the 6-digit key into the flash of a microcontroller, and replaying it via the same connector when a good fingerprint is scanned. If cracking proves sufficiently challenging (meaning there is a modicum of security to be preserved here), I'll move the fingerprint electronics inside the safe, and route another wire out.
