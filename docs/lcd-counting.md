# Counting With LCD 16x2

## Materials
- LED diodes (x7)
- 270 ohm resistors (x7)
- 10K ohm resistor (x1)
- NO Push Button (x1)
- LCD 16x2 (x1)

## Diagram
![Schamatic](/assets/lcd_count_schematic.png)

## C code
```c
#include <16F877A.h>
#fuses xt, nowdt, nolvp, noprotect, nobrownout, put
#use delay(clock=4M)

// Defines LCD pins.
#define LCD_RS_PIN      PIN_B0
#define LCD_RW_PIN      PIN_B1
#define LCD_ENABLE_PIN  PIN_B2
#define LCD_DATA4       PIN_B4
#define LCD_DATA5       PIN_B5
#define LCD_DATA6       PIN_B6
#define LCD_DATA7       PIN_B7

// Defines push pin.
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
         delay_ms(200); // Debouncing.
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