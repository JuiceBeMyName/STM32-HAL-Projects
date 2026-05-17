# 🚀 Projekt 01: UART Telemetry Frame & Internal Temperature Sensor

Pierwszy projekt zrealizowany z wykorzystaniem biblioteki **HAL (Hardware Abstraction Layer)**. Celem projektu jest nauka obsługi peryferiów analogowych oraz asynchronicznej transmisji danych bez blokowania procesora.

## 📌 Opis działania
Mikrokontroler wykorzystuje wbudowany w strukturę krzemu czujnik temperatury. Zmierzone napięcie przez 12-bitowy przetwornik ADC jest poddawane operacjom bitowym i pakowane w ustandaryzowaną, 5-bajtową ramkę binarną. Następnie ramka jest wysyłana do komputera (lub układu nadrzędnego np. Raspberry Pi) za pomocą interfejsu USART z wykorzystaniem przerwań.

## 🛠 Wykorzystane mechanizmy (Nucleo-L476RG)
* **ADC1 (Analog-to-Digital Converter):** Skonfigurowany do odczytu wewnętrznego kanału temperatury (Temperature Sensor Channel).
* **USART2 (Asynchronous):** Skonfigurowany do przesyłania danych z prędkością 115200 bps.
* **NVIC (Nested Vectored Interrupt Controller):** Obsługa globalnego przerwania `USART2_IRQn` dla nieblokującej transmisji danych (`HAL_UART_Transmit_IT` oraz Callback `HAL_UART_TxCpltCallback`).

## 📦 Struktura ramki binarnej
Dane nie są wysyłane jako czysty tekst ASCII, lecz kompresowane do zoptymalizowanej ramki binarnej, co symuluje profesjonalne protokoły transmisyjne:

| Bajt 0 | Bajt 1 | Bajt 2 | Bajt 3 | Bajt 4 |
| :---: | :---: | :---: | :---: | :---: |
| `0x02` | `0x02` | MSB | LSB | `0x03` |
| **STX** (Start) | **ID** (Sensora) | Starszy bajt ADC | Młodszy bajt ADC | **ETX** (Stop) |

## 🚀 Jak testować?
Do odczytu ramek najlepiej wykorzystać program typu **RealTerm** lub autorski skrypt w języku Python (np. z biblioteką `pyserial`). W RealTermie należy ustawić tryb wyświetlania (Display As) na **Hex[space]**, aby zignorować kodowanie ASCII i obserwować surowe bajty pamięci.
