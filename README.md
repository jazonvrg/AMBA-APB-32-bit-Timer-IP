## Project Overview & Technical Description
*(Source code is private due to IP restrictions. Below is the detailed architectural and functional breakdown of the design.)*

This project implements a highly configurable, 64-bit Timer IP equipped with an AMBA APB (Advanced Peripheral Bus) interface. The architecture is customized based on the Core Local Interruptor (CLINT) module commonly found in industrial RISC-V architectures. It is designed to provide precise timing intervals, manage system delays, and generate hardware interrupts.

### Key Features & Implementation Details
I designed the RTL logic to support advanced operational modes, moving beyond basic counting capabilities:

* **Advanced APB Slave Interface:**
    * Fully compliant with the APB protocol, supporting a 12-bit address space and 32-bit data transfers (TDR0, TDR1, TCMP0, TCMP1, etc.).
    * **Byte Access Support:** Implemented logic to allow the bus master to access individual bytes within the 32-bit registers, utilizing the PSTRB signal.
    * **Wait States & Error Handling:** Engineered the interface to support 1-cycle wait states (PREADY manipulation) to optimize timing closure. Robust error handling (PSLVERR) is implemented to block illegal writes (e.g., modifying divisor values while the timer is active).
* **64-bit Count-up Mechanism:**
    * Features a 64-bit counter that can operate at the core system clock frequency or be dynamically divided (up to /256) via the TCR.div_val configuration.
    * Hardware-automated reset: When timer_en transitions from High to Low, the counter automatically clears its internal states back to the initial values without requiring a software reset.
* **Hardware Maskable Interrupts:**
    * Generates level-triggered interrupts (tim_int) when the 64-bit counter perfectly matches the 64-bit programmed compare value (TCMP1 & TCMP0).
    * Implemented a strict "Write 1 to Clear" (W1C) mechanism for the interrupt status register (TISR.int_st) to prevent accidental clearing of pending interrupts.
* **Debug / Halted Mode Integration:**
    * Designed a synchronization mechanism to safely halt the 64-bit counter when the system enters debug mode (triggered by the external dbg_mode pin and internal THCSR.halt_req).
    * Ensured that upon resuming from a halted state, the timer maintains its exact counting period without losing clock cycles.

### Design Challenges & Solutions
1. **Managing 64-bit Data on a 32-bit APB Bus:**
   * *Challenge:* The APB bus is 32-bit, meaning the 64-bit counter (TDR1:TDR0) and compare registers (TCMP1:TCMP0) must be accessed in two separate transactions. This could lead to a race condition where a timer interrupt fires erroneously between the writing of the lower and upper 32 bits.
   * *Solution:* Implemented an internal shadow register/buffering mechanism to ensure that the 64-bit compare value is only updated and evaluated when both 32-bit halves are completely written.
2. **Handling Corner Cases in Debug Mode:**
   * *Challenge:* Stopping the counter abruptly when dbg_mode asserts could cause glitches if the timer is in the middle of a divided clock cycle.
   * *Solution:* The FSM logic ensures that the halt acknowledge (THCSR.halt_ack) is only asserted safely at the boundary of the current counting operation, preserving the exact phase of the divided clock.

### Verification & Coverage Results
`results/`

To ensure industrial-grade reliability, I developed a complete testbench architecture utilizing a custom APB Bus Functional Model (BFM). 

* Achieved **100% Code Coverage** (Statement, Branch, Expression, Condition, and Toggle) by simulating rigorous corner cases, including randomized wait states and illegal register accesses.

**Waveform: Interrupt Generation & W1C Clearance**
![Waveform Interrupt](./results/waveform_interrupt.png)

**Block Diagram: APB Timer Architecture**
![Block Diagram](./results/block_diagram.png)
