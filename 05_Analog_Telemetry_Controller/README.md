# 05_Analog_Telemetry_Controller (Aim Trainer)

An event-driven hardware-software gaming controller utilizing an analog joystick, multi-channel ADC polling, and a Pygame-based training application.

## Project Overview
This project demonstrates how to interface analog human-input hardware with an embedded system, optimize UART bandwidth using firmware-level filtering, and implement relative velocity kinematics for smooth on-screen cursor control.

## Features
- **Multi-Channel ADC Polling:** Configured ADC1 in Discontinuous Mode to sequentially scan two independent analog axes (X and Y) without the overhead of complex DMA or interrupts.
- **Hardware Hysteresis:** Firmware-level threshold filtering (`JOY_THRESHOLD`) to eliminate ADC thermal noise and signal fluctuations, ensuring quiet UART lines when the stick is static.
- **Event-Driven Transmission:** Data packages are transmitted via UART only when a significant physical movement or button state change occurs.
- **Relative Kinematics & Deadzone Handling:** The Python application utilizes velocity mapping (joystick displacement controls cursor speed, not absolute position) and implements a software deadzone to counteract hardware joystick drift.
- **Persistent High Scores:** Automatic local storage (`highscore.txt`) to save and track the player's best performance across sessions.

## Hardware Configuration
- **Microcontroller:** STM32L476RG (Nucleo-L476)
- **Input Device:** KY-023 Analog Joystick Module
- **Pinout Mapping:**
  - `ADC1_IN5` -> PA0 (Joystick VRx)
  - `ADC1_IN6` -> PA1 (Joystick VRy)
  - `GPIO_Input (Pull-up)` -> PB0 (Joystick SW / Button)
  - `USART2_TX` -> PA2 (Virtual COM Port via USB)

*Note: The joystick is entirely powered by the **3.3V** rail of the Nucleo board to protect the STM32 analog input pins from overvoltage.*

## Software Stack
- **Firmware:** C (STM32CubeIDE, focused on ADC sampling optimization and `abs()` mathematical filtering)
- **Game Application:** Python 3.x (utilizing `pygame` for 60 FPS graphics and `pyserial` for data parsing)

## How to Run
1. Connect the joystick pins to the designated Nucleo ports (ensure 3.3V power supply).
2. Compile and flash the C firmware.
3. Install Python packages: `pip install pygame pyserial`.
4. Close any open serial terminals (like RealTerm) and execute the Python script.
