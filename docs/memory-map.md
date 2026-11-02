# STM32F401RE Memory Map Notes

## Scope

These notes target the STM32F401xD/xE family, including the STM32F401RET6 used by the current NUCLEO-F401RE project.

The addresses below are high-level address regions for orientation. Consult RM0368 and the exact device datasheet for the complete peripheral map, memory sizes, and implementation details.

## Cortex-M4 address regions

| Region | Base address | Purpose |
|---|---:|---|
| Code region | `0x00000000` | Code-space alias; boot mapping determines what is visible here |
| SRAM | `0x20000000` | Data, stack, heap, and runtime variables |
| Peripheral region | `0x40000000` | Memory-mapped peripheral registers |
| External RAM region | `0x60000000` | External memory interface region; device support-dependent |
| External device region | `0xA0000000` | External device region; device support-dependent |
| Private peripheral bus | `0xE0000000` | Cortex-M system control/debug components |

## STM32F401RET6 memories

For STM32F401xD/xE devices, the main on-chip memories include:

| Memory | Base address | Size |
|---|---:|---:|
| Flash | `0x08000000` | 512 KB for STM32F401xE |
| SRAM | `0x20000000` | 96 KB |

The STM32F401xE also includes 64 KB of CCM data RAM at `0x10000000`. CCM RAM is core-coupled and is not equivalent to ordinary DMA-accessible SRAM; check the reference manual before placing DMA buffers there.

## Flash layout

The main Flash array starts at `0x08000000`. The reset and interrupt vector table is normally placed at the beginning of the boot memory image.

The project's linker script (`stm32f401xe_flash.ld`) determines how sections such as `.isr_vector`, `.text`, `.rodata`, `.data`, and `.bss` are placed in memory.

- `.isr_vector`: interrupt vector table.
- `.text`: executable code.
- `.rodata`: read-only constants.
- `.data`: initialized global/static variables; initial values are stored in Flash and copied to RAM during startup.
- `.bss`: zero-initialized global/static variables; cleared during startup.
- Heap and stack: runtime memory areas defined or reserved by the linker script/startup code.

Do not edit the linker script based only on this summary. Check the memory regions and startup implementation together.

## Peripheral address space

Peripheral registers are memory-mapped. Firmware accesses them through addresses defined by CMSIS device headers or by carefully written register definitions.

Examples of peripheral groups include:
- RCC: reset and clock control.
- GPIO: general-purpose input/output.
- USART: serial communication.
- SPI and I2C: serial buses.
- TIM: timers.
- ADC: analog-to-digital conversion.

The exact base addresses and register offsets must be taken from RM0368 or the device header.

## Why this matters in bare-metal firmware

Understanding the memory map helps explain:

1. Why peripheral registers are accessed through fixed addresses.
2. Why the linker script controls where code and data are placed.
3. Why startup code copies `.data` and clears `.bss`.
4. Why the initial stack pointer is loaded from the vector table.
5. Why a pointer to a peripheral register must use the correct address and access width.

## How to inspect the actual project layout

From the project directory:

```bash
arm-none-eabi-objdump -h build/Debug/blink.elf
arm-none-eabi-size build/Debug/blink.elf
arm-none-eabi-readelf -S build/Debug/blink.elf
```

Adjust the ELF path to match the output location produced by your CMake preset.

Inspect the linker script:

```bash
cat stm32f401xe_flash.ld
```

## Official references

- [STM32F401 reference manual RM0368](https://www.st.com/resource/en/reference_manual/dm00096844.pdf)
- [STM32F401xD/E datasheet](https://www.st.com/resource/en/datasheet/stm32f401re.pdf)
- [ARM Cortex-M4 documentation](https://developer.arm.com/documentation/100166/latest/)
