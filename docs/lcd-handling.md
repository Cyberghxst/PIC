# Manejo de LCD 16x2
Configurando el PIC debidamente y usando LEDs, imprimir un mensaje
en la pantalla LCD 16x2.

## Materiales
- 1 PIC16F877A (para este caso)
- 1 resistencia de 10K.
- 1 cristal de cuarzo de 4 MHz.
- 2 capacitores cerámicos de 22pF.
- 1 push button normalmente abierto.
- 1 LCD 16x2

## Esquemático
![Schamatic](/assets/lcd_schematic.png)

## Código en C
```c
#include <16F877A.h>
#fuses xt, nowdt, nolvp, noprotect, nobrownout, put
#use delay(clock=4M)

// Define LCD pins.
#define LCD_RS_PIN      PIN_B0
#define LCD_RW_PIN      PIN_B1
#define LCD_ENABLE_PIN  PIN_B2
#define LCD_DATA4       PIN_B4
#define LCD_DATA5       PIN_B5
#define LCD_DATA6       PIN_B6
#define LCD_DATA7       PIN_B7

#include "LCD.c" // Librería incluída en PIC C / CCS C

void main() {
   // Init the LCD.
   lcd_init();
   for (;;) {
   
      // Go to the row 1, column 1.
      lcd_gotoxy(1, 1);
   
      // Print a message in the last location.
      lcd_putc("HOLA MUNDO");
   
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