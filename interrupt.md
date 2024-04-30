# RFM95 Interrupt Wake-Up Example

This Arduino sketch demonstrates how to implement an interrupt-based wake-up mechanism using an RFM95 LoRa module and an Arduino Pro Mini microcontroller.

## Description

The RFM95 module is configured to trigger an interrupt on the Arduino Pro Mini when certain events occur, such as receiving data or completing a transmission. The Arduino Pro Mini utilizes the AVR sleep library to enter low-power sleep mode when idle, conserving power until an interrupt occurs.

Upon detecting an interrupt, the Arduino wakes up, handles the interrupt, and performs necessary actions, such as retrieving data from the RFM95 module via SPI communication.

This example serves as a reference for implementing power-efficient communication schemes in battery-powered IoT devices.

## Components Used

- Arduino Pro Mini
- RFM95 LoRa Module
- LED (for indication)
- Resistors, wires, etc.

## Setup

1. Connect the RFM95 module to the Arduino Pro Mini according to the pin configurations specified in the sketch.
2. Upload the provided sketch to the Arduino Pro Mini using the Arduino IDE or compatible software.
3. Monitor the serial output to observe the wake-up and interrupt handling behavior.

## License

This project is licensed under the [MIT License](LICENSE).

~~~
#include <avr/sleep.h>
#include <SPI.h>

// Define pin constants for RFM95 module
const int RFM95_RST = 5;
const int RFM95_NSS = 6;
const int RFM95_DIO0 = 4;  // Connected to DIO0 pin of RFM95 module

const int LED = 9;  // Example LED pin
const int INTERRUPT_PIN = 2; // Example interrupt pin

volatile bool rfm95Interrupt = false;

void setup() {
  pinMode(RFM95_RST, OUTPUT);
  pinMode(RFM95_NSS, OUTPUT);
  pinMode(RFM95_DIO0, INPUT);
  pinMode(LED, OUTPUT);
  pinMode(INTERRUPT_PIN, INPUT_PULLUP);

  // Attach interrupt to detect RFM95 interrupt
  attachInterrupt(digitalPinToInterrupt(INTERRUPT_PIN), rfm95InterruptHandler, RISING);

  // Initialize SPI communication
  SPI.begin();
  
  // Reset RFM95 module
  digitalWrite(RFM95_RST, HIGH);
  delay(10);
  digitalWrite(RFM95_RST, LOW);
  delay(10);
  digitalWrite(RFM95_RST, HIGH);
  delay(10);
}

void loop() {
  // Put Arduino into sleep mode to save power
  sleepMode();

  // Check if RFM95 interrupt occurred
  if (rfm95Interrupt) {
    // Handle RFM95 interrupt
    handleRfm95Interrupt();

    // Reset interrupt flag
    rfm95Interrupt = false;
  }
}

void sleepMode() {
  // Disable ADC to save power
  ADCSRA = 0;  

  // Set sleep mode to Power-down
  set_sleep_mode(SLEEP_MODE_PWR_DOWN);

  // Enable sleep
  sleep_enable();

  // Enter sleep mode
  sleep_cpu();

  // After waking up, disable sleep
  sleep_disable();

  // Re-enable ADC
  ADCSRA = 1<<ADEN;
}

void handleRfm95Interrupt() {
  // Blink LED to indicate interrupt
  for (int i = 0; i < 5; i++) {
    digitalWrite(LED, HIGH);
    delay(100);
    digitalWrite(LED, LOW);
    delay(100);
  }

  // Request data from RFM95 via SPI
  digitalWrite(RFM95_NSS, LOW); // Select the RFM95 module
  // Send SPI command(s) to request data
  // Example: SPI.transfer(0x02); // Example command to request data
  digitalWrite(RFM95_NSS, HIGH); // Deselect the RFM95 module

  // Process received data if needed
}

void rfm95InterruptHandler() {
  // Set interrupt flag to indicate RFM95 interrupt occurred
  rfm95Interrupt = true;
}

~~~
