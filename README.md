# Lydia

Firmware for a battery-powered, BLE-connected smart thermostat with an e-paper
display. The device reads ambient conditions, drives heat/cool/fan relays
according to local schedules or demand-response (DR) events, renders a
thermostat dial on its e-paper screen, and syncs setpoints/schedules/history
with a companion app over Bluetooth Low Energy. The history and der commands 
sync to the backend when the phone is in BLE range of the thermostat.

## Hardware

- **MCU**: Microchip PIC24FJ256GB406 (16-bit PIC24F), 16 MHz instruction clock
- **BLE**: Microchip RN4871 module (UART, AT-command controlled, custom GATT services)
- **Environmental sensor**: Bosch BME680 (temperature, humidity, pressure, gas — SPI)
- **Board temperature**: LMT70 analog sensor via ADC
- **Display**: Crystalfontz CFAP176264A0-0270 e-paper panel (176x264, SPI)
- **Non-volatile storage**: SPI FRAM (setpoints, schedules, DR events, circular history buffers)
- **Timekeeping**: PIC24 RTCC peripheral
- Physical buttons (Up/Down/Timer/Power) and relay outputs for heat/cool/fan

## Repository layout

```
R1/                   MPLAB X IDE project (firmware source)
  main.c / main.h       App entry point, state machine (sleep/connect/active),
                         thermostat logic, schedules, BLE UUID/handle definitions
  system.c / system.h   Sensor configuration, relay control, schedule state machine
  hal.h / HAL.c          Low-level peripheral drivers (oscillator, pins, timers,
                         SPI1, UART1-3, ADC1, interrupts)
  RN4871.c / RN4871.h    RN4871 BLE module driver
  BME680.c / BME680.h    Bosch BME680 sensor driver
  bme680_defs.h          Vendor BME680 register/type definitions
  epaper.c / epaper.h    E-paper display driver and drawing primitives
  epaper_images.h        Bitmap/image assets for the display
  LUTs_for_CFAP176264A00270.h  E-paper waveform lookup tables
  FRAM.c / FRAM.h        FRAM read/write driver
  memory.c / memory.h    FRAM memory map and allocation logic
  rtcc.c / rtcc.h        RTCC driver
  nbproject/             MPLAB X project metadata
  Makefile               MPLAB X-generated build entry point

BatteryLifeEstimate.set              Battery life estimation worksheet
affect_of_board_voltage_change.xlsx  Analysis of board voltage effects
FRAM_allocation_calculations.xlsx    FRAM memory layout calculations
ble_version_info.txt                 RN4871 firmware version reference (V1.30)
IMG_0217.JPG, IMG_0218.JPG           Reference/hardware photos
```

## Toolchain

- **IDE**: MPLAB X IDE
- **Compiler**: Microchip XC16 v1.36
- **Device family pack**: PIC24F-GA-GB_DFP

## Building

1. Install MPLAB X IDE, the XC16 v1.36 compiler, and the PIC24F-GA-GB device
   family pack.
2. Open `R1/` as an existing MPLAB X project.
3. Build via **Clean and Build** in the IDE, or run `make` from `R1/` using
   the generated `Makefile`. Output artifacts are written to `R1/dist/`.

Debug-only features (BLE test mode, FRAM check/reset, display symbol test,
time-sync test) are toggled via `#define`s near the top of `main.h`.
