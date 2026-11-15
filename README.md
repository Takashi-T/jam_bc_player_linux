# jam_bc_player_linux

## Jam STAPL byte-code player for modern 64-bit Linux

This is a Linux port of ALTERA's Jam STAPL byte-code player (2.3.2)
for 64-bit Linux systems.

This source code uses four GPIO pins accessible via the
Linux libgpiod library.
If GPIO pins are not available or you want to use different hardware to generate JTAG signals, you need to modify the hardware-dependent layer defined in the `jbistub.c` file.


## Hardware

This implementation uses the SoC's GPIO ports to drive the
JTAG signals. A total of four GPIO ports are necessary to
drive the JTAG TCK, TMS, and TDI signals and to receive the TDO signal.

The source code uses the libgpiod (version 2) library.
The `gpiochip` and `line` parameters for each JTAG signal are defined
near the top of `jbistub.c`, which contains the hardware-dependent layer.

See the Porting section below if you want to use a different GPIO library or
a different hardware setup to access the JTAG pins.


## GPIO driver

Linux libgpiod (version 2) is required. Note that its API differs
significantly from version 1, so make sure your Linux system has version 2 installed.


## Changes from the original code (bug fixes)

The following bugs and 32-bit-specific assumptions have been corrected:

* 32-bit integer constants (e.g., 0xffff_ffff) in the byte code are correctly
  converted to signed 64-bit integers. Similarly, implicit 32-bit assumptions
  in operations like arithmetic shift
  are correctly converted to 64-bit signed integer results.
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

The program has been tested in the following environment.

* CPU: NXP's iMX8MP SoC (arm64 architecture)
* OS: Linux 6.6.23 (Yocto Scarthgap with the libgpiod-dev recipe enabled)


## Porting
* Hardware-dependent code is in `jbistub.c`.
* If you only want to change the GPIO pin assignments,
  all you need to do is
  modify the five `#define` constants around line 170.
  They are `JTAG_GPIO_CHIP`, `PIN_TCK`, `PIN_TMS`, `PIN_TDI`, and `PIN_TDO`.
  
* If you want to use a different version of the GPIO library or
  a different hardware mechanism, you need to modify the
  following functions
  in `jbistub.c`.

  * `jbi_jtag_io()`: JTAG signal in/out is done in this function. The Linux-specific code is within the `PORT == UNIX` section of the `#if`/`#else`.

  * `init_jtag_gpio()`: This function is called to open the device and initialize the hardware.

  * `close_jtag_gpio()`: This function is called after byte-code playback is done to close the device and perform any other cleanup tasks.

* After porting `jbistub.c`, first check whether you can
read the IDCODE and USERCODE correctly.

