# Contador con LCD 16x2
Configurando el PIC debidamente y usando LEDs, contar y mostrar el número
en la pantalla LCD 16x2.

## Materiales
- 1 PIC16F877A (para este caso)
- 1 resistencia de 10K.
- 1 cristal de cuarzo de 4 MHz.
- 2 capacitores cerámicos de 22pF.
- 2 push button normalmente abierto.
- 1 LCD 16x2

## Esquemático
![Schamatic](/assets/lcd_count_schematic.png)

## Código en C
```c
#include <16F877A.h>
#fuses xt, nowdt, nolvp, noprotect, nobrownout, put
#use delay(clock=4M)

// Defining LCD pins.
#define LCD_RS_PIN      PIN_B0
#define LCD_RW_PIN      PIN_B1
#define LCD_ENABLE_PIN  PIN_B2
#define LCD_DATA4       PIN_B4
#define LCD_DATA5       PIN_B5
#define LCD_DATA6       PIN_B6
#define LCD_DATA7       PIN_B7

// Defining push pin.
#define PUSH            PIN_D0

#include "LCD.c"

void main() {
   // Init the LCD.
   lcd_init();
   
   // Go to the row 1, column 1.
   lcd_gotoxy(1, 1);
   
   // Print a message in the last location.
   lcd_putc("CONTADOR");
   
   // Variable for counting.
   int i = 0;
   
   // Infinite loop.
   for (;;) {
      if (input(PUSH) == 1) {
         delay_ms(200); // Anti-rebote.
         i++;
         if (i > 127) {
            i = 0;
            lcd_putc("\f");
            lcd_gotoxy(1, 1);
            lcd_putc("CONTADOR");
         }
      }
      lcd_gotoxy(1, 2);
      printf(lcd_putc, "%d", i);
   }
}
```