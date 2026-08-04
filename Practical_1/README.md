# Embedded Systems II (EEE3096S) - Practical 1

Documentation and code tracker for **Practical 1: STM32 GPIO, Timers, and Interrupts** at the University of Cape Town[cite: 1]. 

This practical focuses on transitioning from polled loops to hardware-driven architectures using STM32CubeIDE and the Hardware Abstraction Layer (HAL)[cite: 1].

---

##  Practical Overview & Tasks

This assignment spans six core tasks designed to build low-level competency with the STM32F051C6 microcontroller[cite: 1]:

*   **Task 1: Timer Configuration & Verification** – Configuring TIM16 for a 1 Hz interrupt tick, calculating prescaler/ARR values, and verifying output using an oscilloscope[cite: 1].
*   **Task 2: LED Control with Timer Interrupts** – Implementing a non-blocking running light sequence across an 8-bit LED array using global event flags[cite: 1].
*   **Task 3: Button Input & Software Debounce** – Analyzing mechanical switch bounce, writing a time-based software debounce filter using `HAL_GetTick()`, and dynamically adjusting the `ARR` register at runtime[cite: 1].
*   **Task 4: Multi-Mode LED Control** – Building a complete multi-mode system combining running lights, inverse running lights, and a randomized sparkle state machine driven by multiple buttons[cite: 1].
*   **Task 5: ADC Interrupts & Level Meter** – Interfacing with the ADC to read analog DC voltages and mapping them dynamically onto the LED array as a bar graph[cite: 1].
*   **Task 6: Software PWM via Timer Interrupts** – Generating a custom 100 Hz PWM signal with a 30% duty cycle on pin PB4 using a 10 kHz timer interrupt service routine without dedicated hardware PWM channels[cite: 1].

---

##  Oscilloscope Verification & Waveforms

> **Note on Hardware Validation:** As part of the requirements for this practical, physical waveform measurements were captured to verify timing behavior.

*   <!-- **Task 1: 1 Hz Timer Tick (PB0 Toggle Frequency)** -->
    *   <!-- *Expected Period:* $2.0\text{ s}$ (Toggle frequency of $0.5\text{ Hz}$) -->
    *  <!-- *(Placeholder for Oscilloscope Screenshot: Insert image of PB0 square wave here)* -->
*   <!-- **Task 3: Mechanical Switch Bounce (PA0)** -->
    *   <!-- *Measured Bounce Duration:* $\sim 15\text{ ms}$ to $30\text{ ms}$ single-shot capture window. -->
    *   <!-- *(Placeholder for Oscilloscope Screenshot: Insert single-shot capture of switch contact bounce here)* -->
*   <!-- **Task 6: Software PWM (100 Hz, 30% Duty Cycle on PB4)** -->
    *   <!-- *Measured Period:* $10\text{ ms}$ ($100\text{ Hz}$) -->
    *   <!-- *Measured Positive Pulse Width:* $3\text{ ms}$ ($30\%$ duty cycle) -->
    *  <!-- *(Placeholder for Oscilloscope Screenshot: Insert capture of PB4 PWM waveform here)* -->

---

## Video Demonstrations

Working implementations of the multi-mode LED patterns and hardware responses have been recorded and linked below:

*   **Task 2: Running Light & Multi-Mode Switching Demo**
    *   [Watch the Task 2 Demo on YouTube ](https://youtube.com/shorts/nniuTwCQKKs?feature=share)
---

## Repository Structure

```text
Practical_1/
├── .cproject             <-- IDE project configurations
├── .project              <-- Eclipse project metadata
├── Practical_1.ioc       <-- STM32CubeMX hardware configuration
├── Core/                 <-- Application code source and headers
│   ├── Inc/              <-- Main headers, custom LCD drivers
│   └── Src/              <-- main.c implementation and ISR handlers
└── Drivers/              <-- STM32F0xx HAL and CMSIS files