# Running ARM Cortex-M3 on Terasic DE10-Lite with Intel Max 10 FPGA

This guide describes how to get ARM Cortex-M3 based system-on-chip design running on Treasic DE10-Lite with Intel Max 10 FPGA. This design is the adaption of the design as provided with System-on-Chip Design with ARM Cortex-M Processors by Joseph Yiu.

## Topics we'll be covering:

- [Hardware Required](https://github.com/ylaung-gh/cm3_de10-lite#hardware-required)
- [Software Required](https://github.com/ylaung-gh/cm3_de10-lite#software-required)
- [Overall System Architecture](https://github.com/ylaung-gh/cm3_de10-lite#overall-system-architecture)
- [Getting Started](https://github.com/ylaung-gh/cm3_de10-lite#getting-started)
- [Xilinx IPs to Intel](https://github.com/ylaung-gh/cm3_de10-lite#xilinx-ips-to-intel)
- [Reset Consideration](https://github.com/ylaung-gh/cm3_de10-lite#reser-consideration)
- [Verilog Primitives](https://github.com/ylaung-gh/cm3_de10-lite#verilog-primitives)
- [ITCM Memory Initialization](https://github.com/ylaung-gh/cm3_de10-lite#itcm-memory-initialization)
- [Blinking LEDs Software](https://github.com/ylaung-gh/cm3_de10-lite#blinking-leds-software)
- [Serial Wire Debug Interface](https://github.com/ylaung-gh/cm3_de10-lite#serial-wire-debug-interface)
- [Demo Video](https://github.com/ylaung-gh/cm3_de10-lite#demo-video)

# Hardware Required

- Terasic DE10-Lite Board [DE10-Lite](https://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=218&No=1021)
- `CMSIS-DAP` for Serial Wire Debug [CMSIS-DAP](https://github.com/L-Tek/DAPLINK/blob/master/docs/cmsis-dap.md)

# Software Required

- Quartus Prime Lite Edition (18.1.0)
- Keil uVision V5.31.0.0

# Overall System Architecture

It is important to understand the top level design structure of ARM Cortex-M3 system (CM3) such as the followings:
1. Clocks - How many external clocks (also speed) are used by the system? What are the internal clocks?
2. Resets - How many resets are used? Active low or high?
3. Inputs/Outputs - What are the inputs and outputs (I/O) used by the system? What are the minimum I/Os to test the functionality on the target board (i.e. DE10-Lite)?

# Getting Started

Assuming famalarity with Intel Quartus design tools, create a project for DE10-Lite and add all design files. You will need to register and download obfuscated CM3 from ARM (AT421-MN-80001-r0p0-02rel0) and copy two files as described in [Readme.pdf](https://github.com/ylaung-gh/cm3_de10-lite/blob/master/Readme.pdf).

Then, firstly try synthesizing the top-level design and fix any missing files, etc if any.

# Xilinx IPs to Intel

Synthesis will fail as there are Xilinx specific IP and primitives in the design. The followings explain these and solutions to work around.

1. `BUFG` - In file: `fpga_top_rst_sync.v`, there are two `BUFG` primitives used for `nRST_12MHz` and `nRST_SYSCLK`. `BUFG` is a buffer to drive a clock signal using on-chip clocking resources. FPGA design tools nowadays is able to automatically infer clock signals in the design, synthesize, place and route accordingly. Use a clock constraint in `*.qsf` to further guide the tools. So, one possible workaround for this will be remove these `BUFG` primitives and insert wires instead.

2. `BUFGCE` - In file: `clk_reset_ctrl.v`, there are two BUFGCE primitives. They are used for clock gating. BUFGCE is BUFG with Clock Enable (CE). For this, Intel has `ALTCLKCTRL` IP. So, create and instantiate two of these.

3. Clocking Wizard - In file: `fpga_top.v`, there is a `sys_clk_generator` IP to crate desired clocks from the input base clock. Intel has `ALTPLL` for this. Create and instantiate one `ALTPLL`. Note that the input clock for DE10-Lite is 50 MHz and CM3 system runs at 40 MHz.

# Reset Consideration

Two push-buttons on DE10-Lite are active low. So, check `fpga_top.v` on how the reset signals are applied to various design modules and fix accordingly.

# Verilog Primitives

Another errors encountered during the design synthesis will be the use of `pullup()` primitive. This primitive is not supported in Intel Quartus. `pullup()` primitives are applied on `nSRST`, `nTRST` and `SWDITMS` with tristate signals. Here, `nSRST` is system reset and two others are related to debug interface. The system reset is already active low and hence the `pullup()` from here can be removed. For a quick starter, you may remove the `pullup()` for two other signals. Getting the SWD debug functionality is discussed [here](https://github.com/ylaung-gh/cm3_de10-lite#serial-wire-debug-interface).

# ITCM Memory Initialization

Initialization of Instruction Tightly Coupled Memories (ITCMs) is done by (`itcm0`, `itcm1`, `itcm2` and `itcm3`) files in `AHBBlockRam.v`. These four files are generated by Keil uVision project. Wheneve the software is modified, the ITCM files have to be copied over and updated. For Intel Quartus, these files have to be placed in the same directory where `.qpf` project file is located.

In addition, for MAX 10 FPGA, memory initialization must be explicitly enabled as follows:
`- Assignments -> Device... -> Device and Pin Options... -> Configuration mode: Single Uncompressed Image with Memory Initialization (512Kbits UFM)`

# Blinking LEDs Software

A simple blinking LEDs software is recommended to have a quick test on the functioning of the system. Refer to `testcodes_fpga_blinky_keil_mdk` software project.

# ARM Cortex-M3 on Intel Max 10 FPGA

Congratulations!!! You now have a live ARM Cortex-M3 system with blinking LEDs on Intel Max 10 FPGA perhaps after a bit of troubleshooting.

# Serial Wire Debug Interface

This part is a bit tricky and requires some knowledge of debug interface. As mentioned in [Readme.pdf](https://github.com/ylaung-gh/cm3_de10-lite/blob/master/Readme.pdf), only two wires `SWDIO` and `SWCLK` are used for the SWD debug interface. Instead of `V2C-DAPLINK` board, I used [`CMSIS-DAP`](https://github.com/L-Tek/DAPLINK/blob/master/docs/cmsis-dap.md) board to get the debug functionality. So, three wires (`SWDIO`, `SWCLK` and `GND`) from DE10-Lite are connected to `Pin 1`, `Pin 4` and `Pin 5` on `CMSIS-DAP` board. Here, the pullup on `SWDIO` is important since this is a bi-directional signal. The workaround is to bring the tristate to top-level design and attach a weak pullup resistor in Quartus Assignment Editor.

# Demo Video

So, we now have a live ARM Cortex-M3 system with debug capability on Intel Max 10 FPGA. The demo video can be found [here](https://twitter.com/ylaungsgp/status/1296816931236798470?s=20).

# Author's Comments



