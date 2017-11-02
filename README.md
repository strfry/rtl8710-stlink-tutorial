# Program rtl8170 (RtlDuino) with STM32 Discovery Board

1) Disconnect CN3 Jumpers (inside 'ST-LINK DISCOVERY' label)
2) Connect to SWD (CN2) pins with jumpwires to rtlduino board (see rtlduino-pinout.png):
	Pin 2 <> CLK
	Pin 3 <> GND
	Pin 4 <> TMS
	(Pin 5) <> TRST
3) Connect any 5V pin on STM board to 5VDD
4) Install OpenOCD >= version 0.9, and install gcc-arm-none-eabi
5) git checkout https://bitbucket.org/rebane/rtl8710/src
6) Replace in Makefile "interface/stlink-v2-1.cfg" with "interface/stlink-v2.cfg"
7) Change blinking GPIO to RtlDuino's onboard LED:
 main.c:
	Replace in references to GPIO_PORTA_* (last 3 occurences):
	(((uint32_t)1) << 8) -> (((uint32_t)1) << 6)
