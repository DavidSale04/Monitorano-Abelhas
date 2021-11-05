#include <Ultrasonic.h>
#include "DHT.h"

#define DHTPIN A1 
#define DHTTYPE DHT11
#define ldr A0
 
float h = 0;
float t = 0;


DHT dht(DHTPIN, DHTTYPE);

HC_SR04 sensor1(12,13);   //(Trigger,Echo)

void setup() 
{
  pinMode(ldr, INPUT);
  Serial.begin(9600);
  dht.begin();

}
 
void loop() 
{  
  h = dht.readHumidity();
  t = dht.readTemperature();

    Serial.print("Umidade: ");
    Serial.print(h);
    Serial.print(" %     ");
    Serial.print("    Temperatura: ");
    Serial.print(t);
    Serial.print(" ºC           ");
    Serial.print(" Distância  ");
    Serial.print(sensor1.distance());  
    Serial.print(" cm       ");
    Serial.print(" Luz:  ");
    Serial.println(analogRead(ldr));

  delay(2000);
}
