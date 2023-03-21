# Opensource toolchain tutorial for WinnerMacro W60x

Winner Macro W600 is an embedded Wi-Fi SoC chip which is complying with IEEE802.11b/g/n international standard and which supports multi interface, multi protocol. It can be easily applied to smart appliances, smart home, health care, smart toy, wireless audio & video, industrial and other IoT fields. This SoC integrates Cortex-M3 CPU, Flash, RF Transceiver, CMOS PA, BaseBand. It applies multi interfaces such as SPI, UART, GPIO, I2C, PWM, I2S, 7816. It applies multi encryption and decryption protocol such as PRNG/SHA1/MD5/RC4/DES/3DES/AES/CRC/RSA.

For more information about W600 chip, please refer to the [spec](https://github.com/cjacker/wm-sdk-w60x/blob/main/Doc.EN/W600_Specification_V1.0.2_EN.pdf).

I haven’t seen many devices using this chip in the market, so I’m not sure about its market share. There are very limited options of development boards available for purchase on Aliexpress, most of them were made by thingsturn.com, include TW-01 (ESP8266 pin-to-pin replacement). So I don’t think it’s worth spending a lot of time to learn, the reason that prompted me to write this simple tutorial is that I need to compile the `WM_AT` firmware.

# Hardware prerequiest
- A W600 devboard
  + I will use TB-01 devboard and TW-01 WiFi module made by Thingsturn.com in this tutorial.

# Toolchain overview
- Compiler: arm-none-eabi GNU Toolchain
- SDK: [wm-sdk-w60x](https://github.com/cjacker/wm-sdk-w60x), partial opensource, `wlan.lib` is close source.
- Programming tool: wm_tool integrated in wm-sdk-w60x
- Debugging: **broken**

# Compiler

W600 use 'arm-none-eabi' GCC toolchain since they are based on ARM Cortex-M. It's not neccesary to build the toolchain yourself, there are already a lot of well supported prebuilt release and already widely used by developers. 

[xpack-dev-tools](https://github.com/xpack-dev-tools) provde a prebuilt 'arm-none-eabi' toolchain. you can download it from [here](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/download/v12.2.1-1.2/xpack-arm-none-eabi-gcc-12.2.1-1.2-linux-x64.tar.gz). 

After download:
```
sudo mkdir -p /opt/xpack-arm-none-eabi
sudo tar xf xpack-arm-none-eabi-gcc-12.2.1-1.2-linux-x64.tar.gz -C /opt/xpack-arm-none-eabi --strip-components=1
```
and add /opt/xpack-arm-none-eabi/bin to PATH env.

After toolchain installed, you should comment out below codes in `arm-none-eabi/include/sys/types.h`:

```
#if __BSD_VISIBLE
#include <machine/endian.h>
#include <sys/select.h>
#  define   physadr     physadr_t
#  define   quad        quad_t

#ifndef _IN_ADDR_T_DECLARED
typedef __uint32_t  in_addr_t;  /* base type for internet address */
#define _IN_ADDR_T_DECLARED
#endif

#ifndef _IN_PORT_T_DECLARED
typedef __uint16_t  in_port_t;
#define _IN_PORT_T_DECLARED
#endif

typedef __uintptr_t u_register_t;
#endif /* __BSD_VISIBLE */
```
Otherwise it will cause some "redefined" errors.

NOTE, the triplet of xpack prebuilt toolchain is **arm-none-eabi**.

# SDK

The official SDK of W60x can be downloaded from https://www.winnermicro.com/upload/1/editor/1584693446529.zip, the latest version is `G3.04`. According to the release date, the version number `G3.04` also refers to `G3.4`.

I put a copy with minor fixes (include correcting file permission, add more examples and add English documents) in [wm-sdk-w60x](https://github.com/cjacker/wm-sdk-w60x) repo, I recommend to use this copy directly.

```
git clone https://github.com/cjacker/wm-sdk-w60x.git
cd wm-sdk-w60x
make -C Tools/GNU
```

The fireware file `wm_w600_gz.img` and other related files will be genereated at `Bin` dir.

If you use device with 2M flash, you should change `USE_2M_FLASH = 0` to `USE_2M_FLASH = 1` in `Tools/GNU/Makefile`.

By default, the firmware will be built as `WM_AT` firmwares. WM_AT is something like ESP-AT, for more usage about `WM_AT`, please refer to official "[AT command user manual](https://github.com/cjacker/wm-sdk-w60x/blob/main/Doc.EN/WM_W60X_SDK_AT_Command_V1.0.3.pdf)".

If you want to build your own codes or build some examples, please copy `main.c` from examples to `App` dir and build the project again.


# Programming

After firmware built, program it to target device by:

```
make -C Tools/GNU flash
```
First time programming, it will inform you:
```
unable to download because no serial port is configured.
please open the download_img.sh file (e.g: vim ./download_img.sh),
and configure SERIAL_NAME to the serial port number you are using.
```

You need change `SERIAL_NAME=` to `SERIAL_NAME=<your device>` in `Tools/GNU/download_img.sh`, `your device` usually is `ttyUSB0`.

Or run `wm_tool` manually:
```
./Tools/wm_tool -c ttyUSB0 -ds 2M -dl Bin/wm_w600_gz.img -ws 115200 -rs at
```

# Debugging
**Broken**
W600 debugging can be supported by upstream OpenOCD and forked w60x-openocd v0.10, but as I tried, both not works. 
I tried it with DAP-Link, ST-Link and JLink debugger :
- Firmware flashing only works with JLink.
- When encountering a breakpoint, the program does not stop running.

I don’t have much interest to fix it. This tutorial will be updated if this issue fixed.

Pinmap:
| W600 | JTag/SWD  |
|------|-----------|
| PB6  | TMS/SWDIO |
| PB7  | TCK/SWCLK |
| GND  | GND       |
| 3V3  | 3V3/VREF  |

