# STM32F401 Bare-Metal Learning

**Learning ARM Cortex-M4 by building firmware from the ground up.**

A hands-on repository documenting my journey into embedded systems development using the STM32F401 microcontroller, C programming, and bare-metal register-level programming.

The goal is to understand how microcontrollers work internally, develop peripheral drivers, and build practical embedded applications without relying on high-level hardware abstraction.

---

## 🎯 Objectives

- Understand ARM Cortex-M4 architecture and memory organization.
- Learn bare-metal C programming and direct register manipulation.
- Configure and control STM32 peripherals using reference manuals.
- Understand startup code, linker scripts, interrupts, and memory mapping.
- Implement communication protocols such as UART, SPI, and I2C.
- Develop reusable and modular peripheral drivers.
- Gain practical experience with embedded debugging and firmware development.

## 🛠️ Hardware & Development Environment

| Component | Details |
|---|---|
| Microcontroller | STM32F401 |
| Development Board | STM32 Nucleo-F401RE |
| Architecture | ARM Cortex-M4 |
| Programming Language | C |
| Compiler | ARM GCC |
| Build System | CMake |
| Build Generator | Ninja |
| IDE | VS Code |
| Debugger | ST-Link / GDB |
| Development OS | Ubuntu Linux |

## 📚 Learning Roadmap

### 01. GPIO Programming
- [x] LED Blink
- [ ] Button Input – Polling
- [ ] Multiple GPIO Outputs
- [ ] Knight Rider LED Sequence
- [ ] GPIO Input and Output Registers

### 02. Startup Code & Interrupts
- [ ] Memory Layout and Address Mapping
- [ ] Startup Code and Vector Table
- [ ] Reset Handler
- [ ] NVIC Configuration
- [ ] External Interrupts (EXTI)

### 03. Timers & PWM
- [ ] SysTick Timer
- [ ] General Purpose Timers
- [ ] Timer Interrupts
- [ ] PWM Generation
- [ ] Input Capture

### 04. Communication Protocols & Sensors
- [ ] UART
- [ ] SPI
- [ ] I2C
- [ ] ADC
- [ ] DMA
- [ ] Sensor Interfacing

### 05. Advanced Embedded Concepts
- [ ] Watchdog Timers
- [ ] Low Power Modes
- [ ] Interrupt-driven Programming
- [ ] Memory Management
- [ ] Embedded Debugging

---

## 📂 Repository Structure

```text
stm32f401-learning/
│
├── README.md
│
├── notebook/
│
├── 01-gpio/
│   ├── blink/
│   ├── button-poll/
│   └── knight-rider/
│
├── 02-startup-and-interrupts/
│
├── 03-timers/
│
├── 04-comms-sensors/
│
└── docs/
    ├── stm32f401-pinout.md
    ├── memory-map.md
    └── debugging.md
```

Each experiment maintains its own source files, CMake configuration, startup code, linker script, dependencies, and documentation to ensure independent builds.

## 🧠 Development Philosophy

1. Understand the hardware before writing firmware.
2. Refer to the reference manual when configuring peripherals.
3. Prefer direct register programming when learning peripheral internals.
4. Build and test each experiment independently.
5. Document implementation details, debugging challenges, and observations.
6. Focus on understanding rather than simply making the code work.

## 📖 References

- [STM32F401 Reference Manual – RM0368](https://www.st.com/resource/en/reference_manual/dm00096844.pdf)

