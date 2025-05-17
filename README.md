# EV-OBC-
#include "mbed.h"

mbed::PwmOut pwm9(d9); // Create a PWM output on Pin 9

void setup() {
  pwm9.period(1.0 / 50000);  // Set period to 50 kHz (1/50,000 sec)
  pwm9.write(0.5);           // 50% duty cycle
}

void loop() {}
void setup() {
  // Set pin as output (Timer1 PWM on Pin 9 or 10)
  pinMode(9, OUTPUT);

  // Configure Timer1 for Fast PWM mode with TOP at ICR1
  TCCR1A = (1 << COM1A1) | (1 << WGM11); // Non-inverting mode, Fast PWM
  TCCR1B = (1 << WGM13) | (1 << WGM12) | (1 << CS11); // Fast PWM with ICR1, Prescaler = 8
  ICR1 = 39;  // Set TOP value for 50kHz
  OCR1A = 16; // Set compare match for 40% duty cycle
}

void loop() {
  // Nothing here, PWM runs automatically
}
void setup() {
  pinMode(9, OUTPUT);   // OC1A (PWM Output - High-side)
  pinMode(10, OUTPUT);  // OC1B (Complementary Output - Low-side)

  // Phase Correct PWM Mode 14, ICR1 as TOP, No Prescaler
  TCCR1A = (1 << COM1A1) | (1 << COM1B1) | (1 << COM1B0) | (1 << WGM11);
  TCCR1B = (1 << WGM13) | (1 << CS10);  // Phase Correct, No Prescaler

  ICR1 = 160;  // TOP value for 50 kHz
  OCR1A = 75;  // 50% Duty Cycle for High-side
  OCR1B = 80;  // Slight delay for Low-side (Dead time)
}

void loop() {}
void setup() {

  pinMode(9, OUTPUT);   // OC1A (PWM Output - High-side)

  pinMode(10, OUTPUT);  // OC1B (Complementary Output - Low-side)



  // Configure Timer1 for Phase Correct PWM Mode 14, ICR1 as TOP

  // No Prescaler



  // For Nano BLE 33 Sense Rev2, we use the TC peripheral, not the older Timer1

  // The TC peripheral is more flexible but requires a different setup.



  // Enable the TC peripheral (Timer/Counter)

  PM->APBCMASK.bit.TC0_ = 0; // Unmask TC0 clock



  // Configure TC0 in PWM mode.  We'll use channel 0 (TC0_CH0) for OC1A and channel 1 (TC0_CH1) for OC1B.



  // Reset the TC0 peripheral

  TC0->CTRLA.bit.SWRST = 1;

  while (TC0->SYNCBUSY.bit.SWRST); // Wait for reset to complete



  // Set the prescaler (No prescaler in this example)

  TC0->CTRLA.bit.PRESCALER = TC_CTRLA_PRESCALER_DIV1_gc; // No prescaler

  TC0->CTRLA.bit.MODE = TC_CTRLA_MODE_PWM_gc; // PWM Mode



  // Set waveform generation mode to match Phase Correct PWM (match "match-or-capture" mode)

  TC0->WAVE.bit.WAVEGEN = TC_WAVE_WAVEGEN_MPWM_gc; // Match PWM (Phase Correct)



  // Set the TOP value (ICR1 equivalent)

  TC0->PER = 160;  // TOP value for 50 kHz (adjust as needed)

  while (TC0->SYNCBUSY.bit.PER); // Wait for sync



  // Set the compare values (OCR1A and OCR1B equivalents)

  TC0->CC[0].reg = 75; // 50% Duty Cycle for High-side (OC1A)

  while (TC0->SYNCBUSY.bit.CC0); // Wait for sync



  TC0->CC[1].reg = 80; // Slight delay for Low-side (OC1B - Dead time)

  while (TC0->SYNCBUSY.bit.CC1); // Wait for sync





  // Enable Output on the correct pins (PA06 and PA07 for TC0_CH0 and TC0_CH1 respectively)

  // These are the default pins for TC0_CH0 and TC0_CH1 on the Nano BLE 33 Sense Rev2

  PORT->PINCFG[9].bit.PMUXEN = 1; // Enable peripheral multiplexing on pin 9 (PA06)

  PORT->PMUX[9 >> 1].reg = (PORT->PMUX[9 >> 1].reg & ~(0xF << ((9 & 0x01) * 4))) | (TC_PMUX_TC0_CH0_A << ((9 & 0x01) * 4)); // Set to TC0_CH0 function A



  PORT->PINCFG[10].bit.PMUXEN = 1; // Enable peripheral multiplexing on pin 10 (PA07)

  PORT->PMUX[10 >> 1].reg = (PORT->PMUX[10 >> 1].reg & ~(0xF << ((10 & 0x01) * 4))) | (TC_PMUX_TC0_CH1_A << ((10 & 0x01) * 4)); // Set to TC0_CH1 function A



  PORT->DIRSET.reg = (1 << 9) | (1 << 10); // Set pins 9 and 10 as outputs



  // Start the timer

  TC0->CTRLA.bit.ENABLE = 1;

  while (TC0->SYNCBUSY.bit.ENABLE); // Wait for sync

}


#include <PWM.h>

// Create an instance of the PWM class
PWM pwm;

void setup() {
  // Initialize PWM on pin D2 (or any other PWM-capable pin)
  pwm.begin();
  
  // Set the PWM frequency to 50 kHz
  pwm.setFrequency(50000);

  // Set the duty cycle (50% in this case)
  pwm.write(2, 128);  // 128 is 50% of 255 (8-bit resolution)
}

void 
