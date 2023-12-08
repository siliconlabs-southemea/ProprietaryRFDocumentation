---
sort: 4
---

## How to call the bootloader

  1.  using the UART CLI interface type:

  ```sh
  > bootload
  ```
  2. the EFR32 immediately reboots in bootloader mode. If you flashed the Xmodem bootloader you should get a prompt menu as this one :

  ```sh
  Gecko Bootloader v2.01.00
  1. upload gbl
  2. run
  3. ebl info
  ```
  3. you can now type 1 and use a xmodem transfer to load the updated firmware.