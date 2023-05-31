# XNE Charger Bootloader

## Details

Bootloader looks for {FW_PATH} on SD card. If found it will be written to MCU flash memory beginning at address {APP_START_ADDR}. Once the binary image is written to memory, a `~` is appended to the filename indicating that the upgrade was successful. If unable to start app, LED1 will blink while LED2 remains solid.a

Once update is finished, or if no update file is detected, the bootloader jumps to {APP_START_ADDR} to start the application.

FW_PATH [default: /app.bin]
- defined in `/Core/Inc/ota_sdcard.h`
- specifi2es the pathname of the firmware file located on the SD card that should be written application memory

APP_START_ADDR [default: 0x8010000]
- defined in `STM32G0B1RETXN_FLASH.ld`
- APP_START_ADDR = FLASH_BASE + FLASH_APP_OFFSET
- specifies the address of the application code

## Project Setup

Project dev log

### Using STM32CubeIDE:
1) File > New > STM32 Project
2) MPU/McU Selector (Tab) > Commercial Part Number > Enter "STM32G0B1RET6N"
3) Click [Next] button
4) Project Name > Enter "bootloader"
5) Options
  - Language: C
  - Binary Type: Executable
  - Project Type: STM32Cube
6) Click [Finish] button

### Device Config
- Open `bootloader.ioc`
- Configure pins according to schematic (/Docs/r3_controller.pdf)
- Add FATFS middleware

### Source Code Config
- implement functions in `/FATFS/Target/user_diskio.c`
  -- using public domain SPI library from ChaN (`/Core/Src/user_diskio_spi.c`)
- implement firmware load from file
(`/Core/Src/ota_sdcard.c`)

### Linker Config
- Open `STM32G0B1RETXN_FLASH.ld`
- Change FLASH length to 64K in MEMORY section

### Building
Change build config to output binary file when building.
Properties > C/C++ Build > Settings > MCU Post build options
- [check] Convert to binary file (-O binary)

Binary file file will be output to `Release/bootloader.bin`