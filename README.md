# LoRa_sleep
Sleep code
## This sleep code will put ULP LoRa module into sleep mode for a particular duration and wakeup and perform a specific task here led blinking and then it enters into sleep mode 

~~~
#include <avr/sleep.h>
#include <avr/wdt.h>

// Define pin constants for RFM95 module
const int RFM95_RST = 5;
const int RFM95_NSS = 6;
const int RFM95_DIO0 = 4; // Connected to DIO0 pin of RFM95 module
const int RFM95_DIO1 = 3; // Connected to DIO1 pin of RFM95 module
const int RFM95_DIO2 = 2; // Connected to DIO2 pin of RFM95 module

const byte LED = 9;

void flash ()
  {
  pinMode (LED, OUTPUT);
  for (byte i = 0; i < 10; i++)
    {
    digitalWrite (LED, HIGH);
    delay (50);
    digitalWrite (LED, LOW);
    delay (50);
    }
    
  pinMode (LED, INPUT);
    
  }  // end of flash
  
// watchdog interrupt
ISR (WDT_vect) 
{
   wdt_disable();  // disable watchdog
}  // end of WDT_vect
 
void setup () { 

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
  digitalWrite(RFM95_NSS, LOW); // Ensure NSS is high before configuring
  delay(100);
  
  // Set RFM95 module to sleep mode
  setRFM95SleepMode();
  cli();

}

void setRFM95SleepMode() {
  // Manually turn off RFM95 module
  digitalWrite(RFM95_RST, LOW);
  digitalWrite(RFM95_NSS, HIGH);
  digitalWrite(RFM95_DIO0, LOW);
  digitalWrite(RFM95_DIO1, LOW);
  digitalWrite(RFM95_DIO2, LOW);
}

void loop () 
{
 
  flash ();
  
  // disable ADC
  ADCSRA = 0;  

  // clear various "reset" flags
  MCUSR = 0;     
  // allow changes, disable reset
  WDTCSR = bit (WDCE) | bit (WDE);
  // set interrupt mode and an interval 
  WDTCSR = bit (WDIE) | bit (WDP3) | bit (WDP0);    // set WDIE, and 8 seconds delay
  wdt_reset();  // pat the dog
  
  set_sleep_mode (SLEEP_MODE_PWR_DOWN);  
  noInterrupts ();           // timed sequence follows
  sleep_enable();
 
  // turn off brown-out enable in software
  MCUCR = bit (BODS) | bit (BODSE);
  MCUCR = bit (BODS); 
  interrupts ();             // guarantees next instruction executed
  sleep_cpu ();  
  
  // cancel sleep as a precaution
  sleep_disable();
  
  } // end of loop
~~~
