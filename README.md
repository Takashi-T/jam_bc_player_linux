# jam_bc_player_linux

Jam STAPL byte-code player for modern 64bit Linux
-------------------------------------------------

This is a Linux port of ALTERA's Jam STAPL byte-code player (2.3.2)
for 64bit Linux systems.

This source code assumes GPIO pins are available on the hardware.
If not, you need to modify the hardware dependent layer defined in
jbistub.c file.



Hardware
--------

The hardware dependent file jbistub.c uses SoC's GPIO port.
The "gpiochip" and "line" parameters for each JTAG signal are
defined near the top of the file.

GPIO driver
-----------

Linux libgpiod (ver 2) is required. Please note that the ver 2 API is
totally different from the ver 1 API. So, make sure your Linux has the
ver 2 library.

Bug fixes
---------
The following bugs and 32bit CPU specific coding are fixed.

* The byte-code file headers are correctly read in 4 bytes as
  in the spec, instead of reading sizeof(int) bytes which can be
  4 or 8 depending on the CPU.
* Heap memory management bugs are fixed to avoid process abort during
  free(). They include address alignment issue (4 vs 8 bytes) and
  array indexing (begins with 0 vs 1).
* Add more ifdef statements to avoid errors due to Windows/DOS specific
  include files.
  


Test
----

The program is tested under the following environment.

* CPU: NXP's iMX8MP SoC (arm64 architecture)
* OS: Linux 6.6.23 (Yocto Scarthgap with libgipiod-dev recipe enabled)

