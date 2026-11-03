# STM32 Bare-Metal Button Debouncing & LED Toggle

## Overview

This project demonstrates direct register-level GPIO programming on the **STM32F401RE Nucleo** board using Embedded C.

The onboard user button (PC13) is used to toggle the onboard LED (PA5). A software-based debouncing mechanism ensures that one complete button press produces one LED toggle.

The project uses direct memory-mapped register access without HAL or LL libraries.

## Hardware

| Component | Configuration |
|---|---|
| Microcontroller | STM32F401RE |
| Development Board | NUCLEO-F401RE |
| User Button | PC13 |
| Onboard LED (LD2) | PA5 |
| Clock Enable | RCC AHB1 peripheral clock |

## Features

- Direct memory-mapped register access.
- GPIO clock configuration through RCC.
- GPIO input and output configuration.
- Active-low button detection.
- Software-based button debouncing.
- Button press and release detection.
- LED state toggling using bitwise XOR.
- Atomic GPIO output control using BSRR.

## Register Configuration

The following registers are accessed directly through their memory addresses.

| Register | Address | Purpose |
|---|---|---|
| RCC_AHB1ENR | 0x40023830 | Enable GPIO peripheral clocks |
| GPIOA_MODER | 0x40020000 | Configure PA5 as output |
| GPIOA_BSRR | 0x40020018 | Set/reset PA5 |
| GPIOC_MODER | 0x40020800 | Configure PC13 as input |
| GPIOC_IDR | 0x40020810 | Read PC13 input state |

### GPIO Clock Enable

```c
RCC_AHB1ENR |= (1U << 0);  // Enable GPIOA
RCC_AHB1ENR |= (1U << 2);  // Enable GPIOC
```

### GPIO Pin Configuration

**PA5 – Output Mode**

```c
GPIOA_MODER &= ~(3U << 10);
GPIOA_MODER |=  (1U << 10);
```

**PC13 – Input Mode**

```c
GPIOC_MODER &= ~(3U << 26);
```

## Button Debouncing Logic

The onboard user button is active-low:

- HIGH (1): Button released.
- LOW (0): Button pressed.

The `buttonstate()` function implements the following sequence:

1. Detect a LOW signal on PC13.
2. Wait for a software debounce interval.
3. Confirm that the button remains LOW.
4. Wait until the button is released.
5. Apply a release debounce delay.
6. Return `1` to indicate one valid button press.

Otherwise, it returns `0`.

### Button Detection

```c
if (((GPIOC_IDR >> 13) & 1U) == 0U)
```

The expression extracts bit 13 and checks whether the button is pressed.

## LED Control

The LED state is maintained using a software variable.

```c
led_state ^= 1U;
```

XOR toggles the LED state between 0 and 1.

### BSRR Operations

```c
GPIOA_BSRR = (1U << 5);   // Set PA5 HIGH
GPIOA_BSRR = (1U << 21);  // Reset PA5 LOW
```

The BSRR register provides separate bit positions for setting and resetting GPIO outputs.

- Bits 0–15: Set output pins.
- Bits 16–31: Reset output pins.

PA5 reset bit = 5 + 16 = 21.

## Program Flow
