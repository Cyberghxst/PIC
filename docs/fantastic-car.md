# Fantastic Car

## Materials
- LED diodes (x7)
- 270 ohm resistors (x7)
- 10K ohm resistor (x1)
- NO Push Button (x1)

## Diagram
![Schamatic](/assets/fantastic_car_schematic.png)

## C code
```c
#include <16F877A.h>
#fuses xt, nowdt, nolvp, noprotect, nobrownout, put
#use delay(clock=4M)

#byte portb = 6

void main() {
   // Setting port B as output.
   set_tris_b(0x00);
   
   // Cleaning port B.
   portb = 0;
   
   // Turning on the first led.
   portb = 0b00000001;
   
   // Waiting for 50 ms.
   delay_ms(50);
   
   // Infinite loop.
   for (;;) {
      // <-
      while (!bit_test(portb, 7)) {
         portb = portb << 1;
         delay_ms(50);
      }
      
      delay_ms(50);
      
      // ->
      while (!bit_test(portb, 0)) {
         portb = portb >> 1;
         delay_ms(50);
      }
   }
}
```