#include <Ultrasonic.h>
#include <SPI.h>
#include <SD.h>
#include <Arduino.h>
#include <Wire.h>
#include <MicroLCD.h>
#include "DHT.h"
#include <virtuabotixRTC.h>

#define TRIGGER_PIN  5
#define ECHO_PIN     6
#define ldr A0
#define ldre A1
DHT dht1(A2, DHT11);
DHT dht2(A3, DHT11);

Ultrasonic ultrasonic(TRIGGER_PIN, ECHO_PIN);
//Determina os pinos ligados ao modulo
//myRTC(clock, data, rst)
virtuabotixRTC myRTC(8, 9, 10);
LCD_SSD1306 lcd; /* para módulo contralado pelo CI SSD1306 OLED */

const int chipSelect = 4;
unsigned long period = 5000;  //modifique aqui o intervalo entre medidas, utilize multiplos de 6000ms   
unsigned long time_now = 0;
int tempo = 1;
int timer = 0;
float h1 = 0; 
float t1 = 0;
float h2 = 0; 
float t2 = 0;


void setup() { 

    //myRTC.setDS1302Time(10, 37, 13,5, 02, 12, 2021);
  pinMode(ldr, INPUT);
   pinMode(ldre, INPUT);
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
  float cmMsec, inMsec;
  long microsec = ultrasonic.timing();

  cmMsec = ultrasonic.convert(microsec, Ultrasonic::CM);
  inMsec = ultrasonic.convert(microsec, Ultrasonic::IN);
 
h1 = dht1.readHumidity();
t1 = dht1.readTemperature();
h2 = dht2.readHumidity();
t2 = dht2.readTemperature();

 if (millis() >= time_now + period) {
  time_now += period; 
     
        
        String dataString = "";
        myRTC.updateTime();
 
 dataString+=String(myRTC.dayofmonth);
  dataString+=("/");
  dataString+=(myRTC.month);
  dataString+=("/");
  dataString+=(myRTC.year);
    dataString+=("  ");
 dataString+=("Hora : ");
  //Adiciona um 0 caso o valor da hora seja <10
  if (myRTC.hours < 10)
  {
 dataString+=("0");
  }
  dataString+=(myRTC.hours);
  dataString+=(":");
  //Adiciona um 0 caso o valor dos minutos seja <10
  if (myRTC.minutes < 10)
  {
    dataString+=("0");
  }
  dataString+=(myRTC.minutes);
 
  dataString+=(":");
  //Adiciona um 0 caso o valor dos segundos seja <10
  if (myRTC.seconds < 10)
  {
   dataString+=("0");
  }
  dataString+=(myRTC.seconds);

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
           dataString += ",";
          dataString += String(analogRead(A1));         


           lcd.clear();
  lcd.setCursor(3, 0);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Monitoramento:");
   
  
 lcd.setCursor(90, 0);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(myRTC.hours);
   lcd.setCursor(105, 0);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(":");
 
   lcd.setCursor(110, 0);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(myRTC.minutes);

  
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
  lcd.print("Distancia:");
    lcd.setCursor(69, 14);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(cmMsec);
  
  lcd.setCursor(118, 14);
 lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("cm");
  
        lcd.setCursor(5, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Luz I e E:");
    lcd.setCursor(70, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(analogRead(A0));
    lcd.setCursor(95, 15);
      lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(analogRead(A1));
    lcd.setCursor(124, 15);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("l");
  

           
      File dataFile = SD.open("e0212.csv", FILE_WRITE);
       
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
  
