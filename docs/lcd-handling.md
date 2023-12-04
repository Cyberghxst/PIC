# LCD 16x2 handling

## Materials
- LED diodes (x7)
- 270 ohm resistors (x7)
- 10K ohm resistor (x1)
- NO Push Button (x1)
- LCD 16x2 (x1)

## Diagram
![Schamatic](/assets/lcd_schematic.png)

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

#include "LCD.c" // Library included in PIC C / CCS C standard libraries.

void main() {
   // Init the LCD.
   lcd_init();
   for (;;) {
   
      // Go to the row 1, column 1.
      lcd_gotoxy(1, 1);
   
      // Print a message in the last location.
      lcd_putc("HI WORLD");
   
      // Go to the row 1, column 2.
      lcd_gotoxy(1, 2);
   
      // Print a message in the last location.
      lcd_putc("CYBERGHXST");
      
      // Wait 5s.
      delay_ms(5000);
      
      // Clear the display content.
      lcd_putc("\f");
   }
}
```