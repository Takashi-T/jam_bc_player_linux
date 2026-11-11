# jam_bc_player_linux

## Jam STAPL byte-code player for modern 64bit Linux

This is a Linux port of ALTERA's Jam STAPL byte-code player (2.3.2)
for 64bit Linux systems.

This source code uses four GPIO pins accessible via the
Linux libgpiod library.
If GPIO pins are not available or you want to use different hardware to generate JTAG signals, you need to modify the hardware dependent layer defined in `jbistub.c` file.


## Hardware

This implementation uses SoC's GPIO ports to drive the
JTAG signals. A total of four GPIO ports are necessary to
drive JTAG TCK, TMS, TDI signals and to receive TDO signal.

The `gpiochip` and `line` parameters for each JTAG signal are defined
near the top of `jbistub.c`, the hardware-dependent layer.


## GPIO driver

Linux libgpiod (version 2) is required. Note that its API differs
significantly from version 1, so make sure your Linux system has version 2 installed.


## Changes from the original code (bug fixes)

The following bugs and 32-bit-specific assumptions have been corrected:

* 32bit integer constants (e.g., 0xffff_ffff) in the byte code are correctly
  converted to signed 64bit integers. Similarly, implicit 32bit assumptions
  in operations like arithmetic shift
  are correctly converted to 64bit signed integer results.
* Byte-code file headers are read as exactly 4 bytes, as specified,
  rather than using `sizeof(int)`. The size of `int` can vary by platform,
  so relying on it could result in an incorrect header read.
* Heap-memory handling has been corrected to prevent process aborts during
  `free()`. The fixes address memory-alignment assumptions (4-byte versus
  8-byte alignment) and array indexing, which must start at index 0 rather
  than index 1.
* Additional `#ifdef` guards prevent compilation errors caused by
  Windows- and DOS-specific include files when building on Linux.
  

## Test

The program is tested under the following environment.

* CPU: NXP's iMX8MP SoC (arm64 architecture)
* OS: Linux 6.6.23 (Yocto Scarthgap with libgipiod-dev recipe enabled)


## Porting

When you port the jbistub.c, first check if actions CHECK_IDCODE and/or READ_USERCODE
work correctly.
