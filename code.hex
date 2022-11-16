#include <SoftwareSerial.h>
#include <Servo.h>
#include <TinyGPS.h>
#include<LiquidCrystal.h> 
LiquidCrystal lcd(A0, A1, A2, A3, A4, A5);
SoftwareSerial gsmSerial(2, 3);
TinyGPS gps;
Servo Servo1;
#define trigpin1 12
18
#define echopin1 13
#define led1 5
#define led2 6
#define led3 7
#define led4 8
int pirPin = 4;
int pirState = 0;
int calibrationTime = 5;
void setup() {
  lcd.begin(16, 2);
  Serial.begin(9600);
  gsmSerial.begin(9600);
  Servo1.attach(9);
  delay(1000);
  Servo1.write(0);
  delay(1000);
  Servo1.detach();
  pinMode(trigpin1, OUTPUT);
  pinMode(echopin1, INPUT);
  pinMode(pirPin, INPUT);
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);
  pinMode(led4, OUTPUT);
  digitalWrite(led1, LOW);
  digitalWrite(led2, LOW);
  digitalWrite(led3, LOW);
  digitalWrite(led4, LOW);
  delay(calibrationTime * 1000);
  Serial.println("Smart Waste Management System");
  Servo1.attach(9);
}
bool flag1 = false;
long Distance1, Duration1;
long Filled_bin = 0;
bool isfilled;
19
void loop() {
  Serial.println();
  delay(200);
  Serial.println("PIR sensor data: ");
  pirState = digitalRead(pirPin);
  if (pirState == HIGH && Filled_bin < 97){
    Serial.println("OPEN LID");
    Servo1.attach(9);
    Servo1.write(100); //rotate servo motor
    flag1 = true; 
  }
  else if (pirState == LOW) {
    Serial.println("CLOSE LID");
    if(flag1 == false){
      Servo1.detach();
    }
    if(flag1 == true){
      Servo1.write(0); //bring to original state since it was opened in this case
      flag1 = false;
    }
  }
  Serial.println();
  delay(200);
  digitalWrite(trigpin1, LOW);
  delayMicroseconds(2);
  digitalWrite(trigpin1, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigpin1, LOW);
  Duration1 = pulseIn(echopin1, HIGH);
  Distance1 = (Duration1 / 2) / 29.1;
  Filled_bin = 100 - ((Distance1-4) / 11.04);
 
  lcd.setCursor(0,0); 
  lcd.print("Waste Level: ");
  lcd.setCursor(0,1);
  lcd.print("Filled: ");
  lcd.print(Filled_bin);
  lcd.print("%");
  Serial.println("Data from Ultrasonic Sensor: ");
  Serial.print("Filled: ");
  Serial.print(Filled_bin);
  Serial.println("%");
 
  if ((Filled_bin >= 0) && (Filled_bin < 25)){
    Serial.println("level 1");
    digitalWrite(led1, HIGH);
    digitalWrite(led2, LOW);
    digitalWrite(led3, LOW);
    digitalWrite(led4, LOW);
    isfilled = false;
  }
  else if ((Filled_bin >= 25) && (Filled_bin < 50)){
    Serial.println("level 2");
    digitalWrite(led1, HIGH);
    digitalWrite(led2, HIGH);
    digitalWrite(led3, LOW);
    digitalWrite(led4, LOW);
    isfilled = false;
  }
  else if ((Filled_bin >= 50) && (Filled_bin < 75)){
    Serial.println("level 3");
    digitalWrite(led1, HIGH);
    digitalWrite(led2, HIGH);
    digitalWrite(led3, HIGH);
    digitalWrite(led4, LOW);
    isfilled = false;
  }
  else{
    Serial.println("level 4");
    digitalWrite(led1, HIGH);
    digitalWrite(led2, HIGH);
    digitalWrite(led3, HIGH);
    digitalWrite(led4, HIGH);
    if(isfilled == true){
      isfilled = false;
    }
    else{
      isfilled = true;
    }
    if(isfilled == true){
      gsmSerial.println("AT+CMGF=1"); //Sets the GSM Module in Text Mode
      delay(200);
      gsmSerial.println("AT+CMGS=\"+9762019139470\"\r");
      delay(200);
      gsmSerial.println("SEND GARBAGE TRUCK TO EMPTY THE BIN");// This SMS is sent
      delay(200);
      gsmSerial.println((char)26);
    }
  }
  delay(200);
  Serial.println();
  bool newData = false;
  unsigned long chars;
  unsigned short sentences, failed;
  for (unsigned long start = millis(); millis() - start < 1000;){
    while (Serial.available()){
      char c = Serial.read();
      if (gps.encode(c)){
        newData = true;
      }
    }
  }
  if (newData == true){
    Serial.print("GPS data: ");
    float flat, flon;
    unsigned long age;
    gps.f_get_position(&flat, &flon, &age); 
    Serial.print("Latitude = ");
    Serial.print(flat == TinyGPS::GPS_INVALID_F_ANGLE ? 0.0 : flat, 6);
    Serial.print(" Longitude = ");
    Serial.print(flon == TinyGPS::GPS_INVALID_F_ANGLE ? 0.0 : flon, 6);
  }
  Serial.println();
}
