# Operating Systems Homework 1: Process Simulation Analysis

- **Name:** Hwang, Gyeong-Ryun (黃敬倫)
- **Student ID:** 114502558
- **Department:** CSIE, National Central University
- **Course:** Operating Systems (Spring 2026 - EMI)
- **Tool used:** `process-run.py` (OSTEP Simulator)

---

## 1. Pure CPU Workload Analysis
**Command:** `python3 process-run.py -l 5:100,5:100 -c -p`

- **CPU Utilization:** **100.00%**
- **Analysis:** Since both processes consist of 100% CPU-bound instructions, the CPU remains in the **RUNNING** state throughout the entire execution. There are no I/O operations to trigger idle time, ensuring maximum efficiency.

---

## 2. Impact of Process Ordering & I/O
**Commands:** - Q2: `python3 process-run.py -l 4:100,1:0 -c -p`
- Q3: `python3 process-run.py -l 1:0,4:100 -c -p`

| Metric | Q2 (CPU-bound first) | Q3 (I/O-bound first) |
| :--- | :--- | :--- |
| **Total Time** | 11 Ticks | 7 Ticks |
| **CPU Utilization** | 54.55% | 85.71% |

- **Key Insight:** Ordering matters significantly. In Q3, by starting the I/O-bound process first, the system achieves **I/O Overlap**. While Process 0 is **BLOCKED** waiting for I/O, the scheduler switches to Process 1 to execute CPU instructions, reducing total execution time.

---

## 3. Process Switching Policy (-S)
**Commands:** - Q4: `python3 process-run.py -l 1:0,4:100 -c -S SWITCH_ON_END -p`
- Q5: `python3 process-run.py -l 1:0,4:100 -c -S SWITCH_ON_IO -p`

- **SWITCH_ON_END:** The CPU remains idle while a process waits for I/O, as the system refuses to switch until the current process completes.
- **SWITCH_ON_IO:** This is the preferred policy for modern OSes. It triggers a **context switch** immediately upon an I/O request, allowing other **READY** processes to utilize the CPU.

---

## 4. I/O Completion Strategies (-I)
**Commands:**
- Q6: `python3 process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_LATER`
- Q7: `python3 process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_IMMEDIATE`

- **IO_RUN_LATER:** System resources are underutilized (67.74% CPU Busy). The I/O process must wait in the ready queue after completion, delaying its next I/O burst.
- **IO_RUN_IMMEDIATE:** Utilization reaches **100%**. Running the I/O-bound process immediately after it unblocks allows it to issue the next I/O request as fast as possible, keeping both CPU and Disk busy.

---

## 5. Randomized Process Simulations
**Command Template:** `python3 process-run.py -s [SEED] -l 3:50,3:50 -c -p`

- **Seed 1:** CPU Utilization: 53.33%
- **Seed 2:** CPU Utilization: 62.50%
- **Seed 3:** CPU Utilization: 50.00%

- **Summary:** Randomized traces demonstrate that the mix of instructions and the timing of I/O requests dictate final performance. Lower utilization occurs when CPU-intensive tasks finish early, leaving the CPU idle during remaining long-latency I/O waits.