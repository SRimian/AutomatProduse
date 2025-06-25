# AutomatProduse

1. Se instaleaza Arduino IDE
2. Se introduce codul in sketch
3. Alegem placa corecta si portul serial corect
4. Apasam verify si apoi load


Codul proiectului:
#include <LiquidCrystal_I2C.h>
#include <Wire.h>
#include <Servo.h>

Servo servo1;
Servo servo2;

LiquidCrystal_I2C lcd(0x27, 16, 2);

#define pinIR 7
#define buton1 A0
#define buton2 A1
#define pinSensor 8
#define pinServo1 5
#define pinServo2 6

float sold = 0;

void actiuneServo(int parametru) {
  switch(parametru) {
    case 0:
      sold += 0.50;
      rescriereEcran();
      break;
    case 1:
      lcd.setCursor(0, 3);
      lcd.print("Sold insuficient ");
      delay(2000);
      rescriereEcran();
      break;
    case 2:
      lcd.setCursor(0, 3);
      lcd.print("Asteptati...     ");
      miscareServo1();
      break;
    case 3:
      lcd.setCursor(0, 3);
      lcd.print("Asteptati...     ");
      miscareServo2();
      break;
  }
}
void miscareServo1() {
  servo1.attach(pinServo1);
  servo1.write(10);
  delay(1000);
  servo1.detach();
  sold -= 3.0;
  Serial.println("Produs 1 aruncat, cost: 3.00 RON");
  rescriereEcran();
}

void miscareServo2() {
  servo2.attach(pinServo2);
  servo2.write(10);
  delay(1000);
  servo2.detach();
  sold -= 2.5;
  Serial.println("Produs 2 aruncat, cost: 2.50 RON");
  rescriereEcran();
}

void verificaApasareButon() {
  bool stareButon1 = digitalRead(buton1);
  bool stareButon2 = digitalRead(buton2);

  if (stareButon1 == LOW) apasareButon1();
  if (stareButon2 == LOW) apasareButon2();
}


void apasareButon1() {
  if (sold >= 3.0) {
    actiuneServo(3);
  } else if (sold < 3.0) {
    actiuneServo(1);
  }
}

void apasareButon2() {
  if (sold >= 2.5) {
    actiuneServo(2);
  } else if (sold < 2.5) {
    actiuneServo(1);
  }
}

void rescriereEcran() {
  lcd.clear();
  lcd.setCursor(2, 0);
  lcd.print("SOLD:");
  lcd.setCursor(7, 0);
  lcd.print(sold);
  lcd.setCursor(11, 0);
  lcd.print("RON");
  lcd.setCursor(0, 3);
  lcd.setCursor(0, 1);
  lcd.print("-PR1-      -PR2-");
  lcd.setCursor(0, 2);
  lcd.print("  -1-         -2-");
}

void detectareMoneda() {
  if (digitalRead(pinSensor)) {
    actiuneServo(0);
  }
}

void setup() {
  lcd.begin(16,2);
  lcd.backlight();
  rescriereEcran();
  pinMode(buton1, INPUT_PULLUP);
  pinMode(buton2, INPUT_PULLUP);
  pinMode(pinSensor, INPUT);
  pinMode(pinIR, INPUT);
  Serial.begin(9600);
}

void loop() {
  verificaApasareButon();
  detectareMoneda();
}
