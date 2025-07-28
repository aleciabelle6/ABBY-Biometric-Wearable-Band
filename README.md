# Anxiety Buddy Band for Youth (ABBY) - Biometric Wearable Band  
_Alecia Barbieri — Princeton University ECE Funded Summer Internship, 2023_

## Project Overview

This repository contains the KiCad design files for a custom Electrodermal Activity (EDA) and heart rate/blood oxygen sensor signal conditioning circuit. It integrates analog signal conditioning stages to filter, amplify, and prepare the weak physiological signals from wearable electrodes and sensors for digitization. This project involved extending and enhancing the Open-Smartwatch-Light open-source platform (https://open-smartwatch.github.io/) by advancing its electronic hardware and serves as my first exposure to KiCAD. 

The circuit is designed to amplify and clean signals from EDA electrodes, with additional capability for heart rate and blood oxygen sensing and position tracking. The design focuses on maximizing signal fidelity through multi-stage amplification and filtering, using a low-power LM324D operational amplifier.

---

## Circuit Extension Design Summary

Taking on this project with no prior experience in PCB design was a very daunting but nonetheless rewarding endeavor. As such, I gained so much knowledge from starting with an open-source PCB layout and extending the functionality of it. Below, I describe the various additions that I made to the design.

### Heart rate and Pulse Sensing

The BH1792GLC-E2 is an optical sensor that measures heart rate and pulse by detecting changes in blood volume using reflected green light. It emits green light through integrated LEDs and captures the reflected signal from skin tissue using a photodiode. This reflected light varies with blood flow, allowing the sensor to extract photoplethysmography (PPG) pulse wave information that is digitized internally and outputt via I2C to the ESP32 microcontroller. The sensor operates with precise timing control and built-in ambient light cancellation for the purpose of enhancing signal quality and reducing noise.

### EDA Sensor Signal Processing

EDA (Electrodermal Activity) electrodes measure changes in skin conductance caused by sweat gland activity, which reflects stress levels. I designed an analog signal conditioning chain that consists of three distinct stages, each implemented using one of the four internal amplifiers in the LM324D quad op-amp IC, for the purpose of having access to noise-free, clear, and useful data that can inform the microcontroller on parts of the physiological makeup that even the user isn't aren't aware of. The raw EDA signal interfaces with the board through an SJ-3524 connector. From there it gets processed through a 3-stage amplifier. The first stage is a differential amplifier stage, which takes in the raw EDA electrode signals, biased with a 1.65 V reference to accommodate single-supply operation and amplifies the differential signal from the electrodes while rejecting common-mode noise and interference. The second stage is an active low-pass filter stage which attenuates high-frequency noise and artifacts, preserving the low-frequency physiological signals typical in EDA and heart rate sensing. The third and final stage is the gain stage, which provides a gain of 11 to bring the signal into an optimal range for analog-to-digital conversion. This conditioned final signal then gets sent to an ADC input that then interfaces with the I2C protocols of the ESP32 at address 0x48.  

### Miscellaneous Changes
I changed the circular Open-Smartwatch-Light into a rectangular board to have enough room to accommodate the new devices. With this, I rerouted the entire board, prioritizing sectioning off analog, digital, and power lines as much as possible with the limited board space and also prioritized having sufficient trace width for the current passing through each branch of the circuit. 

---

## Circuit Component Overview

This section highlights the different components' functions in the circuit. 

The TTGO T-Micro32 V2.0 (ESP32-based Microcontroller) was used due to its very compact size, coming in at 45% smaller than standard ESP32. It has full GPIO support, Wi-Fi and Bluetooth features, and advanced power-saving features including clock gating, deep sleep, and dynamic power delegation. This microcontroller controls all of the devices, primarily using I2C to communicate with the different sensors as well as the ADC converter.

As far as sensing, the BH1792GLC-E2 ECG/PPG Heart Rate and Oximeter Sensor is an IR-based pulse wave sensor for heart rate and SpO2 monitoring. Communicates via I2C, uses interrupt-based burst reads, and includes bypass capacitors for power stability. The BMA400 3-axis accelerometer is an ultra-low power accelerometer for motion and activity tracking. It integrates with I2C, uses INT pin for data-ready alerts, and includes bypass capacitor for voltage filtering. The board supports an external pair of EDA electrodes spaced 4 cm apart at the top and bottom of the wrist for optimal skin conductance sensing. They are routed through SJ-3524 connector, then through 3-stage amplifier, then through ADC, then to I2C. In order for the EDA electrodes to be able to interface with the rest of the electronics, it must have a SJ-3524-SMT connector jack as seen in this pair of EDA electrodes: https://lafayettepolygraph.com/products/eda-assembly-7ft-lx6/. Throughout the board there are many capacitors used for voltage stability, filtering, and transient suppression across biosensors, power supply, and signal lines. Resistors are also employed in various places for pulling up the I2C lines as well as noise cancelling, as seen in the low pass filter for the EDA electrode signals.

The GC9A01 IPS Display: 1.28” circular IPS LCD with SPI interface, parallel RGB support, and adjustable brightness via 2N7002 transistor switch, which happenss by adjusting current through the LEDK pin on the GC9A01 screen, which in turn adjusts the backllight brightness of the display. This display screen offers wide viewing angles and vibrant display quality. There are also 3 SPST Buttons used for power enable (EN pin control) and general-purpose user inputs (e.g. calibrate, transmit).

The power delegation of the board is quite intuitive. The board is powered by a 3.7V LiPo battery with shorting protection; monitored via voltage divider and B_MON pin for on-screen battery status display. The MCP73831 LiPo Charging Management Controller is a constant-current/voltage charger with thermal regulation, charge termination, and USB-powered battery input for safe, efficient battery management. The TPS2115A Power Multiplexer is a smart autoswitching between USB and battery power sources based on availability and voltage conditions. It acts very similiar to a rely in that it relies on the battery level of the battery to determine which input to transfer to the output and to the next stage of the power management system. The PCB also contains 2 XC6209 LDO Voltage regulators that convert the output of the multiplexer to a consistent 3.3V output regardless of the input level, allowing for consistent operation of sensor powering and the main logic throughout the circuit. The micro-USB port has two functions as it takes in a charging cable to charge the onboard Li-Po battery using a regulated charging circuit, and this port also provides a data interface for uploading firmware to the microcontroller. This enables both power management and programming through a single, convenient connection. With this, the CH340E chip is a USB to Serial Converter converts the inputs of the micro-USB to UART to connect with the microcontroller to upload firmware. 

## Repository Contents

- **KiCad Project Files:** Schematic, PCB layout, and BOM files for the full EDA signal conditioning circuit.
- **EDA Models:** The symbol, footprint, and 3D CAD model for each component on the PCB.
- **Datasheets:** Relevant datasheets for each component.


## Getting Started

1. Clone the repository:
   ```bash
   git clone https://github.com/aleciabelle6/ABBY-Biometric-Wearable-Band.git
   cd ABBY-Biometric-Wearable-Band
2. On KiCAD, select **File > Open Project**.
3. Navigate to where you cloned this repository and open the file *abbyWatchV1.kicad_pro*. 

## License

This project is made available for academic, non-commercial use. Please contact me if you'd like to use any part of the content or code beyond that scope.

---

*Project Advisor: Dr. David Wentzlaff*  
*Department of Electrical and Computer Engineering, Princeton University*
