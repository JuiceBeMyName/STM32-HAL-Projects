# 📡 STM32 Advanced Ultrasonic Radar & Telemetry System

A professional, bare-metal embedded system built on the STM32 architecture (ARM Cortex-M4). This project implements a real-time ultrasonic distance tracker with a bidirectional Python telemetry interface via UART DMA, non-blocking hardware debouncing, and a 16x2 LCD display.

## 🚀 Key Features

* **Non-Blocking Architecture:** The system uses `HAL_GetTick()` for all timing operations, ensuring the processor is never frozen by `HAL_Delay()` during the main execution loop.
* **Hardware Interrupts & DMA:** Uses Timer Input Capture (TIM3) for microsecond-precise echo timing and UART with DMA (Direct Memory Access) for seamless PC communication.
* **Smart Standby Mode:** Features a physical main switch with advanced software debouncing (Falling Edge detection) to safely put the system into standby, cutting off sensor emissions and telemetry.
* **Bidirectional Telemetry:** * **TX (STM32 -> PC):** Transmits binary frames with XOR checksums containing the precise distance in centimeters.
  * **RX (PC -> STM32):** The Python host evaluates the distance and sends back specific hardware commands `<LED,BUZZER>` to control warning states dynamically.
* **Bare-Metal LCD Driver:** Custom 4-bit mode implementation for the HD44780 controller, avoiding heavy libraries and completely eliminating floating-point math (`sprintf` with `%f`) to save Flash memory.

---

## 🔌 Hardware Wiring Guide

To replicate this setup, connect the components to the STM32 Nucleo board as follows:

### 1. HC-SR04 Ultrasonic Sensor
| Sensor Pin | STM32 Nucleo / Power | Description |
| :--- | :--- | :--- |
| **VCC** | 5V | Sensor power supply |
| **GND** | GND | Common ground |
| **TRIG** | `SENSOR_TRIG_Pin` (GPIOA) | 10µs trigger pulse (Output) |
| **ECHO** | `TIM3_CH1` | Timer Input Capture (Input) |

### 2. Status Indicators (LEDs & Buzzer)
| Component | STM32 Nucleo Pin | Behavior |
| :--- | :--- | :--- |
| **Green LED** | `LED_GREEN_Pin` (GPIOC) | Distance > 50 cm (Clear) |
| **Yellow LED** | `LED_YELLOW_Pin` (GPIOC) | Distance 20-50 cm (Warning) |
| **Red LED** | `LED_RED_Pin` (GPIOC) | Distance 5-20 cm (Alarm) |
| **Active Buzzer** | `BUZZER_ACTIVE_Pin` (GPIOC)| Dynamic beeping based on distance |

### 3. Main Control Button
| Button Pin | STM32 Nucleo / Power | Description |
| :--- | :--- | :--- |
| **Leg 1** | `MAIN_BTN_Pin` (PA9) | Configured with Internal Pull-Up |
| **Leg 2** | GND | Triggers Falling Edge upon press |

### 4. LCD 16x2 (HD44780 - 4-Bit Mode)
*Note: The display requires a 5V logic supply to operate the liquid crystals properly.*

| LCD Pin | Name | Connection |
| :--- | :--- | :--- |
| **1** | VSS | GND |
| **2** | VDD | 5V |
| **3** | VO | Middle pin of a 10kΩ Potentiometer (Ends to 5V & GND) |
| **4** | RS | `LCD_RS_Pin` (GPIOC) |
| **5** | RW | GND (Hardwired for Write-Only mode) |
| **6** | E | `LCD_EN_Pin` (GPIOC) |
| **7 - 10** | D0 - D3 | *Not Connected (Floating)* |
| **11** | D4 | `LCD_D4_Pin` (GPIOB) |
| **12** | D5 | `LCD_D5_Pin` (GPIOB) |
| **13** | D6 | `LCD_D6_Pin` (GPIOB) |
| **14** | D7 | `LCD_D7_Pin` (GPIOC) |
| **15** | A (Anode) | 5V (Optional: via 220Ω resistor) |
| **16** | K (Cathode)| GND |

---

## 💻 Software Setup

### Microcontroller (STM32)
1. Open the `.ioc` file in **STM32CubeIDE**.
2. Generate the code to ensure all HAL libraries are linked.
3. Build the project and flash it to the Nucleo board.

### Python Host Application
The host script requires the `pyserial` library to decode the binary frames and send commands back to the STM32.

```bash
# Install the required library
pip install pyserial

# Run the telemetry script (Ensure the correct COM port is set in the script)
python host_telemetry.py
