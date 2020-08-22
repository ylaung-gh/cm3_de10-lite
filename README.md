# Running ARM Cortex-M3 on Terasic DE10-Lite with Intel Max 10 FPGA

This guide describes how to get ARM Cortex-M3 based system-on-chip design running on Treasic DE10-Lite with Intel Max 10 FPGA. This design is the adaption of the design as provided with System-on-Chip Design with ARM Cortex-M Processors by Joseph Yiu.


## Topics we'll be covering:

- [Hardware Required](https://github.com/ylaung-gh/cm3_de10-lite#hardware-required)
- [Software Required](https://github.com/ylaung-gh/cm3_de10-lite#software-required)
- [Overall System Architecture](https://github.com/ylaung-gh/cm3_de10-lite#overall-system-architecture)
- [Xilinx IPs to Intel](https://github.com/ylaung-gh/cm3_de10-lite#xilinx-ips-to-intel)
- [Clock Consideration](https://github.com/ylaung-gh/cm3_de10-lite#clock-consideration)
- [Reset Consideration](https://github.com/ylaung-gh/cm3_de10-lite#reser-consideration)
- [Verilog Primitives](https://github.com/ylaung-gh/cm3_de10-lite#verilog-primitives)
- [ITCM Memory Initialization](https://github.com/ylaung-gh/cm3_de10-lite#itcm-memory-initialization)
- [Serial Wire Debug Interface](https://github.com/ylaung-gh/cm3_de10-lite#serial-wire-debug-interface)
- [Live Debug and Download of Software](https://github.com/ylaung-gh/cm3_de10-lite#live-debug-and-download-of-software)
- [Demo Video](https://github.com/ylaung-gh/cm3_de10-lite#demo-video)

# Hardware Required

- Terasic DE10-Lite Board [DE10-Lite](https://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=218&No=1021)
- CMSIS-DAP for Serial Wire Debug [CMSIS-DAP](https://github.com/L-Tek/DAPLINK/blob/master/docs/cmsis-dap.md)

# Software Required

- Quartus Prime Lite Edition (18.1.0)
- Keil uVision V5.31.0.0

# Overall System Architecture

It is important to understand the top level design structure of ARM Cortex-M3 system (CM3) such as the followings:
1. Clocks - How many external clocks (also speed) are used by the system? What are the internal clocks?
2. Resets - How many resets are used? Active low or high?
3. Inputs/Outputs - What the inputs and outputs (I/O) used by the system? What are the minimum I/Os to test the functionality on the target board (i.e. DE10-Lite)?

# Xilinx IPs to Intel

# Clock Consideration

# Reset Consideration

# Verilog Primitives

# ITCM Memory Initialization

# Serial Wire Debug Interface

# Live Debug and Download of Software

