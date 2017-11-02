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
```diff
diff --git Makefile Makefile
index 4581f9a..66a3be5 100644
--- Makefile
+++ Makefile
@@ -24,8 +24,8 @@ clean:
 	rm -rf $(FILENAME_PREFIX).bin $(FILENAME_PREFIX).elf
 
 install:
-	openocd -f interface/stlink-v2-1.cfg -f rtl8710.ocd -f cortex/cortex.ocd -c "init" -c "reset halt" -c "load_image $(FILENAME_PREFIX).bin $(ADDRESS) bin" -c "cortex_bootstrap $(ADDRESS)" -c "shutdown"
+	openocd -f interface/stlink-v2.cfg -f rtl8710.ocd -f cortex/cortex.ocd -c "init" -c "reset halt" -c "load_image $(FILENAME_PREFIX).bin $(ADDRESS) bin" -c "cortex_bootstrap $(ADDRESS)" -c "shutdown"
 
 reset:
-	openocd -f interface/stlink-v2-1.cfg -f rtl8710.ocd -c "init" -c "reset halt" -c "reset run" -c "shutdown"
+	openocd -f interface/stlink-v2.cfg -f rtl8710.ocd -c "init" -c "reset halt" -c "reset run" -c "shutdown"
```
7) Change blinking GPIO to RtlDuino's onboard LED:
```diff
diff --git main.c main.c
index f32c716..b18b811 100644
--- main.c
+++ main.c
@@ -8,12 +8,12 @@ void delay(uint32_t delay){
 int main(){
 	*(volatile uint32_t *)0x4000021C |= (((uint32_t)1) << 8); // REG_SOC_PERI_FUNC1_EN |= BIT_PERI_GPIO_EN; (enable gpio peripheral)
 
-	*(volatile uint32_t *)0x40001004 |= (((uint32_t)1) << 8); // GPIO_PORTA_DDR |= ...???; (set gpio to output, GC4 on B&T RTL-00)
+	*(volatile uint32_t *)0x40001004 |= (((uint32_t)1) << 6); // GPIO_PORTA_DDR |= ...???; (set gpio to output, GC4 on B&T RTL-00)
 
 	while(1){
-		*(volatile uint32_t *)0x40001000 |= (((uint32_t)1) << 8); // GPIO_PORTA_DR |= ...???;   (pin high)
+		*(volatile uint32_t *)0x40001000 |= (((uint32_t)1) << 6); // GPIO_PORTA_DR |= ...???;   (pin high)
 		delay(1000000);
-		*(volatile uint32_t *)0x40001000 &= ~(((uint32_t)1) << 8); // GPIO_PORTA_DR &= ~...???; (pin low)
+		*(volatile uint32_t *)0x40001000 &= ~(((uint32_t)1) << 6); // GPIO_PORTA_DR &= ~...???; (pin low)
 		delay(1000000);
 	}
 }
```
