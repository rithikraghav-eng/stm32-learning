# GPIO Button Polling

## Objective

Read a push-button input using GPIO polling on the STM32F401RE and use its state to control an LED.

> **Implementation note:** Update the pin assignments and electrical details below to match the actual wiring and source code in this experiment. Do not assume the example pins are the ones used by your firmware.

## Hardware

- **Board:** STM32 Nucleo-F401RE
- **MCU:** STM32F401RET6
- **Input:** Push-button
- **Output:** LED (onboard LD2 is connected to PA5 on the Nucleo-F401RE)

| Signal | Example pin | Purpose |
|---|---|---|
| Button input | Confirm from your wiring/code | Read button state |
| LED output | PA5 (LD2) | Indicate button state |

## How polling works

1. Enable the GPIO peripheral clock through RCC.
2. Configure the button pin as a digital input.
3. Configure the LED pin as a digital output.
4. Repeatedly read the button input data register (`GPIOx_IDR`).
5. Apply the required logic to the LED output.

Polling means the CPU repeatedly checks the input in the main execution loop. It does not require an external interrupt.

## Register concepts

- **RCC AHB1ENR:** Enables the clock to the relevant GPIO port.
- **GPIOx_MODER:** Selects input or output mode.
- **GPIOx_PUPDR:** Configures no pull, pull-up, or pull-down where required.
- **GPIOx_IDR:** Reads the logic level present on the input pin.
- **GPIOx_ODR / GPIOx_BSRR:** Controls the output pin.

Use the STM32F401 reference manual to verify the register address, bit position, and reset value before relying on a register configuration.

## Button electrical behaviour

A GPIO input must not be left floating. Use an appropriate external resistor or the MCU's internal pull-up/pull-down, depending on the circuit.

For an active-low button with a pull-up:
- Released: input reads HIGH.
- Pressed: input reads LOW.

For an active-high button with a pull-down:
- Released: input reads LOW.
- Pressed: input reads HIGH.

Confirm which arrangement your circuit uses before interpreting the input value.

## Build

This experiment uses the project's existing CMake configuration. From this directory, use the presets configured in `CMakePresets.json`:

```bash
cmake --list-presets
cmake --preset Debug
cmake --build --preset Debug
```

If the STM32 VS Code Extension provides its own build task or flash workflow, use that established workflow. Do not replace the generated toolchain or flashing configuration without checking it first.

## Flash and test

1. Connect the Nucleo board through its ST-LINK USB connector.
2. Build the Debug configuration.
3. Flash using the STM32 VS Code Extension's existing flash/debug action.
4. Press and release the button.
5. Confirm that the LED follows the behaviour implemented in `Src/main.c`.

## Expected result

The LED responds to the button state according to the logic implemented in the firmware. Record the exact behaviour here (for example, LED ON while pressed, or LED toggles on each press).

## Limitations and next steps

- A polling loop can consume CPU time while continuously checking the input.
- Mechanical buttons can bounce, producing several rapid transitions from one physical press.
- A simple polling implementation may need debounce logic.
- Polling does not provide the same event-driven behaviour as EXTI interrupts.

Possible follow-up experiments:
- Add software debounce.
- Compare polling with EXTI interrupt handling.
- Measure response latency and CPU activity.

## What I learned

Record your own observations:
- Which GPIO port and pin were used?
- Was the button active-high or active-low?
- Which pull configuration was selected, and why?
- Which register was used to read the input?
- What debugging issue did you encounter and how did you resolve it?

## References

- [STM32F401xD/E datasheet](https://www.st.com/resource/en/datasheet/stm32f401re.pdf)
- [STM32F401 reference manual RM0368](https://www.st.com/resource/en/reference_manual/dm00096844.pdf)
- [NUCLEO-F401RE board documentation](https://www.st.com/en/evaluation-tools/nucleo-f401re.html)
