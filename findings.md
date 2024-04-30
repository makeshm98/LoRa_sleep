## These are the code changes that I sorted out where the device will enter sleep and where the device will happen an interrupt and wake up 

~~~
#include <avr/sleep.h>
#include <avr/wdt.h>

// Define pin constants for RFM95 module
const int RFM95_RST = 5;
const int RFM95_NSS = 6;
const int RFM95_DIO0 = 4;  // Connected to DIO0 pin of RFM95 module
const int RFM95_DIO1 = 3;  // Connected to DIO1 pin of RFM95 module
const int RFM95_DIO2 = 2;  // Connected to DIO2 pin of RFM95 module

const byte LED = 9;

void flash() {
  pinMode(LED, OUTPUT);
  for (byte i = 0; i < 10; i++) {
    digitalWrite(LED, HIGH);
    delay(50);
    digitalWrite(LED, LOW);
    delay(50);
  }

  pinMode(LED, INPUT);

}  // end of flash

// watchdog interrupt
ISR(WDT_vect) {
  wdt_disable();  // disable watchdog
}  // end of WDT_vect

void setup() {

  // Configure pin modes
  pinMode(RFM95_RST, OUTPUT);
  pinMode(RFM95_NSS, OUTPUT);
  pinMode(RFM95_DIO0, INPUT);
  pinMode(RFM95_DIO1, INPUT);
  pinMode(RFM95_DIO2, INPUT);


  // Reset RFM95 module
  digitalWrite(RFM95_RST, LOW);
  delay(10);
  digitalWrite(RFM95_RST, HIGH);
  delay(10);

  // Initialize RFM95 module
  digitalWrite(RFM95_NSS, LOW);  // Ensure NSS is high before configuring
  delay(100);
  disablePortC();
  setPortCInactive();
  // Set RFM95 module to sleep mode
  //setRFM95SleepMode();
  cli();
}

// Function to set Port C pins to high-impedance state
void setPortCInactive() {
  // Configure all pins of Port C as inputs without pull-up resistors
  PORTC = 0x00;  // Clear all pins of Port C (disable internal pull-up resistors)
  DDRC = 0x00;   // Set all pins of Port C as inputs
}


// Function to disable Port C
void disablePortC() {
  // Disable input buffer for Port C (PCINT23:16)
  DDRB = 0xFF;  // Set all pins of Port B as output (or any other unused port)

  // Disable input buffer for Port C (PCINT15:8)
  DDRC = 0xFF;  // Set all pins of Port C as output

  // Disable input buffer for Port C (PCINT7:0)
  DDRD = 0xFF;  // Set all pins of Port D as output (or any other unused port)
}


void setRFM95SleepMode() {
  // Manually turn off RFM95 module
  digitalWrite(RFM95_RST, LOW);
  digitalWrite(RFM95_NSS, HIGH);
  digitalWrite(RFM95_DIO0, LOW);
  digitalWrite(RFM95_DIO1, LOW);
  digitalWrite(RFM95_DIO2, LOW);
}

void loop() {

  flash();

  // disable ADC
  ADCSRA = 0;

  // clear various "reset" flags
  MCUSR = 0;   //when disabling the board will be in sleep mode and will not awake



  // allow changes, disable reset
  WDTCSR = bit(WDCE) | bit(WDE);
  // set interrupt mode and an interval
  WDTCSR = bit(WDIE) | bit(WDP3) | bit(WDP0);  // set WDIE, and 8 seconds delay
  wdt_reset();                                 // pat the dog



// if the line from ADCSRA = 0; and wdt_reset(); doesn't executed then it will only be in sleep mode willl not awake it is 100% true verified


  set_sleep_mode(SLEEP_MODE_PWR_DOWN); //Only led blink is happening not sleep 
  setRFM95SleepMode();  //If this doesn't executed sleep wont be entered


  //noInterrupts();       // timed sequence follows this does not affects sleep sequence where it will be normal mode 
  
  
  sleep_enable();   //If this doen't executed the sleep will not be enabled only led blink will take place



  // turn off brown-out enable in software
  MCUCR = bit(BODS) | bit(BODSE);  //If these two didn't executed the led blink is not taken place where it is showing only 0.750 milli ampere
  MCUCR = bit(BODS);
  interrupts ();             // guarantees next instruction executed


  //if the above three doesn't executed LED blink doesn't take place like the interrupt will not happen where it will remain in 0.740 Milli ampere


  sleep_cpu();  //If this doesn't executed sleep wont be entered

  // cancel sleep as a precaution
  //sleep_disable();

}  // end of loop
~~~
