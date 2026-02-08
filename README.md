# Axon

Axon is a single-channel mixed-signal Electromyography (EMG) acquisition and actuation system built around a custom PCB. It combines a low-noise analog front end, delta-sigma analog-to-digital conversion, and STM32 firmware for real-time signal acquisition and control of external devices.

The project emphasizes signal integrity, deterministic signal paths, and hardware/firmware co-design in a timing-constrained embedded system.

## System Architecture
Axon implements a linear signal-processing pipeline from electrodes to digital processing:

- Electrodes  
- Input Protection  
- Instrumentation Amplifier  
- Active Band-Pass Filtering  
- Delta-Sigma ADC  
- STM32 (SPI)  
- Actuation Outputs

## Analog Front End

### Input Protection
- 100 kΩ series resistor at the electrode input
- Schottky diode clamps to +5 V and −5 V
- Protects downstream circuitry from transients and overvoltage events

### Instrumentation Amplifier
- INA333 instrumentation amplifier
- Gain: 20×
- Chosen for high CMRR (~100 dB) to suppress common-mode noise inherent in EMG signals
- Reference voltage provided by a buffered ground generated using an OPA333

### Active Filtering
All active filters use OPA333 op-amps for low offset and low noise.

- High-pass Sallen–Key filter  
  - Cutoff frequency: 15 Hz  
  - Linear gain  
  - Removes DC offset and motion artifacts

- Low-pass Sallen–Key filter  
  - Cutoff frequency: 450 Hz
  - Gain: 5×
  - Limits high-frequency noise and out-of-band content

## Analog-to-Digital Conversion

- ADS131M02 delta-sigma ADC
- Differential inputs:
  - Filtered signal routed through 1 kΩ resistors to AIN0P and AIN1P
  - AIN0N and AIN1N tied to buffered ground
- ADC clock provided by an ASE 8.192 MHz oscillator
- Design considers conversion latency and group delay inherent to delta-sigma architectures

## Digital Processing & Control

### Microcontroller
- STM32 microcontroller
- Communicates with ADC via SPI
- Handles data acquisition, processing, and output control

### Programming & Debug
- JTAG / SWD interface exposed
- Signals:
  - SWDIO, SWCLK, SWO, RESET
  - RXI, TXO
  - VREF = 3.3 V

### Outputs
- Three MCU-controlled output pins routed to an external connector
- Intended for actuation or control of external devices


## Power Architecture

- USB-C power input
- Protection fuse on input
- Power rails:
  - +5 V from USB
  - −5 V generated via TPS7A inverter
  - 3.3 V generated via TPS604 DC-DC converter

## PCB Layout

- 4-layer PCB stackup:
  1. Signal
  2. Ground plane
  3. Power pours
  4. Signal
- Layout prioritizes:
  - Low-noise analog routing
  - Solid ground reference
  - Separation of analog and digital domains

## Notes

Axon serves as a completed mixed-signal system demonstrating practical considerations in low-level analog design, real-time embedded firmware, and end-to-end signal path analysis.
