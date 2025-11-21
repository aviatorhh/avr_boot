avr_boot
========

![mega_with_eth](https://github.com/user-attachments/assets/e5e562d8-33d2-43a2-ade2-97f4df358e82)


SD card bootloader for atmega processors, here ATmega2560 especially

This is a fork and mainly concentrates on the show boards above.

I am working on the controllability of the flash operation as it always flashes on reset for the time being. I have a test running where a file gets downloaded from somewhere (big security issue) and then stored on the SD card. That will be checked with CRC and the flashed if needed.

As easy as it can get! I spent days with this. Hopefully you wont!

- for any ATMega with 4096kb Bootloader
- uses Petit FatFs R0.03 for FAT12, FAT16, FAT32
- looks for FIRMWARE.BIN and flashes it nearly instantly
- without any interference to your application
- (no CRC Check and no version bytes in EEPROM (see KISS))

### Boards Manager installation

The original avr_boot is integrated in Arduino IDE version 1.6.4 or greater!!! [See here for instructions.](https://github.com/zevero/avr_boot/tree/gh-pages). Not this one.

### Manual installation

This is with avr-gcc and avrdude under linux with an Atmega2560 and AVRISP mkII! Adaption to your case (WinAvr, another Atmega, another flash-tool) will not be complicated...

- adapt Makefile
  - MCU_TARGET: Your atmegaXXX
  - BOOT_ADR: in bytes not words!
  - F_CPU: CPU Frequency (not critical. A higher value will work as well)
  - SD_CS_PORT: Data Register of the SD CS pin(see the datasheet for your microcontroller)
  - SD_CS_DDR: Data Direction Register of the SD CS pin
  - SD_CS_BIT: Bit of the SD CS pin
  - USE_LED: For debugging 0...deactivate or 1...active
  - LED_DDR: Data Direction Register of the LED
  - LED_PORT: Data Register of the LED
  - LED_BIT: Bit of the LED
  - USE_UART: For debugging 0...deactivate or divider (UBRR) for baudate see http://wormfood.net/avrbaudcalc.php
- update spi_pins.h with the SPI pins of your microcontroller if not already defined
- if using USE_LED adapt LED-pins in asmfunc.S
- if you want to add FAT12 adapt pff/src/pffconfh.h (default ist FAT16 + FAT32)
- if you want to support lower case filenames adapt pff/src/pffconfh.h (default is uppercase)
- if you prefer another filename instead of FIRMWARE.BIN adapt main.c 
- make (you may need to do "sudo apt-get install avr-libc gcc-avr")
- set fuses: avrdude -c avrispmkII -p m2560 -U hfuse:w:0xda:m
  - find high fuse in http://eleccelerator.com/fusecalc/fusecalc.php?chip=atmega2560
- flash: avrdude -c avrispmkII -p m2560 -Uflash:w:./avr_boot.hex:i -Ulock:w:0x3F:m 

### Put your sketch on SD card 

- in Arduino IDE go to File > Preferences and check "Show verbose output during compiliation"
- compile sketch and find the location of your /tmp/buildxxx/sketch.cpp.hex
- make bin file: avr-objcopy -I ihex -O binary sketch.cpp.hex FIRMWARE.BIN
- copy the file into the root of an SD (FAT16/FAT32)
- put it into the SD slot of your ATmega
- reset it
- it might already have happend!

### Bootloader sizes
Compiled for atmega2560 roughly 4k

### Tested successfully on
 - ATmega2560

### Serial support
Not yet implemented

### KISS
If you wish you *can* add CRC Check or versioning with EEPROM *but* I prefere to keep things simple. avr_boot will reflash your FIRMWARE.BIN as long as it is present.
Is this a problem? No! It happens nearly instantly and only differing bytes are flashed really.
You may consider putting your logic into your application and perform a CRC Check after the fact to inform the user and delete or rename FIRMWARE.BIN

### Thanks to
- https://github.com/per1234 - Boards Manager Installation and help with differenct MCUs
- http://elm-chan.org/fsw/ff/00index_p.html
- Wilfried Klaas for the MCSDepthLogger https://github.com/willie68/OpenSeaMapLogger
- https://github.com/mharizanov/avr_boot
- https://github.com/osbock/avr_boot
- and others???
