### Project Overview & Technical Description
*(Source code is private due to academic/IP restrictions. Below is the detailed architectural and functional breakdown of the design.)*

This project implements a **Direct Memory Access (DMA) Master controller** compliant with the AMBA AHB (Advanced High-performance Bus) protocol. The primary objective of this module is to offload data transfer tasks from the main CPU by autonomously managing **memory-to-memory** data transactions with high efficiency.

### Key Features & Implementation Details
To achieve high throughput and reliable data transfer, I designed and implemented the following core components:

* **AHB Interface Integration:** Engineered the master interface to fully comply with AMBA AHB specifications, managing signals such as `HADDR`, `HTRANS`, `HBURST`, `HSIZE`, and handling the `HREADY` response correctly.
* **Versatile Transfer Modes:** * Implemented control logic to support **SINGLE** transfers for isolated data movements.
    * Successfully designed and verified **INCR4 (4-beat incrementing burst)** transfers to maximize bus utilization and speed up large block transfers.
* **FSM-Based Bus Control:** Developed a robust, multi-state Finite State Machine (FSM) to orchestrate the complex timing of the AHB protocol. The FSM precisely controls bus request (`HBUSREQ`), address phase pipelining, and data phase execution.
* **Internal Data Buffering (FIFO):** Integrated a synchronous **FIFO** to temporarily store data read from the source address before writing it to the destination. This ensures seamless memory-to-memory transfers and prevents data loss when the destination slave inserts wait states (pulls `HREADY` low).

### Design Challenges & Solutions
*(Ghi chú: Thêm phần này vào sẽ cực kỳ "ăn tiền", chứng tỏ ông thực sự bắt tay vào làm và fix bug)*

1. **Handling AHB Pipelined Architecture:**
   * *Challenge:* AHB protocol overlaps the address phase of the current transfer with the data phase of the previous one. Aligning the internal FIFO read/write pointers with this pipelined nature was tricky, especially during burst transfers.
   * *Solution:* I implemented a secondary FSM specifically to track the pipelined stages, ensuring that the address generator increments only when the previous data phase is acknowledged by `HREADY = 1`.
2. **FIFO Overrun/Underrun Prevention:**
   * *Challenge:* Managing burst reads into the FIFO while the AHB write operation is delayed.
   * *Solution:* Added threshold flags (`almost_full`, `almost_empty`) within the FIFO logic to throttle the AHB read requests dynamically, guaranteeing data integrity.

## Results & Verification
*(Please refer to the `results/` folder for visual documentation)*

I developed a comprehensive SystemVerilog/Verilog testbench to stress-test the FSM and FIFO logic. The verification covered corner cases including back-to-back INCR4 bursts and randomized slave wait states.

* **[Image/Link to Block Diagram]**: Shows the datapath between the AHB Master interface, the FSM controller, and the internal FIFO.
* **[Image/Link to Waveform]**: Simulation waveform highlighting a successful INCR4 memory-to-memory transfer, demonstrating correct `HTRANS` (NONSEQ to SEQ) transitions and `HREADY` handling.
