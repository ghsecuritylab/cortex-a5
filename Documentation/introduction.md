# Introduction

This document will contain some general information of the processor, including startup routines and programming methodes.

## Chip startup

The Cortex-A5 allways boots from ROM at address 0x00000000. This executable is known as the first level bootloader. The chip can either start in to different modes

- Standard boot mode
- Secure boot mode

By default the chip starts in default boot mode. The first level bootloader does some basic initialization involving

- Clock configuration 12 MHz external or PLL
- Try to retrieve valid code from any NVM (non-volatile memory)
- Start USB stack and execute the SAM-BA monitor

If the first level bootloader finds a valid application in any NVM is copys the code into SRAM, restores the reset values for all the peripherals, performs **REMAP** and set the PC (program-counter) to 0 to jump to the SRAM application.
 
#### REMAP
REMAP is the process where the addresses in the internal memory is beiing remapped. At startup the ROM memory section starts at 0x00000000 and ends at 0x00200000 where the internal SRAM begins. After executing the remap, the SRAM section starts at address 0x00000000.

#### Valid image detection
The first level bootloader analyzes the first 28 bytes of the NVM, that is the ARM exception vectors. The sixth vector describes the size of the image to download.

#### Example
The image that the bootloader loads into SRAM is often named boot.bin and is the second stage bootloader. In our case it is the at91bootstrap and the secth vector says that it is 17 KB in size. It must **NOT** exceed 64 KB.

#### SAM-BA
If the processor fails to retreive any valid information from any of the connected NVM the processor resets all used peripherals, and tries to establish USB connection. If it can, it executes the SAM-BA monitor, and is in idle state waiting for commands.
