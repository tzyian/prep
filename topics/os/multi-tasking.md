### What’s the difference between preemptive and cooperative multitasking?
- **Preemptive multitasking**:  
  - The OS scheduler decides when a process/thread should be interrupted and another scheduled.  
  - Ensures fair CPU allocation.  
  - Examples: modern OS (Linux, Windows, macOS).  

- **Cooperative multitasking**:  
  - Each process voluntarily yields control of the CPU.  
  - If a process misbehaves (never yields), the whole system may freeze.  
  - Example: early versions of Windows and Mac OS.