# Embedded Systems II (EEE3096S)

Welcome to my repository for Embedded Systems II (EEE3096S) at the University of Cape Town. This repository serves as a tracker and portfolio for my practicals, focusing on low-level microcontroller programming and hardware interaction.

## Overview

This workspace contains all my lab exercises, configurations, and project code using the STM32 microcontroller ecosystem. The code here is written in C and leverages STM32CubeIDE alongside the ST Hardware Abstraction Layer (HAL) to configure and control bare-metal hardware.

## Peripherals & Concepts Explored

As the semester progresses, this repository will contain implementations covering:
* **GPIOs:** Direct pin manipulation, external interrupts, and hardware debouncing.
* **Timers:** Precise hardware delays, PWM signal generation, and hardware interrupts.
* **ADC:** Analog-to-digital conversion for continuous sensor data acquisition.
* **Displays:** Interfacing with a 2x16 LCD for real-time data output.
* **System Clocking:** Managing the `.ioc` configuration, clock trees, and peripheral multiplexing.

## Repository Structure

Each practical or lab assignment is organized into its own directory. Inside, you will find:
1. The `.ioc` configuration file used by STM32CubeMX.
2. The root `Core/` directory containing the `Src/` and `Inc/` folders with the main application logic.
3. The `Drivers/` directory containing the relevant HAL library files.

---
*Author: Takudzwa Antony Masendeke*