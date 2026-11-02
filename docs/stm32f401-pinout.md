# STM32F401RE Pinout Notes

## Target device

This repository's current project configuration identifies the MCU as **STM32F401RET6** and the development board as **NUCLEO-F401RE**.

The MCU package pinout and the Nucleo board's Arduino/ST morpho headers are not interchangeable diagrams. Use the Nucleo board user manual and board schematic when mapping a header pin to an MCU pin.

## Useful board signals

| Board signal | MCU pin | Notes |
|---|---|---|
| LD2 (user LED) | PA5 | Onboard user LED on NUCLEO-F401RE |
| User button (B1) | PC13 | Onboard user push-button; verify board revision/schematic |
| ST-LINK virtual COM port | PA2 / PA3 | USART2 TX/RX connection on the Nucleo board; check board documentation and solder-bridge configuration |
| SWDIO | PA13 | Serial Wire Debug data |
| SWCLK | PA14 | Serial Wire Debug clock |

These are common NUCLEO-F401RE board mappings. Confirm against the exact board revision and schematic before wiring external circuitry.

## GPIO port naming

STM32 GPIO pins are identified by port and pin number:

- `PA5` means GPIO port A, pin 5.
- `PB3` means GPIO port B, pin 3.
- `PC13` means GPIO port C, pin 13.

The port's GPIO registers are accessed through the peripheral memory map. The GPIO peripheral clock must be enabled before configuring or using the port.

## GPIO configuration checklist

For a digital input or output, check:

1. Enable the corresponding GPIO port clock in `RCC_AHB1ENR`.
2. Configure the pin mode in `GPIOx_MODER`.
3. Configure output type in `GPIOx_OTYPER` when using output mode.
4. Configure speed in `GPIOx_OSPEEDR` when needed.
5. Configure pull-up/pull-down in `GPIOx_PUPDR`.
6. Read inputs through `GPIOx_IDR`.
7. Set/reset outputs using `GPIOx_BSRR` or `GPIOx_ODR`.

Each pin uses two bits in `MODER`, `OSPEEDR`, and `PUPDR`; each pin uses one bit in `OTYPER`, `IDR`, and `ODR`.

## Alternate functions

A GPIO pin can also be assigned to an alternate function for peripherals such as USART, SPI, I2C, timers, and other functions. The alternate-function selection is configured through `GPIOx_AFRL` or `GPIOx_AFRH`, along with setting the pin's mode to alternate function.

Do not select an alternate function based only on a generic pinout image. Verify the exact AF mapping in the STM32F401 datasheet.

## Official references

- [STM32F401xD/E datasheet](https://www.st.com/resource/en/datasheet/stm32f401re.pdf) — package pinout and alternate-function tables.
- [NUCLEO-F401RE product page and board documentation](https://www.st.com/en/evaluation-tools/nucleo-f401re.html) — board connectors, schematic, and user manual.
- [STM32F401 reference manual RM0368](https://www.st.com/resource/en/reference_manual/dm00096844.pdf) — GPIO and RCC register details.

## Maintenance note

When using a different STM32F401 package or a different Nucleo board revision, re-check the pin mapping. Do not assume every STM32F401 board exposes the same pins or onboard peripherals.
