#include <Arduino.h>
#include <Wire.h>
#include <MicroLCD.h>
#include <Ultrasonic.h> 
#include "DHT.h"

#define ldr A0


DHT dht1(A1, DHT11);
DHT dht2(A2, DHT11);

HC_SR04 sensor1(12,13); //(Trigger,Echo)

LCD_SSD1306 lcd; /* para módulo contralado pelo CI SSD1306 OLED */


float h1 = 0; 
float t1 = 0;
float h2 = 0; 
float t2 = 0;

//Variaveis de média:

int divisor = 0;
float somaumidade1 = 0;
float somatemperatura1= 0;
float mediaumidade1= 0;
float mediatemperatura1=0;

void setup() { 
  pinMode(ldr, INPUT);
  Serial.begin(9600); 
  dht1.begin();
  dht2.begin();
  lcd.begin();

}

void loop() {

//medias calculadas:
divisor ++;
somaumidade1 += somaumidade1;
somatemperatura1 += somatemperatura1;
mediaumidade1= somaumidade1/divisor;
mediatemperatura1=somatemperatura1/divisor;

h1 = dht1.readHumidity();
t1 = dht1.readTemperature();
h2 = dht2.readHumidity();
t2 = dht2.readTemperature();

Serial.print("Umidade interna: ");
Serial.print(h1);
Serial.print(" %     ");
Serial.print("    Temperatura interna: ");
Serial.print(t1);
Serial.print(" ºC           ");
Serial.print("Umidade externa: ");
Serial.print(h2);
Serial.print(" %     ");
Serial.print("    Temperatura externa: ");
Serial.print(t2);
Serial.print(" ºC           ");
Serial.print(" Distância  ");
Serial.print(sensor1.distance());  
Serial.print(" cm       ");
Serial.print(" Luz:  ");
Serial.println(analogRead(ldr));


   lcd.clear();
  lcd.setCursor(15, 0);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(" Monitoramento:      ");
  
  lcd.setCursor(5, 10);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Umidade I:");
    lcd.setCursor(90, 10);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(h1);
    lcd.setCursor(124, 10);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("%");

    lcd.setCursor(5, 11);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Temperatura I:");
    lcd.setCursor(92, 11);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(t1);
    lcd.setCursor(124, 11);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("C");

  lcd.setCursor(5, 12);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Umidade E:");
    lcd.setCursor(90, 12);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(h2);
    lcd.setCursor(124, 12);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("%");

    lcd.setCursor(5, 13);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Temperatura E:");
    lcd.setCursor(92, 13);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(t2);
    lcd.setCursor(124, 13);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("C");
  
      lcd.setCursor(5, 14);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Desenvolvimento:");
    lcd.setCursor(105, 14);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(sensor1.distance());
    lcd.setCursor(118, 14);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("cm");
  
        lcd.setCursor(5, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Luminosidade:");
    lcd.setCursor(90, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(analogRead(A0));
    lcd.setCursor(124, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("l");
  delay(10000);}
