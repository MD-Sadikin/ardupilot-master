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
| PA4 | MPU6500 CS | User-supplied wiring | High |
| PA5 | SPI1 SCK | User-supplied wiring | High |
| PA6 | SPI1 MISO | User-supplied wiring | High |
| PA7 | SPI1 MOSI | User-supplied wiring | High |
| PC4 | MPU6500 interrupt | User-supplied wiring | High |
| PB6 | I2C1 SCL | User-supplied wiring | High |
| PB7 | I2C1 SDA | User-supplied wiring | High |
| PB10 | USART3 TX, GPS | User-supplied wiring | High |
| PB11 | USART3 RX, GPS | User-supplied wiring | High |
| PC0 | Battery voltage ADC1 input placeholder | Unused ADC1-capable pin; carrier wiring is unverified | Low |
| PC1 | Battery current ADC1 input placeholder | Unused ADC1-capable pin; carrier wiring is unverified | Low |
| PA2 | USART2 TX, RC/telemetry candidate | Public reference pinout | Medium |
| PA3 | USART2 RX, RC/telemetry candidate | Public reference pinout | Medium |
| PA0 | UART4 TX, spare | Public reference pinout | Medium |
| PA1 | UART4 RX, spare | Public reference pinout | Medium |
| PB8 | Motor 1 PWM | Public reference pinout; TIM4_CH3 alternate function | Medium |
| PB5 | Motor 2 PWM | Public reference pinout; TIM3_CH2 alternate function | Medium |
| PB4 | Motor 3 PWM | Public reference pinout; TIM3_CH1 alternate function | Medium |
| PB3 | Motor 4 PWM | Public reference pinout; TIM2_CH2 alternate function | Medium |
| PA10 | Motor 5 PWM | Public reference pinout; TIM1_CH3 alternate function | Medium |
| PA9 | Motor 6 PWM | Public reference pinout; TIM1_CH2 alternate function | Medium |
| PB9 | Buzzer | Public reference pinout | Medium |

## Needs hardware verification

- Sensor board orientation; `ROTATION_NONE` is only a build-time placeholder.
- RC and telemetry connector wiring and the intended USART2 direction.
- PWM signal levels, connector mapping, and whether all six motor pads are
  populated on the particular core-board carrier.
- Battery-voltage and current ADC wiring. PC0 and PC1 are only unused ADC1
  placeholders to provide a valid ADC1 DMA configuration; their carrier
  connections must be verified.
- Battery-voltage divider ratio and current-sensor scale. The hwdef uses
  neutral placeholder scales of `1.0`; these must not be used without
  hardware measurement.
- Discrete status LED pin and polarity. PB1 is labelled as an LED-strip
  output in the reference, not a confirmed status LED.
- BMP280 address if the module is strapped to `0x77` instead of `0x76`.
