# LED con botón.
Configurando el PIC debidamente y usando LEDs, encender y apagar
un led a través de un botón.

## Materiales
- 1 Diodos LED de cualquier color.
- 1 resistencias de 270.
- 1 PIC16F877A (para este caso)
- 1 resistencia de 10K.
- 1 cristal de cuarzo de 4 MHz.
- 2 capacitores cerámicos de 22pF.
- 2 push button normalmente abierto.

## Esquemático
![Schamatic](/assets/led_button_schematic.png)

## Código en C
```c
#include <16F877A.h>
#fuses xt, nowdt, nolvp, noprotect, nobrownout, put
#use delay(clock=4M)

#use standard_io(B)
#use fast_io(D)

// Define LED pin.
#define LED    PIN_B0
// Define PUSH pin.
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
         delay_ms(200); // Anti-rebote.
         output_high(LED);
      } else {
         output_low(LED);
      }
   }
}
```