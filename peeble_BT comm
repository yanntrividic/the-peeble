#include<Wire.h>
#include <SoftwareSerial.h>
SoftwareSerial XBee(0, 1);

const int MPU=0x68; 
int16_t AcX,AcY,AcZ,Tmp,GyX,GyY,GyZ;
int16_t pAcX, pAcY, pAcZ, pTmp, pGyX, pGyY, pGyZ;
int looper = 0;

int ledPinTilt = 13;
int ledPinAcc = 12;
int inPin = 7; 
int value = 0;


long mapped;
long gThreshold = 10000;
long aThreshold = 5000;

void setup(){
  XBee.begin(9600);
  Wire.begin();
  Wire.beginTransmission(MPU);
  Wire.write(0x6B); 
  Wire.write(0);    
  Wire.endTransmission(true);
  Serial.begin(9600);

  
  pinMode(ledPinTilt, OUTPUT);              // initializes digital pin 13 as output
  pinMode(ledPinAcc, OUTPUT);              // initializes digital pin 13 as output
  pinMode(inPin, INPUT);                // initializes digital pin 7 as input
}

void loop(){

  value = digitalRead(inPin);   // reads the value at a digital input 
  digitalWrite(ledPinTilt, value);   
  
  Wire.beginTransmission(MPU);
  Wire.write(0x3B);  
  Wire.endTransmission(false);
  Wire.requestFrom(MPU,12,true);  
  AcX=Wire.read()<<8|Wire.read();    
  AcY=Wire.read()<<8|Wire.read();  
  AcZ=Wire.read()<<8|Wire.read();  
  GyX=Wire.read()<<8|Wire.read();  
  GyY=Wire.read()<<8|Wire.read();  
  GyZ=Wire.read()<<8|Wire.read();

  if(looper == 0){
    pAcX=AcX;
    pAcY=AcY;
    pAcZ=AcZ;
    pGyX=GyX;
    pGyY=GyY;
    pGyZ=GyZ;
    looper++;
  }
  
  Serial.print("Accelerometer: ");
  //Serial.print("X = "); Serial.print(AcX);
  //Serial.print(" | Y = "); Serial.print(AcY);
  //Serial.print(" | Z = "); Serial.print(AcZ); 
  //Serial.print(" | Ader = "); 
  long Ader = derivative(pAcX, AcX) + derivative(pAcY, AcY) + derivative(pAcZ, AcZ);
  Serial.println(Ader) ; 

  
  Serial.print("Gyroscope: ");
  //Serial.print("X = "); Serial.print(GyX);
  //Serial.print(" | Y = "); Serial.print(GyY);
  //Serial.print(" | Z = "); Serial.print(GyZ);
  //Serial.print(" | Gder = "); 
  long Gder = derivative(pGyX, GyX) + derivative(pGyY, GyY) + derivative(pGyZ, GyZ);
  
  Serial.println(Gder) ; 

  Serial.println(" ");

  pAcX=AcX;
  pAcY=AcY;
  pAcZ=AcZ;
  pGyX=GyX;
  pGyY=GyY;
  pGyZ=GyZ;


  long mapped = 0 ;
  
  //long mapped = map(Gder, 0, 35000, 0, 255);
  
  if(Gder > gThreshold || Ader > aThreshold){
    mapped += 1;
  }

  
 // Serial.print("Mapped: ");
  Serial.println(mapped);
  digitalWrite(ledPinAcc, mapped); 
  if (Serial.available())
  { // If data comes in from serial monitor, send it out to XBee
    XBee.write(Serial.read());
  }  
  
  delay(333);
}

int16_t derivative(int16_t prev, int16_t current){
  return abs(abs(current)-abs(prev));
}
