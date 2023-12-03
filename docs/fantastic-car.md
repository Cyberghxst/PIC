# Auto Fantástico
Configurando el PIC debidamente y usando LEDs, recrear el efecto del
auto fantástico.

## Materiales
- 7 Diodos LED de cualquier color.
- 7 resistencias de 270.
- 1 PIC16F877A (para este caso)
- 1 resistencia de 1K.
- 1 cristal de cuarzo de 4 MHz.
- 2 capacitores cerámicos de 22pF.
- 1 push button normalmente abierto.

## Esquemático
![Schamatic](/assets/fantastic_car_schematic.png)

## Código en C
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