//Monitoramento de abelhas:

//inclusão das bibliocas 

#include <Ultrasonic.h>
#include <SPI.h>
#include <SD.h>
#include <Arduino.h>
#include <Wire.h>
#include <MicroLCD.h>
#include "DHT.h"
#include <virtuabotixRTC.h>

//Definição dos pinos dos sensores:
#define ldr A0
#define ldre A1
DHT dht1(A2, DHT11);
DHT dht2(A3, DHT11);
HC_SR04 cmMsec(6,5); // (trigger, echo);

//Determina os pinos ligados ao modulo RTC
//myRTC(clock, data, rst)
virtuabotixRTC myRTC(10, 9, 8);

LCD_SSD1306 lcd; /* para módulo contralado pelo CI SSD1306 OLED */
// Conexão do modulo SD:
// ** MOSI - pin 11 on Arduino Uno/Duemilanove/Diecimila
// ** MISO - pin 12 on Arduino Uno/Duemilanove/Diecimila
// ** CLK - pin 13 on Arduino Uno/Duemilanove/Diecimila
// ** CS - depends on your SD card shield or module.
//    Pin 4 used here for consistency with other Arduino examples

//Definição das variáveis coletadas:
const int chipSelect = 4;
unsigned long period = 5000;  //Controla a atualização 
unsigned long time_now = 0;
int tempo = 1;
int timer = 0;
float h1 = 0; 
float t1 = 0;
float h2 = 0; 
float t2 = 0;


  void setup() { 
 //(segundos, minutos, hora, dia da semana, dia do mes, mes, ano)
 //myRTC.setDS1302Time(15, 32, 19,7, 25, 2, 2022);
  pinMode(ldr, INPUT);
  pinMode(ldre, INPUT);
  pinMode(2, OUTPUT);
  
  Serial.begin(9600); 
  dht1.begin();
  dht2.begin();
  lcd.begin();
 // pino 2 definido como alto para ser usado como fonte para o módulo SD
  digitalWrite(2, HIGH);

   //indica a iniciação ou a falha na leitura do cartão
  Serial.print(F("Lendo cartao SD"));
  delay(100);
  if (!SD.begin(chipSelect)) {
     Serial.println(F("Leitura Falhou"));
     while (1);
  }
  Serial.println(F("Cartao SD Inicializado!"));
 }

  void loop() {
   
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
  if (myRTC.hours < 10)
  {
  dataString+=("0");
  }
  dataString+=(myRTC.hours);
  dataString+=(":");
  if (myRTC.minutes < 10)
  {
  dataString+=("0");
  }
  dataString+=(myRTC.minutes);
 
  dataString+=(":");
  if (myRTC.seconds < 10)
  {
   dataString+=("0");
  }
  dataString+=(myRTC.seconds);

          dataString += ",";      //separa cada dado com uma virgula
          dataString += String(t2);
          dataString += ",";
          dataString += String(h2);
          dataString += ",";
          dataString += String(t1);
          dataString += ",";
          dataString += String(h1);
          dataString += ",";
          dataString += String(analogRead(A1));
          dataString += ",";
          dataString += String(analogRead(A0));         
          dataString += ",";
          dataString += String(cmMsec.distance());         

  // escreve os ddados no display OLED
  lcd.clear();
  
  lcd.setCursor(5, 0);
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
  
  lcd.setCursor(0, 2);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Umidade E:");
  lcd.setCursor(90, 2);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(h2);
  lcd.setCursor(124, 2);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("%");
  
  lcd.setCursor(0, 3);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Temperatura E:");
  lcd.setCursor(92, 3);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(t2);
  lcd.setCursor(124, 3);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("C");

  lcd.setCursor(0, 4);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Umidade I:");
  lcd.setCursor(90, 4);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(h1);
  lcd.setCursor(124, 4);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("%");

  lcd.setCursor(0, 5);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Temperatura I:");
  lcd.setCursor(92, 5);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(t1);
  lcd.setCursor(124, 5);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("C");
  
  lcd.setCursor(0, 6);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Luz E e I:");
  lcd.setCursor(70, 6);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(analogRead(A1));
  lcd.setCursor(95, 6);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(analogRead(A0));
  lcd.setCursor(124, 6);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("l");
  
  lcd.setCursor(0, 7);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("Distancia:");
  lcd.setCursor(69, 7);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print(cmMsec.distance());
  lcd.setCursor(110, 7);
  lcd.setFontSize(FONT_SIZE_SMALL);
  lcd.print("cm");

  // cria e escreve o arquivo 
      File dataFile = SD.open("e0212.csv", FILE_WRITE);
       
        if (dataFile) {
          dataFile.println(dataString);
          dataFile.close();
          Serial.println(dataString);}
        else {
          Serial.println(F("ERRO"));
       }
       tempo++; //contador de minutos
  }       
  }
