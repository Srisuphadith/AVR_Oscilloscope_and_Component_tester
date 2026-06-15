# AVR Oscilloscope and Component Tester

An ATmega328P-based embedded systems project that turns an AVR microcontroller into a small digital oscilloscope and V-I curve component tester. The firmware samples analog signals with the built-in ADC, renders waveforms on a 128x64 SSD1306 OLED display, and uses a rotary encoder to adjust measurement settings.

This project was developed for the CPE324 Embedded System course.

## Features

- Oscilloscope mode for plotting ADC waveforms on an OLED display
- Component tester mode for drawing a V-I characteristic curve
- Rotary encoder control for adjusting horizontal and vertical scale
- Push-button mode switching with buzzer feedback
- OLED graphics support, including pixels, lines, and simple text
- Reusable AVR modules for GPIO, ADC, TWI/I2C, LCD, keypad, ultrasonic sensor, DS1307 RTC, MCP3201 ADC, and delay utilities
- AVR-GCC build scripts and USBasp flashing scripts

## Hardware

The main firmware targets:

- ATmega328P running at 8 MHz
- 128x64 SSD1306 OLED display over I2C
- Rotary encoder with push button
- USBasp programmer
- Analog input signal for oscilloscope mode
- External component test circuit connected to ADC inputs and PORTB DAC-style output pins
- Optional LED and buzzer indicators

> Important: ATmega328P ADC pins are not 5 V tolerant above VCC. Use proper input protection, voltage dividers, and current limiting before connecting external signals.

## Pin Overview

| Function | ATmega328P pin |
| --- | --- |
| Oscilloscope analog input | ADC2 / PC2 |
| Component tester voltage read | ADC1 / PC1 |
| Component tester current read | ADC0 / PC0 |
| OLED SDA | PC4 |
| OLED SCL | PC5 |
| Rotary encoder DT | PD2 / INT0 |
| Rotary encoder CLK | PD3 / INT1 |
| Rotary encoder switch | PD4 / PCINT20 |
| Buzzer | PD5 |
| Mode/status LED | PD6 |
| DAC-style component test output | PORTB / PB0-PB7 |

The repository also includes an ATmega328P pin reference image:

![ATmega328P pinout](1_atmega328pPin.png)

Image attribution: [ATMEGA328P-PU.jpg](https://commons.wikimedia.org/wiki/File:ATMEGA328P-PU.jpg) by [oomlout](https://www.flickr.com/people/oomlout/) is licensed under [CC BY-SA 2.0](https://creativecommons.org/licenses/by-sa/2.0/).

## Repository Structure

```text
.
|-- oscilloscope.c          # Main oscilloscope and component tester firmware
|-- rotary.c                # Rotary encoder experiment/demo
|-- timer.c                 # Timer interrupt experiment/demo
|-- include/                # Header files for reusable AVR modules
|-- src/                    # Module implementations
|-- lib/                    # Static AVR library and library build helper
|-- sh/                     # Compile, upload, and fuse scripts
|-- example/                # Small example programs for modules and peripherals
|-- doc/                    # Module usage notes
`-- 1_atmega328pPin.png     # ATmega328P pinout image
```

## Prerequisites

Install the AVR toolchain before building:

- `avr-gcc`
- `avr-libc`
- `avr-binutils`
- `avrdude`
- USBasp driver/support for your operating system

On Debian/Ubuntu-based systems:

```sh
sudo apt update
sudo apt install gcc-avr avr-libc binutils-avr avrdude
```

On macOS with Homebrew:

```sh
brew tap osx-cross/avr
brew install avr-gcc avrdude
```

## Build and Flash

Make the scripts executable:

```sh
chmod +x go.sh sh/*.sh lib/*.sh
```

Compile the main firmware:

```sh
./sh/compile.sh oscilloscope
```

Flash the generated HEX file with USBasp:

```sh
./sh/upload.sh oscilloscope
```

Or rebuild the library, compile the firmware, show memory usage, flash the board, and clean generated files in one command:

```sh
./go.sh oscilloscope
```

## Fuse Configuration

The project includes a fuse configuration script:

```sh
./sh/fusebitConfig.sh
```

This writes fuse bytes for the ATmega328P using USBasp. Only run it if you understand the target clock and boot configuration for your board, because incorrect fuse settings can make the microcontroller difficult to program without the correct clock source.

## Using the Firmware

1. Connect the SSD1306 OLED display to the I2C pins.
2. Connect the rotary encoder to `PD2`, `PD3`, and the switch to `PD4`.
3. Connect the oscilloscope signal input to `ADC2 / PC2`.
4. Flash `oscilloscope.c` to the ATmega328P.
5. Use the rotary encoder to adjust the active scale setting.
6. Press and hold the encoder switch to switch between oscilloscope mode and component tester mode.

In oscilloscope mode, the display shows the waveform plus estimated frequency, time-per-division, and volt-per-division values. In component tester mode, the firmware sweeps `PORTB` and plots a V-I curve from ADC readings.

## Module Notes

The reusable modules are documented in [doc/documentation.md](doc/documentation.md). Highlights include:

- `pin_module`: GPIO configuration, digital write, and digital read helpers
- `delay_module`: millisecond and microsecond delay helpers
- `analog_module`: ADC read helper
- `oled_module`: SSD1306 initialization, drawing, text, and line functions
- `twi_module`: I2C/TWI support
- `mcp_module`: MCP3201 12-bit ADC support
- `ds1307_module`: DS1307 real-time clock support
- `lcd_module`, `keypad_module`, and `ultrasonic_module`: extra peripheral helpers

## Team

- 66070501027 Noppanut Prasertwongsa
- 66070501052 Srisuphadith Rattanaprasert
- 66070501053 Sakchat Hensook
- 66070501060 Adisorn Parama

## References

- [ATmega328P product page](https://www.microchip.com/wwwproducts/en/ATmega328P) - Microchip Technology
- [ATmega328/P complete datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-42735-8-bit-AVR-Microcontroller-ATmega328-328P_Datasheet.pdf) - Microchip Technology
- [ATMEGA328P-PU reference image](https://commons.wikimedia.org/wiki/File:ATMEGA328P-PU.jpg) - Wikimedia Commons, CC BY-SA 2.0

## Project License

No project license file or source-code license notice was found in this repository. Until a license is added by the project owners, the code should be treated as all-rights-reserved by default.

The external ATmega328P reference image linked above is licensed separately under CC BY-SA 2.0 and requires attribution if reused.
