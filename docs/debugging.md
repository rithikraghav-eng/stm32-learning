# STM32F401 Debugging Notes

## Hardware and tools

Current learning setup:
- Board: NUCLEO-F401RE
- MCU: STM32F401RET6
- Debug probe: onboard ST-LINK
- IDE: VS Code with STM32 VS Code Extension
- Toolchain: GNU Arm Embedded Toolchain
- Build system: CMake with Ninja

## Recommended debug cycle

1. Build the Debug configuration.
2. Flash the generated firmware using the STM32 extension.
3. Start a debug session.
4. Set a breakpoint in `main()` or the peripheral initialization code.
5. Run or step through the firmware.
6. Inspect registers, variables, and the call stack.
7. Check the peripheral clock and pin configuration before assuming the source code is wrong.

## Useful GDB commands

```gdb
# Run / control execution
monitor reset halt
break main
continue
step
next
finish
halt

# Inspect execution
info registers
backtrace
list

# Inspect memory
x/16wx 0x40020000
x/1wx 0x40023830

# Inspect a specific address as a word
x/wx ADDRESS

# Read a byte
x/bx ADDRESS
```

The addresses above are examples for GPIOA and RCC on STM32F401. Verify the relevant register address in RM0368 before interpreting the value.

## Common register checks

For a GPIO output that does not work, check:

1. Is the GPIO peripheral clock enabled in `RCC_AHB1ENR`?
2. Does `GPIOx_MODER` configure the pin as output?
3. Is the pin configured for the expected output type?
4. Is the output state set in `GPIOx_ODR` or through `GPIOx_BSRR`?
5. Is the selected pin actually connected to the LED or external circuit?
6. Is another peripheral or board-level circuit using the pin?

For a GPIO input that reads incorrectly, check:

1. Is the pin configured as input?
2. Is the input electrically driven?
3. Is the pull-up/pull-down configuration correct?
4. Is the button active-high or active-low?
5. Does the code read the correct `GPIOx_IDR` bit?
6. Is switch bounce affecting the observed behaviour?

## LED does not blink

Check in this order:

- Confirm the correct board and LED pin. On NUCLEO-F401RE, LD2 is connected to PA5.
- Confirm the GPIOA clock is enabled.
- Inspect `GPIOA_MODER` and verify PA5 is in output mode.
- Inspect `GPIOA_ODR` or `GPIOA_BSRR`.
- Set a breakpoint and confirm the main loop is executing.
- Check whether the delay implementation is blocking, optimized away, or based on an unconfigured timer.
- Confirm the flashed ELF corresponds to the source you are inspecting.

## Build and flash failures

- Read the first meaningful compiler/linker error, not only the final failure line.
- Confirm the ARM GCC toolchain is installed and on PATH.
- Confirm the CMake configure step uses the intended preset.
- Confirm the linker script path exists and is passed to the linker.
- Confirm the correct ST-LINK device is detected.
- Do not delete the build directory as the first troubleshooting step; inspect the configure/build error first.

## Useful terminal commands

```bash
# Toolchain versions
arm-none-eabi-gcc --version
cmake --version
ninja --version

# Check USB devices
lsusb

# Check serial devices
ls -l /dev/ttyACM* /dev/ttyUSB* 2>/dev/null

# Inspect ELF sections and size
arm-none-eabi-objdump -h path/to/firmware.elf
arm-none-eabi-size path/to/firmware.elf

# Inspect symbols
arm-none-eabi-nm -n path/to/firmware.elf
```

## Debugging log template

For each issue, record:

- **Symptom:** What did the board do?
- **Expected behaviour:** What should have happened?
- **Reproduction:** Steps to reproduce the issue.
- **Measurements:** Register values, debugger output, or electrical measurements.
- **Root cause:** The confirmed cause, not an assumption.
- **Fix:** What changed?
- **Verification:** How was the fix tested?

## Important cautions

- Reading a register is not proof that the external pin has the expected voltage. Use a multimeter or oscilloscope when electrical confirmation is needed.
- A successful flash operation does not prove the firmware is executing correctly.
- Avoid changing multiple register settings at once while debugging.
- Be careful when writing peripheral registers; an incorrect write can alter clocks, pin modes, or peripheral state.

## References

- [STM32F401 reference manual RM0368](https://www.st.com/resource/en/reference_manual/dm00096844.pdf)
- [STM32F401xD/E datasheet](https://www.st.com/resource/en/datasheet/stm32f401re.pdf)
- [NUCLEO-F401RE board documentation](https://www.st.com/en/evaluation-tools/nucleo-f401re.html)
