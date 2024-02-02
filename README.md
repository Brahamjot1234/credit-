const int led = 13;
const int pir = 7;
const int button = 3;
const int trigger = 4;
const int echo = 5;

volatile boolean controlUltrasonic = false; // Flag to control sensor focus

int ledState = LOW;

void setup() {
  pinMode(led, OUTPUT);
  pinMode(pir, INPUT);
  pinMode(button, INPUT_PULLUP);
  pinMode(trigger, OUTPUT);
  pinMode(echo, INPUT);

  attachInterrupt(digitalPinToInterrupt(button), buttonUsed, FALLING);

  Serial.begin(9600);
}

void loop() {
  if (controlUltrasonic) {
    ultrasonicSensor();
  } else {
    if (digitalRead(pir) == HIGH) {
      digitalWrite(led, HIGH);
      ledState = HIGH;
      Serial.println("Motion detected");
    } else {
      digitalWrite(led, LOW);
      ledState = LOW;
    }
  }

  Serial.print("LED state: ");
  Serial.println(ledState);
  delay(500);
}

void buttonUsed() {
  controlUltrasonic = !controlUltrasonic;
  Serial.print("Button pressed. Now focusing on ");
  Serial.println(controlUltrasonic ? "ultrasonic sensor" : "PIR sensor");
}

void ultrasonicSensor() {
  digitalWrite(trigger, LOW);
  delayMicroseconds(2);
  digitalWrite(trigger, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigger, LOW);

  long timeSpan = pulseIn(echo, HIGH);
  int dist = timeSpan * 0.034 / 2;

  int distThreshold = 50;

  if (dist < distThreshold) {
    digitalWrite(led, HIGH);
    ledState = HIGH;
    Serial.println("Object detected");
  } else {
    digitalWrite(led, LOW);
    ledState = LOW;
  }
}
