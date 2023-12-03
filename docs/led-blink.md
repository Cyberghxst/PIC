# Parpadeo de un LED.
Configurando el PIC debidamente y usando LEDs, encender y apagar
un led de manera infinita.

## Materiales
- 1 Diodos LED de cualquier color.
- 1 resistencias de 270.
- 1 PIC16F877A (para este caso)
- 1 resistencia de 10K.
- 1 cristal de cuarzo de 4 MHz.
- 2 capacitores cerámicos de 22pF.
- 1 push button normalmente abierto.

## Esquemático
![Schamatic](/assets/led_blink_schematic.png)

## Código en C
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