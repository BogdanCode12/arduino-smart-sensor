#define timpDetectatLumina  10 // 10 seconds if motion is detected
int lowLightValue  = 0;
int highLightValue = 255;
int luminaAprinsaTimp = 0;
int currentLightValue = 0;
bool forceLuminaAprinsa = false;

// Pin definitions
const int pwmPin1 = 9;          
const int pwmPin2 = 10;         
const int lightSensorPin = A0;    
const int irSensorPin = A1;       
const int switchPin = 8;       

unsigned long lastLogic = 0;

void setup() {
  // Initialize pins
  pinMode(pwmPin1, OUTPUT);
  pinMode(pwmPin2, OUTPUT);
  pinMode(lightSensorPin, INPUT);
  pinMode(irSensorPin, INPUT);
  pinMode(switchPin, INPUT_PULLUP);

  // Start with LEDs off
  analogWrite(pwmPin1, 0);
  analogWrite(pwmPin2, 0);
}

void loop() {
  int switchValue = digitalRead(switchPin);
  int lightValue = !digitalRead(lightSensorPin);
  if (switchValue == LOW) {
    forceLuminaAprinsa = true;
  }
  if (switchValue == HIGH) {
    forceLuminaAprinsa = false;
  }
  if (digitalRead(irSensorPin)) {
    luminaAprinsaTimp = timpDetectatLumina;
  }

  if ((millis() - lastLogic) > 1000) {
    if (forceLuminaAprinsa) {
      fadeLightToValue(highLightValue);
    } else {
      if (lightValue == LOW && !forceLuminaAprinsa) {
        if (luminaAprinsaTimp > 0) {
          fadeLightToValue(highLightValue);
          luminaAprinsaTimp--;
        } else {
          fadeLightToValue(30);
        }
      } else {
        fadeLightToValue(lowLightValue);
      }
    }
    lastLogic = millis();
  }
}

void fadeLightToValue(int target) {
  while (target != currentLightValue) {
    if (target > currentLightValue) {
      currentLightValue++;
    } else {
      currentLightValue--;
    }
    analogWrite(pwmPin1, currentLightValue);
    analogWrite(pwmPin2, currentLightValue);
    delay(5);
  }
}
