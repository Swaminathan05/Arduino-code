# Arduino-code
This the C++ code for Arduino Uno to control the speed of the fan with respect to the temperature and simulate the on/off of the LED. 
code:

#include <math.h>

const int THERMISTOR_PIN = A0;
const int FAN_PIN = 13;
const float SERIES_RESISTOR = 10000;
const float THERMISTOR_NOMINAL = 10000;
const int TEMPERATURE_NOMINAL = 25;
const int B_COEFFICIENT = 3950;

void setup() {
  Serial.begin(9600);
  pinMode(FAN_PIN, OUTPUT);
}
void loop() {
  int adcValue = analogRead(THERMISTOR_PIN);
  float resistance = SERIES_RESISTOR / ((1023.0 / adcValue) - 1.0);
  float steinhart;
  steinhart = resistance / THERMISTOR_NOMINAL;
  steinhart = log(steinhart);
  steinhart /= B_COEFFICIENT;
  steinhart += 1.0 / (TEMPERATURE_NOMINAL + 273.15);
  steinhart = 1.0 / steinhart;
  float temperatureC = steinhart - 273.15;

  String fanState;

  if (temperatureC < 25) {
    analogWrite(FAN_PIN, 0);
    fanState = "OFF";
  } else if (temperatureC >= 25 && temperatureC < 30) {
    analogWrite(FAN_PIN, 128);
    fanState = "50% Speed";
  } else {
    analogWrite(FAN_PIN, 255);
    fanState = "100% Speed";
  }
  Serial.print("Temperature: ");
  Serial.print(temperatureC);
  Serial.print(" °C  |  Fan State: ");
  Serial.println(fanState);

  delay(1000);
}
