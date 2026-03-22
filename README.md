# ⏱️ APB-Based Timer IP

An APB-compliant Timer IP built from scratch with programmable control registers, prescalers, and interrupt generation logic. I built this side project to deeply understand the AMBA APB protocol and the coverage-driven verification flow. My main focus was on achieving 100% functional and code coverage.

### 📦 Technologies

* **RTL Design:** Verilog
* **Protocol:** AMBA APB
* **Verification:** QuestaSim, ModelSim
* **Environment & OS:** Linux, VIM
* **Methodology:** Coverage-Driven Verification, VPLAN, Testcase Lists (pat.list)

### ⚙️ IP Features

Here's what this Timer IP can do:

* **APB Protocol Interface:** Fully compliant with the AMBA APB standard for read/write register operations.
* **Programmable Prescaler:** Allows scaling of the input clock to generate various timer tick rates.
* **Interrupt Generation:** Generates a stable interrupt signal when the timer reaches zero or a target value.
* **Control Registers:** Configurable registers to start, stop, and clear the timer on the fly.

### 🦉 The Process

I started by analyzing the specification and drawing the hardware architecture. Then, I wrote the RTL code in Verilog, ensuring the logic was synthesizable and clean. 

Next, I focused heavily on the verification phase. Instead of writing random tests, I established a comprehensive **Verification Plan (VPLAN)** mapping out all possible scenarios. 

To make testing efficient, I developed an automated Testbench environment. I created a Testcase List (`pat.list`) to run regression testing overnight. I used QuestaSim to simulate the design, debug waveforms, and track coverage metrics. 

Every time I found a bug through the waveform, I traced it back to the RTL, fixed it, and re-ran the regression. Finally, I kept adding edge-case scenarios until I hit the golden metric: 100% Code Coverage across the board with zero mismatches against the Golden Model.

### 📚 What I Learned

During this project, I significantly improved my hardware thinking and debugging skills:

* **AMBA Protocols:** I deeply understood the handshaking mechanism of the APB protocol (PSEL, PENABLE, PREADY) and how to design bus interfaces.
* **Coverage-Driven Verification:** I learned that verification isn't just about passing tests, but about proving you've tested everything. Analyzing branch and toggle coverage taught me how to find "dead code" in my RTL.
* **Linux & VIM Power:** Writing and debugging code entirely in a Linux terminal using VIM and Makefiles drastically improved my productivity.

### 📊 Verification Results & Artifacts

*(Replace the links below with the actual paths to your images in the `images` folder)*

**1. Architecture Block Diagram**
![Block Diagram](images/block_diagram.png)

**2. Simulation Waveform (QuestaSim)**
![Waveform](images/waveform.png)
*Snippet showing the APB read/write transactions and interrupt firing.*

**3. Code Coverage Report (100%)**
![Coverage Report](images/coverage.png)
*Achieved 100% in Statement, Branch, Expression, Condition, and Toggle coverage.*

### 💭 How can it be improved?

* Upgrade the interface from APB to AHB-Lite for faster burst transactions.
* Implement a Watchdog mode for system reset capabilities.
* Migrate the Verilog testbench to a standard UVM (Universal Verification Methodology) environment.
