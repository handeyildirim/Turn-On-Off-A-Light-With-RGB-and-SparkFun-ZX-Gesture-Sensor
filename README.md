# Turn-On-Off-A-Light-With-RGB-and-SparkFun-ZX-Gesture-Sensor
#include <Wire.h>
#include <ZX_Sensor.h>

int state = HIGH; // the current state of the output pin
int reading; //the current reading from the input pin
int previous = LOW; // the previous reading from the input pin

//Pins
int ledPin = 7;

// Constants
const int ZX_ADDR = 0x10;  // ZX Sensor I2C address

// Global Variables
ZX_Sensor zx_sensor = ZX_Sensor(ZX_ADDR);
uint8_t x_pos;
uint8_t z_pos;

void setup() {

  //Initialize digital pin 7 as an output.
  pinMode(ledPin, OUTPUT);
  
  uint8_t ver;

  // Initialize Serial port
  Serial.begin(9600);
  Serial.println();
  Serial.println("-----------------------------------");
  Serial.println("SparkFun/GestureSense - I2C ZX Demo");
  Serial.println("-----------------------------------");
  
  // Initialize ZX Sensor (configure I2C and read model ID)
  if ( zx_sensor.init() ) {
    Serial.println("ZX Sensor initialization complete");
  } else {
    Serial.println("Something went wrong during ZX Sensor init!");
  }
  
  // Read the model version number and ensure the library will work
  ver = zx_sensor.getModelVersion();
  if ( ver == ZX_ERROR ) {
    Serial.println("Error reading model version number");
  } else {
    Serial.print("Model version: ");
    Serial.println(ver);
  }
  if ( ver != ZX_MODEL_VER ) {
    Serial.print("Model version needs to be ");
    Serial.print(ZX_MODEL_VER);
    Serial.print(" to work with this library. Stopping.");
    while(1);
  }
  
  // Read the register map version and ensure the library will work
  ver = zx_sensor.getRegMapVersion();
  if ( ver == ZX_ERROR ) {
    Serial.println("Error reading register map version number");
  } else {
    Serial.print("Register Map Version: ");
    Serial.println(ver);
  }
  if ( ver != ZX_REG_MAP_VER ) {
    Serial.print("Register map version needs to be ");
    Serial.print(ZX_REG_MAP_VER);
    Serial.print(" to work with this library. Stopping.");
    while(1);
  }
}

void loop() {
  
  // If there is position data available, read and print it
  if ( zx_sensor.positionAvailable() ) {
    
    x_pos = zx_sensor.readX(); //for the x coordinat
    if ( x_pos != ZX_ERROR ) {
      Serial.print("X: ");
      Serial.print(x_pos);
    }
    
    z_pos = zx_sensor.readZ(); //for the z coordinat
    if ( z_pos != ZX_ERROR ) {
      Serial.print(" Z: ");
      Serial.println(z_pos);
      if(z_pos <= 12){
        if(previous == LOW){
          state = HIGH;
          digitalWrite(ledPin,state);
          reading = state;
          previous = reading;
          control();
        }
        
        else {
          state = LOW;
          digitalWrite(ledPin,state);
          reading = state;
          previous = reading;
          control();
       }
      }
    }   
  }  
}

void control(){
  z_pos = zx_sensor.readZ(); 
    if ( z_pos != ZX_ERROR ) {
      if(reading == HIGH && previous == HIGH){
        state = LOW ;
      }
      else if(reading == LOW && previous == LOW){
        state = HIGH;
      }
      else if(reading == LOW && previous == HIGH){
        state = HIGH;
      }
      else {
        state = LOW;
      }
    }
}
