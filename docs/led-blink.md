# LED Blinking

## Materials
- LED diodes (x1)
- 270 ohm resistors (x1)
- 10K ohm resistor (x1)
- NO Push Button (x1)

## Diagram
![Schamatic](/assets/led_blink_schematic.png)

## C code
```c
#include <16F877A.h>
#fuses xt, nowdt, nolvp, noprotect, nobrownout, put
#use delay(clock=4M)

#use fast_io(B)

// Define LED pin.
#define LED    PIN_B0

void main() {
   // Setting port B as output.
   set_tris_b(0x00);
   
   // Cleaning port b.
   output_b(0x00);
   
   // Infinite loop.
   for (;;) {
      // Turn on the led.
      output_high(LED);
      
      // Wait 500 ms.
      delay_ms(500);
      
      // Turn off the led.
      output_low(LED);
      
      // Wait 500 ms.
      delay_ms(500);
   }
}
```