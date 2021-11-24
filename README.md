
#include <Ultrasonic.h>
#include <SPI.h>
#include <SD.h>
#include <Arduino.h>
#include <Wire.h>
#include <MicroLCD.h>
#include "DHT.h"

#define ldr A0

DHT dht1(A1, DHT11);
DHT dht2(A2, DHT11);

HC_SR04 sensor1(5,6); //(Trigger,Echo)

LCD_SSD1306 lcd; /* para módulo contralado pelo CI SSD1306 OLED */

const int chipSelect = 4;
unsigned long period = 60000;  //modifique aqui o intervalo entre medidas, utilize multiplos de 6000ms   
unsigned long time_now = 0;
int tempo = 1;
int timer = 0;
float h1 = 0; 
float t1 = 0;
float h2 = 0; 
float t2 = 0;


void setup() { 

    
  pinMode(ldr, INPUT);
  pinMode(2, OUTPUT);
  Serial.begin(9600); 
  dht1.begin();
  dht2.begin();
  lcd.begin();
 
   digitalWrite(2, HIGH);
Serial.print(F("Lendo cartao SD"));
delay(100);
  if (!SD.begin(chipSelect)) {
     Serial.println(F("Leitura Falhou"));
     while (1);
  }
Serial.println(F("Cartao SD Inicializado!"));
 
    //(segundos, minutos, hora, dia da semana, dia do mes, mes, ano)
 
}

void loop() {
 
h1 = dht1.readHumidity();
t1 = dht1.readTemperature();
h2 = dht2.readHumidity();
t2 = dht2.readTemperature();

 if (millis() >= time_now + period) {
  time_now += period; 
     
        
        String dataString = "";
          dataString += String(tempo);
          dataString += ",";      //separa cada dado com uma virgula
          dataString += String(t1);
          dataString += ",";
          dataString += String(h1);
          dataString += ",";
          dataString += String(t2);
          dataString += ",";
          dataString += String(h2);
          dataString += ",";
          dataString += String(analogRead(A0));
          


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
  lcd.print("OFF");
 // sensor1.distance()
 //   lcd.setCursor(118, 14);
 // lcd.setFontSize(FONT_SIZE_SMALL);
  //lcd.print("cm");
  
        lcd.setCursor(5, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Luminosidade:");
    lcd.setCursor(90, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(analogRead(A0));
    lcd.setCursor(124, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("l");
  

           
      File dataFile = SD.open("abelhas4r.csv", FILE_WRITE);
       
        if (dataFile) {
          dataFile.println(dataString);
          dataFile.close();
          Serial.println(dataString);
        }
          
        else {
          Serial.println(F("ERRO"));
       }
       tempo++; //contador de minutos
  }       

  }
  
