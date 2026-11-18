/* 
 * File:   newmain.c
 * Author: jpman
 *
 * Created on August 18, 2026, 10:31 AM
 */

// CONFIG1L
#pragma config PLLDIV = 1       // PLL Prescaler Selection bits (No prescale (4 MHz oscillator input drives PLL directly))
#pragma config CPUDIV = OSC1_PLL2// System Clock Postscaler Selection bits ([Primary Oscillator Src: /1][96 MHz PLL Src: /2])
#pragma config USBDIV = 1       // USB Clock Selection bit (used in Full-Speed USB mode only; UCFG:FSEN = 1) (USB clock source comes directly from the primary oscillator block with no postscale)

// CONFIG1H
#pragma config FOSC = XT_XT        // Oscillator Selection bits (HS oscillator (HS))
#pragma config FCMEN = OFF      // Fail-Safe Clock Monitor Enable bit (Fail-Safe Clock Monitor disabled)
#pragma config IESO = OFF       // Oscillator Switchover mode disabled

// CONFIG2L
#pragma config PWRT = OFF       // Power-up Timer Enable bit (PWRT disabled)
#pragma config BOR = ON         // Brown-out Reset Enable bits (Brown-out Reset enabled in hardware only (SBOREN is disabled))
#pragma config BORV = 3         // Brown-out Reset Voltage bits (Minimum setting 2.05V)
#pragma config VREGEN = OFF     // USB Voltage Regulator Enable bit (USB voltage regulator disabled)

// CONFIG2H
#pragma config WDT = OFF        // Watchdog Timer Enable bit (WDT disabled (control is placed on the SWDTEN bit))
#pragma config WDTPS = 32768    // Watchdog Timer Postscale Select bits (1:32768)

// CONFIG3H
#pragma config CCP2MX = ON      // CCP2 MUX bit (CCP2 input/output is multiplexed with RC1)
#pragma config PBADEN = OFF     // PORTB A/D Enable bit (PORTB<4:0> pins are configured as analog input channels on Reset)
#pragma config LPT1OSC = OFF    // Low-Power Timer 1 Oscillator Enable bit (Timer1 configured for higher power operation)
#pragma config MCLRE = ON       // MCLR Pin Enable bit (MCLR pin enabled; RE3 input pin disabled)

// CONFIG4L
#pragma config STVREN = ON      // Stack Full/Underflow Reset Enable bit (Stack full/underflow will cause Reset)
#pragma config LVP = OFF         // Single-Supply ICSP Enable bit (Single-Supply ICSP enabled)
#pragma config ICPRT = OFF      // Dedicated In-Circuit Debug/Programming Port (ICPORT) Enable bit (ICPORT disabled)
#pragma config XINST = OFF      // Extended Instruction Set Enable bit (Instruction set extension and Indexed Addressing mode disabled (Legacy mode))

// CONFIG5L
#pragma config CP0 = OFF        // Code Protection bit (Block 0 (000800-001FFFh) is not code-protected)
#pragma config CP1 = OFF        // Code Protection bit (Block 1 (002000-003FFFh) is not code-protected)
#pragma config CP2 = OFF        // Code Protection bit (Block 2 (004000-005FFFh) is not code-protected)
#pragma config CP3 = OFF        // Code Protection bit (Block 3 (006000-007FFFh) is not code-protected)

// CONFIG5H
#pragma config CPB = OFF        // Boot Block Code Protection bit (Boot block (000000-0007FFh) is not code-protected)
#pragma config CPD = OFF        // Data EEPROM Code Protection bit (Data EEPROM is not code-protected)

// CONFIG6L
#pragma config WRT0 = OFF       // Write Protection bit (Block 0 (000800-001FFFh) is not write-protected)
#pragma config WRT1 = OFF       // Write Protection bit (Block 1 (002000-003FFFh) is not write-protected)
#pragma config WRT2 = OFF       // Write Protection bit (Block 2 (004000-005FFFh) is not write-protected)
#pragma config WRT3 = OFF       // Write Protection bit (Block 3 (006000-007FFFh) is not write-protected)

// CONFIG6H
#pragma config WRTC = OFF       // Configuration Register Write Protection bit (Configuration registers (300000-3000FFh) are not write-protected)
#pragma config WRTB = OFF       // Boot Block Write Protection bit (Boot block (000000-0007FFh) is not write-protected)
#pragma config WRTD = OFF       // Data EEPROM Write Protection bit (Data EEPROM is not write-protected)

// CONFIG7L
#pragma config EBTR0 = OFF      // Table Read Protection bit (Block 0 (000800-001FFFh) is not protected from table reads executed in other blocks)
#pragma config EBTR1 = OFF      // Table Read Protection bit (Block 1 (002000-003FFFh) is not protected from table reads executed in other blocks)
#pragma config EBTR2 = OFF      // Table Read Protection bit (Block 2 (004000-005FFFh) is not protected from table reads executed in other blocks)
#pragma config EBTR3 = OFF      // Table Read Protection bit (Block 3 (006000-007FFFh) is not protected from table reads executed in other blocks)

// CONFIG7H
#pragma config EBTRB = OFF      // Boot Block Table Read Protection bit (Boot block (000000-0007FFh) is not protected from table reads executed in other blocks)

// #pragma config statements should precede project file includes.
// Use project enums instead of #define for ON and OFF.

#include <xc.h>
#include <stdio.h>
#include <stdlib.h>


#define Dlock RC7 // LEDs
#define Wvalve RC6
#define Dvalve RB6

#define PWash RB7 // LEDs
#define Wash RA1 // Wash LED
#define Rinse RA0 // Rinse LED
#define Spin RC0

#define Motor RA5 //Motors
#define Motor2 RA3
#define Buzzer RA4

#define _XTAL_FREQ 16000000
#define PWM_Frequency 8300 // PWM signal frequency
#define TMR2_Prescale 4 // Timer2 Prescaler selected for 4

unsigned int PDC10bit; // Global variable for duty cycle

// LCD  port pins

#define RS RB3  //LCD
#define RW RB4
#define EN RB5

#define A2 Door_sensor //Switch
#define B1 Start   // PB
#define B2 Wadjust  //PB, Stop

// Commands for LCD display (taken from Act0 by Dr. Alejandro Arag?n)
#define ClrScreen 0x01 // LCD clear display screen //
#define ReturnHome 0x02 // LCD return home
#define DecCursor 0x04 // LCD decrement cursor (shift cursor to left)
#define IncCursor 0x06 // LCD increment cursor (shift cursor to right)
#define ShiftRight 0x05 // Shift display right
#define ShiftLeft 0x07 // Shift display left
#define DispOFFCurOFF 0x08 // Display OFF, cursor OFF
#define DispOFFCurON 0x0A // Display OFF, cursor ON
#define DispONCurOFF 0x0C // Display ON, cursor OFF
#define DispONCurBk 0x0E // Display ON cursor Blinking
#define DispOFFCurBk 0x0F // Display OFF cursor Blinking
#define ShiftCurLeft 0x10 // Shift cursor position to left
#define ShiftCurRight 0x14 // Shift cursor position to right
#define ShiftDispLeft 0x18 // Shift entire display to the left
#define ShiftDispRight 0x1C // Shift entire display to the right
#define FirstLine 0x80 // Cursor beginning of first line  //
#define SecondLine 0xC0 // Cursor beginning of second line //
#define TwoLines57Mat 0x38 // Two lines, 5x7 matrix

// Constants (delays)
#define Delay_LCD 20 // Delay for LCD ENABLE pin
#define Delay_Shift 10 // Delay for char shifts in LCD - instant
#define Delay_Auto 10 // Delay for automatic display - instant
#define Delay_LED 200 // Delay for blinking LED


// PORT definitions
#define LCD_Port PORTD // LCD display connected to PORTA
#define LCD_Tris TRISD // LCD display I/O pin selection

void Init_Ports(void)
{
    
    ADCON1 = 0x0F; // CONFIGURE analog pins as digital, configures the ADCON1 register
    // Inputs
    TRISAbits.TRISA0 = 0; // Rinse LED
    TRISAbits.TRISA1 = 0; // Wash LED
    TRISAbits.TRISA2 = 1; // Door sensor
    TRISBbits.TRISB0 = 1; // Vibration interrupt (INT0)
    TRISBbits.TRISB1 = 1; // Continue button
    TRISBbits.TRISB2 = 1; // PB2
    
    // Actuators
    TRISCbits.TRISC7 = 0; // Dlock
    TRISCbits.TRISC6 = 0; // Wvalve
    TRISBbits.TRISB6 = 0; // Dvalve
    TRISBbits.TRISB7 = 0; // PWash
    TRISCbits.TRISC2 = 0; // CCP1 PWM - motor direction 2
    TRISCbits.TRISC1 = 0; // CCP2 PWM - motor direction 1
    TRISCbits.TRISC0 = 0; // Spin
    
    TRISAbits.TRISA5 = 0; // Motor
    TRISAbits.TRISA3 = 0; // Motor
    TRISAbits.TRISA4 = 0; // Buzzer
    
    PORTCbits.RC7 = 0; // Dlock initialized to low
    PORTCbits.RC6 = 0; // Wvalve initialized to low
    PORTCbits.RC5 = 0; // Dvalve initialized to low
    PORTCbits.RC4 = 0; // PWash initialized to low
    PORTCbits.RC2 = 0; // CCP1 PWM - motor direction 2
    PORTCbits.RC1 = 0; // CCP2 PWM - motor direction 1
    PORTCbits.RC0 = 0; // Spin initially stopped
    
    PORTAbits.RA0 = 0; // Rinse LED initially off
    PORTAbits.RA1 = 0; // Wash LED initially off
    PORTAbits.RA5 = 0; // Motor initially stopped
    PORTAbits.RA3 = 0; // Motor initially stopped
    PORTAbits.RA4 = 0; // Buzzer, initially low
   
    TRISBbits.TRISB3 = 0; // RS declared as output for LCD display
    TRISBbits.TRISB4 = 0; // RW declared as output for LCD display
    TRISBbits.TRISB5 = 0; // EN declared as output for LCD display
    
    PORTBbits.RB3 = 0; // initialized to low
    PORTBbits.RB4 = 0; //
    PORTBbits.RB5 = 0; //
    
    LCD_Tris = 0; // All LCD pins declared as output    
    LCD_Port = 0; // Initialize display PORT buffer to 0
}

// The next function writes a command to the LCD display, written by te teacher
void Lcd_CmdWrite(unsigned char c){
    LCD_Port = c; // Place char ASCII in LCD data bus
    RS = 0; // To send commands to LCD: RS = 0 
    RW = 0; // This one always to GND // 0 is for writing data
    EN = 1; // Send data to LCD now
    __delay_ms(Delay_LCD); // Wait for line to stabilise
    EN = 0; // Ready, all sent
}

// The next function writes data to the LCD display, written by te teacher
void Lcd_DataWrite(unsigned char d){
    LCD_Port = d; // Place char ASCII in LCD data bus
    RS = 1; // To send data to LCD: RS = 1
    RW = 0; // This one always to GND
    EN = 1; // Send data to LCD now
    __delay_ms(Delay_LCD); // Wait for line to syabilise
    EN = 0; // Ready, all sent
}

// The next function uses the previous function to write a full text string to the LCD, written by the teacher
void Message_LCD(unsigned char *s){
    while(*s){   
    Lcd_DataWrite(*s++);
    }
}

// The next function gest the LCD set up, written by the teacher
void Init_LCD(){
    Lcd_CmdWrite(TwoLines57Mat); //Configure the LCD for two lines
    Lcd_CmdWrite(DispONCurOFF);  // Turn the display ON and turn the cursor OFF.
    Lcd_CmdWrite(ClrScreen);
    Lcd_CmdWrite(FirstLine);
}




// Initialize PWM module
void Init_PWM(){
    P1M1 = 0; // Single PWM output
    P1M0 = 0;

    CCP1M3 = 1; // CCP1 PWM mode on RC2
    CCP1M2 = 1;
    CCP1M1 = 0;
    CCP1M0 = 0;

    CCP2M3 = 1; // CCP2 PWM mode on RC1
    CCP2M2 = 1;
    CCP2M1 = 0;
    CCP2M0 = 0;

    T2CKPS1 = 0; // Prescaler 1:4 for Timer 2
    T2CKPS0 = 1;
    TMR2ON = 1; // Turn timer 2 on
}

// Calculate duty cycle
// duty1 controls CCP1 / RC2; duty2 controls CCP2 / RC1
void PWM_Duty(unsigned int duty1, unsigned int duty2){
    if(duty1 > 100){
        duty1 = 100;
    }

    if(duty2 > 100){
        duty2 = 100;
    }

    PDC10bit = ((unsigned long)duty1 * 4 * (PR2 + 1))/100;
    DC1B1 = (PDC10bit >> 1) & 1;
    DC1B0 = PDC10bit & 1;
    CCPR1L = PDC10bit >> 2; // Store the remaining 8 bit

    PDC10bit = ((unsigned long)duty2 * 4 * (PR2 + 1))/100;
    DC2B1 = (PDC10bit >> 1) & 1;
    DC2B0 = PDC10bit & 1;
    CCPR2L = PDC10bit >> 2; // Store the remaining 8 bit
}

void PWM_Period(){  //wrote by the teacher
    float PWMPeriod = 0.0;
    float Tosc = 0.0;

    PWMPeriod = 1.0/PWM_Frequency;
    Tosc = 1.0/_XTAL_FREQ;
    PR2 = (PWMPeriod/(4*Tosc*TMR2_Prescale))-1;
}

void shut_down_everything(){
    PWM_Duty(0, 0); // Stop both motor directions
    Dlock = 0;
    Wvalve = 0;
    Dvalve = 0;
    PWash = 0;
    Wash = 0;
    Rinse = 0;
    Spin = 0;
    Motor = 0;
    Motor2 = 0;
}


void Init_sequence(void)
{
  Lcd_CmdWrite(ClrScreen);
  Lcd_CmdWrite(FirstLine);
  Message_LCD("All goodie");
  Lcd_CmdWrite(SecondLine);
  Message_LCD("in the hoodie");
  __delay_ms(1000);
  Lcd_CmdWrite(ClrScreen);
  shut_down_everything();
}

// basically just a  delay function, but looks better
void wait(int ms){
    int i;
    for(i = 0; i < ms; i++){
        __delay_ms(1); 
    }
}

// first cycle
void pwash(){
    int i = 0;
    for(i = 0; i < 8000; i++){
        wait(1);
        shut_down_everything();
        PWash = 1; // led
        Wvalve = 1; // led
        Dlock = 1;
        if (i % 1000 == 0){
          Lcd_CmdWrite(ClrScreen);  
          Lcd_CmdWrite(FirstLine);
          Message_LCD("Pre-Wash");
          Lcd_CmdWrite(SecondLine);
          Message_LCD("Water valve open");
        } 
    }
    
    for(i = 0; i < 2000; i++){
        wait(1);
        shut_down_everything();
        PWM_Duty(70, 0); // 70% duty cycle, motor direction 2
        PWash = 1;
        Wvalve = 1; // led
        Dlock = 1;
        if (i % 1000 == 0){
          Lcd_CmdWrite(ClrScreen);  
          Lcd_CmdWrite(FirstLine);
          Message_LCD("Pre-Wash");
          Lcd_CmdWrite(SecondLine);
          Message_LCD("Energizing motor");
        } 
    }
    
    shut_down_everything();
    Lcd_CmdWrite(ClrScreen);
}


// second cycle
void wash(){
    int i = 0;
    for(i = 0; i < 30000; i++){
        wait(1);
        Wash = 1; // led
        Wvalve = 0; // led
        if (i%1000 == 0){
            if ((i/1000)%2 == 0){
                PWM_Duty(0, 100); // 100% duty cycle, motor direction 1
            }
            
            else{
                PWM_Duty(100, 0); // 100% duty cycle, motor direction 2
            }
        }
        Dlock = 1; // led
        if (i % 1000 == 0){
          Lcd_CmdWrite(ClrScreen);  
          Lcd_CmdWrite(FirstLine);
          Message_LCD("Wash");
          Lcd_CmdWrite(SecondLine);
          Message_LCD("Energizing motor");
        } 
    }
    
    shut_down_everything();
    Lcd_CmdWrite(ClrScreen);
}

// third cycle

void rinse(){
    int i = 0;
    for(i = 0; i < 15000; i++){
        wait(1);
        shut_down_everything();
        Rinse = 1; // led
        Wvalve = 1; // led
        Dvalve = 1;      
        Dlock = 1; // led
        if (i % 1000 == 0){
          Lcd_CmdWrite(ClrScreen);  
          Lcd_CmdWrite(FirstLine);
          Message_LCD("Rinse");
          Lcd_CmdWrite(SecondLine);
          Message_LCD("Water valve open");
        } 
    }
    
    for(i = 0; i < 5000; i++){
        wait(1);
        shut_down_everything();
        Rinse = 1; // led
        Wvalve = 1; // led
        Dvalve = 1;
        PWM_Duty(70, 0); // 70% duty cycle, motor direction 2
        Dlock = 1; // led
        if (i % 1000 == 0){
          Lcd_CmdWrite(ClrScreen);  
          Lcd_CmdWrite(FirstLine);
          Message_LCD("Rinse");
          Lcd_CmdWrite(SecondLine);
          Message_LCD("Energizing motor");
        } 
    }
    
    shut_down_everything();
    Lcd_CmdWrite(ClrScreen);
}

// fourth cycle
void spin(){
    int i = 0;
    for(i = 0; i < 10000; i++){
        wait(1);
        shut_down_everything();
        Spin = 1; // led
        PWM_Duty(100, 0); // 100% duty cycle, motor direction 2
        Dlock = 1;
        if (i % 1000 == 0){
          Lcd_CmdWrite(ClrScreen);  
          Lcd_CmdWrite(FirstLine);
          Message_LCD("Spin");
          Lcd_CmdWrite(SecondLine);
          Message_LCD("Energizing motor");
        } 
    }
    
    shut_down_everything();
    Lcd_CmdWrite(ClrScreen);
}


// Enable interrupts (INT0 and INT2)
void Enable_Interrupts() {
    TRISBbits.TRISB0 = 1;     // RB0 as vibration input
    TRISBbits.TRISB2 = 1;     // RB2 as stop input

    INTEDG0 = 1;              // RB0 rising edge: low to high
    INTEDG2 = 1;              // RB2 rising edge: low to high

    INT0IF = 0;               // Clear INT0 flag
    INT2IF = 0;               // Clear INT2 flag

    INT0IE = 1;               // Enable RB0 / INT0 interrupt
    INT2IE = 1;               // Enable RB2 / INT2 interrupt
    GIE = 1;                  // Enable global interrupts
}



void start_message(){
    Lcd_CmdWrite(ClrScreen);
    Lcd_CmdWrite(FirstLine);
    Message_LCD("Press");
    Lcd_CmdWrite(SecondLine);
    Message_LCD("start");    
}


void stop_triggered(){
    Lcd_CmdWrite(ClrScreen);
    Lcd_CmdWrite(FirstLine);
    Message_LCD("Stop");
    Lcd_CmdWrite(SecondLine);
    Message_LCD("triggered");
}


// Interrupt service routine

void __interrupt() Interrupt2(void) {

    if (INT0IF) {

        INT0IF = 0;

        // Immediately stop everything
        shut_down_everything();

        int i = 0;

        Lcd_CmdWrite(ClrScreen);
        Lcd_CmdWrite(FirstLine);
        Message_LCD("Excessive");
        Lcd_CmdWrite(SecondLine);
        Message_LCD("vibration");

        // Continue alarm until vibration signal goes LOW
        while (PORTBbits.RB0 == 1) {

            __delay_ms(1);
            i++;

            // Alarm ON
            if (i == 300) {

                Dlock = 1;
                PWash = 1;
                Wash = 1;
                Rinse = 1;
                Spin = 1;
                Buzzer = 1;
            }

            // Alarm OFF
            if (i == 1000) {

                Dlock = 0;
                PWash = 0;
                Wash = 0;
                Rinse = 0;
                Spin = 0;
                Buzzer = 0;
            }

            // Reset blinking timer
            if (i >= 1200) {
                i = 0;
            }
        }

        // =================================
        // VIBRATION HAS STOPPED
        // =================================

        shut_down_everything();

        Lcd_CmdWrite(ClrScreen);
        Lcd_CmdWrite(FirstLine);
        Message_LCD("Press START");

        // Wait for START (RB1 HIGH)
        // and door sensor HIGH (RA2)
        while (PORTBbits.RB1 == 0 || PORTAbits.RA2 != 1) {
            wait(1);
        }

        // Confirmation buzzer
        Buzzer = 1;
        wait(2000);
        Buzzer = 0;

        Lcd_CmdWrite(ClrScreen);

        INT0IF = 0;
    }

    
    
    // interrupt2 (original)
    if (INT2IF) {

        INT2IF = 0;

        // If spin is active
        if (PORTCbits.RC0 == 1) {

            Lcd_CmdWrite(ClrScreen);

            Buzzer = 1;
            wait(2000);
            Buzzer = 0;

            Spin = 1;
        }

        // Otherwise pause the cycle
        else {

            shut_down_everything();

            Dlock = 0;

            Lcd_CmdWrite(ClrScreen);
            Lcd_CmdWrite(FirstLine);
            Message_LCD("Cycle paused");
            Lcd_CmdWrite(SecondLine);
            Message_LCD("Press START");

            // Wait for START (RB1 LOW)
            // and door sensor HIGH (RA2)
            while (PORTBbits.RB1 == 0 || PORTAbits.RA2 != 1) {
                wait(1);
            }

            Buzzer = 1;
            wait(2000);
            Buzzer = 0;
        }

        Lcd_CmdWrite(ClrScreen);

        INT2IF = 0;
    }
}

void main(void) {
    Init_Ports();
    Init_LCD();
    Init_sequence();
    PWM_Period();
    PWM_Duty(0, 0);
    Init_PWM();
    Enable_Interrupts();
    
    while(1){
    int i = 0;
    while(PORTBbits.RB1 == 0){ //wait for the START button
        wait(1);
        i = i + 1;
        if (i%1000 == 0){
            start_message();
            i = 0;
        }
    }
    

    wait(2000); // interval
    pwash();
    wait(2000);
    wash();
    wait(2000);
    rinse();
    wait(2000);
    spin();
    
    int a = 0;
    while(a < 3){
        int i = 0;
        wait(1);
        i = i +1;
        if (i == 500){
            Buzzer = 1;
        }
        
        if (i == 1000){
            Buzzer = 0;
            i = 0;
            a = a+1;
        }
    }
  }
}
