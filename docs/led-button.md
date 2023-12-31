# LED Diode Control With Button

## Materials
- LED diodes (x1)
- 270 ohm resistors (x1)
- 10K ohm resistor (x1)
- NO Push Button (x2)

## Diagram
![Schamatic](/assets/led_button_schematic.png)

## C code
```c
#include <16F877A.h>
#fuses xt, nowdt, nolvp, noprotect, nobrownout, put
#use delay(clock=4M)

#use standard_io(B)
#use fast_io(D)

// Defines LED pin.
#define LED    PIN_B0
// Defines PUSH pin.
#define PUSH   PIN_D0

void main() {
   // Setting port B as output.
   set_tris_b(0x00);
   
   // Setting port D as input.
   set_tris_d(0xFF);
   
   // Cleaning port b.
   output_b(0x00);
   
   // Infinite loop.
   for (;;) {
      if (input(PUSH) == 1) {
         delay_ms(200); // Debouncing.
         output_high(LED);
      } else {
         output_low(LED);
      }
   }
}
```