# WeActF405

ArduPilot board definition for the WeAct STM32F405RGT6 core board:

- STM32F405RGT6
- 8 MHz HSE
- 1 MB flash
- 192 KB RAM
- USB-C through the STM32 USB FS pins
- MPU6500 on SPI1
- BMP280 and optional external compass on I2C1
- u-blox GPS on USART3

The board definition intentionally contains only pins confirmed by the
requested wiring or the public `rizacelik/STM32F405RGT6_INAV_Firmware`
pinout reference. It does not copy INAV firmware code.

## Pinout

| Pin | Function | Justification | Confidence |
| --- | --- | --- | --- |
| PB12 | MPU6500 CS | Tested board hwdef | High |
| PB13 | SPI2 SCK | Tested board hwdef | High |
| PC2 | SPI2 MISO | Tested board hwdef | High |
| PC3 | SPI2 MOSI | Tested board hwdef | High |
| PB10 | I2C2 SCL, BMP280 | Tested board hwdef | High |
| PB11 | I2C2 SDA, BMP280 | Tested board hwdef | High |
| PC0 | Battery voltage ADC1 input placeholder | Unused ADC1-capable pin; carrier wiring is unverified | Low |
| PC1 | Battery current ADC1 input placeholder | Unused ADC1-capable pin; carrier wiring is unverified | Low |
| PA2 | USART2 TX, RC/telemetry candidate | Public reference pinout | Medium |
| PA3 | USART2 RX, RC/telemetry candidate | Public reference pinout | Medium |
| PA0 | UART4 TX, GPS | Tested board hwdef | High |
| PA1 | UART4 RX, GPS | Tested board hwdef | High |
| PB6 | USART1 TX | Tested board hwdef | High |
| PB7 | USART1 RX | Tested board hwdef | High |
| PC6 | USART6 TX | Tested board hwdef | High |
| PC7 | USART6 RX | Tested board hwdef | High |
| PC8 | Onboard microSD DAT0 | Official WeActStudio F405 core-board schematic | High |
| PC9 | Onboard microSD DAT1 | Official WeActStudio F405 core-board schematic | High |
| PC10 | Onboard microSD DAT2 | Official WeActStudio F405 core-board schematic | High |
| PC11 | Onboard microSD DAT3 | Official WeActStudio F405 core-board schematic | High |
| PC12 | Onboard microSD CLK | Official WeActStudio F405 core-board schematic | High |
| PD2 | Onboard microSD CMD | Official WeActStudio F405 core-board schematic | High |
| PB2 | Onboard blue notification LED | User-provided board detail | High |
| PB8 | Motor 1 PWM | Public reference pinout; TIM4_CH3 alternate function | Medium |
| PB5 | Motor 2 PWM | Public reference pinout; TIM3_CH2 alternate function | Medium |
| PB4 | Motor 3 PWM | Public reference pinout; TIM3_CH1 alternate function | Medium |
| PB3 | Motor 4 PWM | Public reference pinout; TIM2_CH2 alternate function | Medium |
| PA10 | Motor 5 PWM | Public reference pinout; TIM1_CH3 alternate function | Medium |
| PA9 | Motor 6 PWM | Public reference pinout; TIM1_CH2 alternate function | Medium |
| PB9 | Buzzer | Public reference pinout | Medium |

## Needs hardware verification

- Sensor board orientation; `ROTATION_NONE` is only a build-time placeholder.
- RC input wiring on USART2 and telemetry connector wiring on USART1/USART6.
- PWM signal levels, connector mapping, and whether all six motor pads are
  populated on the particular core-board carrier.
- Battery-voltage and current ADC wiring. PC0 and PC1 are only unused ADC1
  placeholders to provide a valid ADC1 DMA configuration; their carrier
  connections must be verified.
- Battery-voltage divider ratio and current-sensor scale. The hwdef uses
  neutral placeholder scales of `1.0`; these must not be used without
  hardware measurement.
- Onboard blue LED polarity. The hwdef assumes PB2 is active-low; verify this
  if the LED indication is inverted.
- BMP280 address if the module is strapped to `0x77` instead of `0x76`.
- QMC5883L board orientation; `ROTATION_NONE` is a build-time placeholder.
- microSD card logging has been enabled on the onboard slot using SDMMC1;
  card-detect wiring is not used by this definition.

## Sensor bring-up checks

The firmware uses these tested sensor interfaces:

- MPU6500 module: `SCLK` PB13, `MISO` PC2, `SDA/MOSI` PC3,
  `NCS/CS` PB12, and no interrupt pin is required by this tested
  configuration. The board definition uses the
  `Invensense` MPU60x0-family backend for this sensor.
- BMP280 module: `SCL/SCK` PB10, `SDA/MOSI` PB11, `CSB/CS` high for I2C,
  and `SDO/MISO` selecting the I2C address. The board definition currently
  probes address `0x76`.
- QMC5883L compass: PB10/PB11 on the same I2C2 bus, address `0x0D`.
- Onboard microSD slot: SDMMC1 4-bit mode using PC8/PC9/PC10/PC11 for
  DAT0-DAT3, PC12 for CLK, and PD2 for CMD. FATFS logging is enabled.

Use a FAT32-formatted microSD card. A single MBR partition is recommended;
exFAT is not supported by this configuration. The slot is powered and wired
by the core board, so no external SD pull-up or card-detect connection is
required from the user.

The original PA5/PA6/PA7/PA4 and PB6/PB7 assignments were based on the
initial wiring description, but the tested board hwdef supplied for this
hardware uses the SPI2/I2C2 assignments above. Use the tested assignments
when reproducing the known-good firmware behavior.
