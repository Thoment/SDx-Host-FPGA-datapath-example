# SDx-Host-FPGA-datapath-example
Datapath between host and FPGA(Alveo U200) via OpenCL and AXI channel. This is extracted from a larger project so there are some other files.

This example project implements the process of reading or writing data from host to FPGA via OpenCL and AXI channel.

## How to use
1. Download test.sdx.zip from
https://drive.google.com/file/d/1fYg4aPnVbIakujECRQJ3pmVg-qrPBg9d/view?usp=sharing

2. Open SDx
select File -> Import
choose Xilinx -> SDx Project, then click Next
choose SDx project exported zip file, click Next
select test.sdx.zip in the file Browser as Archieve File, select test and click Finish

## Project Overview
In src sdx_rtl_kernel -> sdx_kernel_wizard_0, you can find host_example.cpp and sdx_kernel_wizard_0.xo
### host_example.cpp
Here host_example.cpp uses OpenCL to pass arguments to FPGA and upload binary file. You should create a binary file and fopens its directory.
Four AXI pointer are created and passed to FPGA. (In this version, only weights are used. A previous version implements Output = Weights * Inputs + Bias, you can modify the interfaces in host_example.cpp and FPGA kernel to implement that.)
To write to FPGA, first use fopen to open a binary file, fread it and clEnqueueWriteBuffer it to FPGA. Reading from FPGA is opposite with clEnqueueReadBuffer and fwrite.

### RTL Kernel sdx_kernel_wizard_0.xo





## There is also a memory latency and cycles of this reading/writing process.

Memory test for reading 16KB features in different granularties and orders:

| Granularities | Periods | Time | Delay(Periods) | Delay(Time) | Bandwidth |
| --------- | --------- | --------- | --------- | --------- | --------- |
| 16KB*1 | 330 | 1.1 us | \/ | \/ | 13.87GB/s |
| 1KB out of order | 360 | 1.20us | 90 | 300ns | 12.72GB/s |
| 1KB in order | 360 | 1.20us | 90 | 300ns | 12.72GB/s |
| 256B out of order | 1045 | 3.48us | 75 | 250.0ns | 4.38GB/s |
| 256B in order | 1108|3.69us | 76 | 253.3ns | 4.13GB/s |
| 64B out of order | 3838 | 12.79us | 72 | 240ns | 1.19GB/s |
| 64B in order | 3796 | 12.65us | 72 | 240ns | 1.21GB/s |
| 16B out of order | 15120 | 50.40us | 72 | 240ns | 310MB/s |
| 16B in order | 15210 | 50.70us | 72 | 240ns | 308MB/s |
| 4B out of order | 60530 | 201.8us | 72 | 240ns | 77.4MB/s |
| 4B in order | 62547 | 208.5us | 72 | 240ns | 74.9MB/s |
