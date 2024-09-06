# Gas Detector System
I made this for a final project for the "Microcontrollers" subject.

## Materials
- Servomotor SG90 (x4)
- Potentiometer (x4)
- Buzzer (x1)
- LCD 16x2 (x1)
- PIC16F877A (x1)
- LEDs (x2)
- ...everything else in the diagram.

## Diagram
![Schamatic](/assets/gas-detection-system_shematic.png)

## C code
```c
///////////////////////////////////////////////////////////////////////////////
////                    INGENIERIA MECATRÓNICA 7C                          ////
////          Proyecto Final para Microcontroladores 2023B                 ////
////                                                                       ////
////       Sistema de ventilación contra gases controlado por PIC          ////
////                                                                       ////
////                          INTEGRANTES                                  ////
////              B********** d* l* C*** G****** A******                   ////
////                   C******** C*** J*** J*****                          ////
////             C******* C****** F******** J***** J****                   ////
////                   R******* H******** J*** J*****                      ////
////                     V******** Q****** J******                         ////
////                                                                       ////
////  PREMISA                                                              ////
////  Este programa ayudará a las personas que viven cerca de pozos        ////
////  petroleros de los cuales se pueden emitir gases que potencialmente   ////
////  puedan dañar la integridad de las personas de los alrededores.       ////
////  El objetivo de este programa es ayudar a ventilar las viviendas      ////
////  para eludir el gas de la mejor forma posible y evitar su acceso a    ////
////  los hogares en cuestión.                                             ////
////                                                                       ////
////  OBSERVACIONES/DATOS                                                  ////
////  == SERVOMOTORES ==                                                   ////
////  Debido a la librería que se usó para controlar estos motores, los    ////
////  ángulos de denotan de la siguiente forma.                            ////
////  0: Para -90 grados.                                                  ////
////  90: Para 0 grados.                                                   ////
////  180: Para +90 grados.                                                ////
////  Todas estas amplitudes probados con el servo SG90.                   ////
////                                                                       ////
///////////////////////////////////////////////////////////////////////////////
#include <16F877A.h> // Incluye la librería del PIC16F877A.
#device ADC = 10
#include <stdlib.h>

/*    DECLARACIÓN DE FUSIBLES DE CONGIFURACIÓN                  */
#fuses XT         // Declara la utilización de un cristal de <= 4 MHz.
#fuses NOBROWNOUT   // Habilita el reset brownout.
#fuses NOPUT      // Deshabilita el Power-Up-Timer.
#fuses NOWDT      // Deshabilita el "WATCH DOG TIMER".
#fuses NOLVP      // Deshabilita el pin de programación de bajo voltaje.
#fuses NOPROTECT  // Deshabilita la protección del código contra lectura.
/*    FIN DECLARACIÓN DE FUSIBLES DE CONGIFURACIÓN              */

#use delay(clock=4M)  // Declara la velocidad del cristal para los delays.

#use standard_io(A, B, C, D)

// START -> PIN_DEFS:LCD
#define LCD_RS_PIN      PIN_E1
#define LCD_RW_PIN      PIN_E2
#define LCD_ENABLE_PIN  PIN_E0
#define LCD_DATA4       PIN_D4
#define LCD_DATA5       PIN_D5
#define LCD_DATA6       PIN_D6
#define LCD_DATA7       PIN_D7
// END   -> PIN_DEFS:LCD

// START -> PIN_DEFS:ALARM
#define LED             PIN_B0
#define BUZZER          PIN_B1
#define STATUS_LED      PIN_B7
// END   -> PIN_DEFS:ALARM

// START -> PIN_DEFS:SWITCHES
#define ALARM_SWITCH    PIN_C0
#define DISPLAY_SWITCH  PIN_C1
// END   -> PIN_DEFS:SWITCHES

// START -> PIN_DEFS:MOTORS
#define MOTOR_1         PIN_B2
#define MOTOR_2         PIN_B3
#define MOTOR_3         PIN_B4
#define MOTOR_4         PIN_B5
// END   -> PIN_DEFS:MOTORS

/*
// START -> SERVO_DEFS:USING
#define use_servo_1
#define use_servo_2
#define use_servo_3
// END   -> SERVO_DEFS:USING

// START -> PIN_DEFS:SERVOS
#define servo_1         PIN_D0
#define servo_2         PIN_D1
#define servo_3         PIN_D2
// END   -> PIN_DEFS:SERVOS
*/

// Inclusión de la librería de la pantalla LCD.
#include "LCD.c"

/*
// Inclusión de la librería de los servomotores.
#include "servo_st.c"
*/

// Variable para guardar las lecturas del MQ-2.
float sensor_1, sensor_2, sensor_3, sensor_4;

/*
unsigned int voltage_1, voltage_2, voltage_3, voltage_4;
unsigned int ppm_1, ppm_2, ppm_3, ppm_4;
*/

// Variables para controlar las L para sonar.
float limit_raw = 500.0;
// unsigned int limit_ppm = 4000;

// Inicializa el ADC.
void set_ADC() {
   setup_adc(ADC_CLOCK_INTERNAL);            // Configurar el reloj interno del ADC
   setup_adc_ports(AN0_AN1_AN2_AN3_AN4);     // Configurar los pines AN0 a AN4 como entradas analógicas
   set_adc_channel(0);                       // Configura el canal de lectura inicial
   delay_us(20);                             // Pequeña espera antes de iniciar la conversión
}

// Inicializa y limpia los puertos.
void init_ports() {
   set_ADC();
   
   // Setting all port tris.
   set_tris_b(0x00);            // Configura el puerto B como salida.
   set_tris_c(0xFF);            // Configura el puerto C como entrada.
   set_tris_d(0x00);            // Configura el puerto D como salida.
   
   // Cleaning outputs.
   output_b(0x00), output_d(0x00);
}

float read_analog_channel(int channel) {
   set_adc_channel(channel);        // Seleccionar el canal analógico deseado
   delay_ms(20);                    // Pequeña espera antes de iniciar la conversión
   return read_adc();               // Leer y devolver el valor analógico
}

// Limpia la LCD.
void lcd_clear() {
   lcd_putc("\f");
}

void start_blink() {
   for (int i = 0; i <= 2; i++) {
      output_high(STATUS_LED);
      delay_ms(250);
      output_low(STATUS_LED);
      delay_ms(250);
   }
}

void main() {
   lcd_init();
   lcd_gotoxy(1,1), lcd_putc("INICIANDO");
   lcd_gotoxy(1,2), lcd_putc("SISTEMA");
   delay_ms(2000);
   
   init_ports();
   lcd_gotoxy(1,2), lcd_putc("SERVOMOTORES");
   /*
   servo_init();
   servo_1_write(0);
   servo_2_write(0);
   servo_3_write(0);
   */
   lcd_clear();
   delay_ms(2000);
   start_blink();
   
   int1 alarmEnabled = 1;
   int display = 1;
   do {
      // Lectura de los sensores.
      sensor_1 = read_analog_channel(0);
      delay_ms(20);
      sensor_2 = read_analog_channel(1);
      delay_ms(20);
      sensor_3 = read_analog_channel(2);
      delay_ms(20);
      sensor_4 = read_analog_channel(3);
      delay_ms(20);
      
      /*
      // Cálculo de voltajes.
      voltage_1 = sensor_1 * 5.0 / 1023.0;
      voltage_2 = sensor_2 * 5.0 / 1023.0;
      voltage_3 = sensor_3 * 5.0 / 1023.0;
      voltage_4 = sensor_4 * 5.0 / 1023.0;
      
      // Cálculo de partes por millón.
      ppm_1 = voltage_1 * 1000.0;
      ppm_2 = voltage_2 * 1000.0;
      ppm_3 = voltage_3 * 1000.0;
      ppm_4 = voltage_4 * 1000.0;
      */
      
      // Acción del botón del switch de alarma.
      if (input(ALARM_SWITCH) == 1) {
         delay_ms(200); // Anti-rebote.
         lcd_clear();
         lcd_gotoxy(1,1), lcd_putc("ESTADO ALARMA");
         lcd_gotoxy(1,2);
         if (alarmEnabled == 1) {
            alarmEnabled = 0;
            lcd_putc("Activado");
         } else {
            alarmEnabled = 1;
            lcd_putc("Desactivado");
         }
         continue;
      }
      
      // Acción del botón de display.
      if (input(DISPLAY_SWITCH) == 1) {
         delay_ms(200);
         if (display >= 4) display = 0;
         else display++;
      }
      
      if (
         sensor_1 >= limit_raw
         ||
         sensor_2 >= limit_raw
         ||
         sensor_3 >= limit_raw
         ||
         sensor_4 >= limit_raw
      ) {
         // Revisa si la alarma está habilitada, para sonar.
         if (alarmEnabled == 1) {
            for (int i = 0; i <= 3; i++) {
               output_high(LED);
               output_high(BUZZER);
               delay_ms(2000);
               output_low(LED);
               output_low(BUZZER);
               delay_ms(2000);
            }
         }
         
         // Manejo de los sensores.
         if (sensor_1 >= limit_raw) {
            lcd_clear();
            lcd_gotoxy(1, 1), lcd_putc("GAS DETECTADO");
            lcd_gotoxy(1, 2), printf(lcd_putc, "S1 | L: %.1f", sensor_1);
            output_high(MOTOR_1);
            if (input(DISPLAY_SWITCH) == 1) continue;
            if (input(ALARM_SWITCH) == 1) continue;
         } else if (sensor_2 >= limit_raw) {
            lcd_clear();
            lcd_gotoxy(1, 1), lcd_putc("GAS DETECTADO");
            lcd_gotoxy(1, 2), printf(lcd_putc, "S2 | L: %.1f", sensor_2);
            output_high(MOTOR_2);
            if (input(DISPLAY_SWITCH) == 1) continue;
            if (input(ALARM_SWITCH) == 1) continue;
         } else if (sensor_3 >= limit_raw) {
            lcd_clear();
            lcd_gotoxy(1, 1), lcd_putc("GAS DETECTADO");
            lcd_gotoxy(1, 2), printf(lcd_putc, "S3 | L: %.1f", sensor_3);
            output_high(MOTOR_3);
            if (input(DISPLAY_SWITCH) == 1) continue;
            if (input(ALARM_SWITCH) == 1) continue;
         } else if (sensor_4 >= limit_raw) {
            lcd_clear();
            lcd_gotoxy(1, 1), lcd_putc("GAS DETECTADO");
            lcd_gotoxy(1, 2), printf(lcd_putc, "S4 | L: %.1f", sensor_4);
            output_high(MOTOR_4);
            if (input(DISPLAY_SWITCH) == 1) continue;
            if (input(ALARM_SWITCH) == 1) continue;
         }
      } else {
         // Lecturas del sensor seleccionado.
         if (display == 1) {
            lcd_clear();
            lcd_gotoxy(1, 1), lcd_putc("LECTURA ACTUAL");
            lcd_gotoxy(1, 2), printf(lcd_putc, "S1 | PPM: %.1f", sensor_1);
            output_low(MOTOR_1);
            if (input(DISPLAY_SWITCH) == 1) continue;
            if (input(ALARM_SWITCH) == 1) continue;
         } else if (display == 2) {
            lcd_clear();
            lcd_gotoxy(1, 1), lcd_putc("LECTURA ACTUAL");
            lcd_gotoxy(1, 2), printf(lcd_putc, "S2 | PPM: %.1f", sensor_2);
            output_low(MOTOR_2);
            if (input(DISPLAY_SWITCH) == 1) continue;
            if (input(ALARM_SWITCH) == 1) continue;
         } else if (display == 3) {
            lcd_clear();
            lcd_gotoxy(1, 1), lcd_putc("LECTURA ACTUAL");
            lcd_gotoxy(1, 2), printf(lcd_putc, "S3 | PPM: %.1f", sensor_3);
            output_low(MOTOR_3);
            if (input(DISPLAY_SWITCH) == 1) continue;
            if (input(ALARM_SWITCH) == 1) continue;
         } else if (display == 4) {
            lcd_clear();
            lcd_gotoxy(1, 1), lcd_putc("LECTURA ACTUAL");
            lcd_gotoxy(1, 2), printf(lcd_putc, "S4 | PPM: %.1f", sensor_4);
            output_low(MOTOR_4);
            if (input(DISPLAY_SWITCH) == 1) continue;
            if (input(ALARM_SWITCH) == 1) continue;
         }
      }
   } while (true);
}

/*
CALCULAR EL VOLTAJE DEL POT
float volts = (reading * 5.0) / 1023.0;
DONDE 
- READING: VALOR DEL ADC
- 5.0: VOLTAJE MÁXIMO
- 1023: RESISTENCIA MÁXIMA DEL POT
*/
```