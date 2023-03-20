# Opensource toolchain tutorial for WinnerMacro W60x

Winner Macro W600 is an embedded Wi-Fi SoC chip which is complying with IEEE802.11b/g/n international standard and which supports multi interface, multi protocol. It can be easily applied to smart appliances, smart home, health care, smart toy, wireless audio & video, industrial and other IoT fields. This SoC integrates Cortex-M3 CPU, Flash, RF Transceiver, CMOS PA, BaseBand. It applies multi interfaces such as SPI, UART, GPIO, I2C, PWM, I2S, 7816. It applies multi encryption and decryption protocol such as PRNG/SHA1/MD5/RC4/DES/3DES/AES/CRC/RSA.

For more information about W600 chip, please refer to the [spec](https://github.com/cjacker/wm-sdk-w60x/blob/main/Doc.EN/W600_Specification_V1.0.2_EN.pdf).

I haven’t seen many devices using this chip in the market, so I’m not sure about its market share. There are limited options of development boards available for purchase on Aliexpress, most of them were made by thingsturn.com, include TW-01, a pin-to-pin replacement of ESP8266 WiFi module.



# Hardware prerequiest
- A W600 devboard
  + I will use TB-01 devboard and TW-01 WiFi module made by Thingsturn.com in this tutorial.
- DAP-Link/JLink Debugger

# Toolchain overview
- Compiler: arm-none-eabi GNU Toolchain
- SDK: wm-sdk-w60x
- Programming tool: wm_tool integrated in wm-sdk-w60x
- Debugging: w60x-openocd / gdb

