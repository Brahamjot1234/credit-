// Pin Definitions
const int ledPin = 13;
const int pirPin = 7;
const int buttonPin = 3;
const int triggerPin = 4;
const int echoPin = 5;

// Variables
boolean focusOnUltrasonic = false; // Toggle between PIR and Ultrasonic
int ledState = LOW;

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(pirPin, INPUT);
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(triggerPin, OUTPUT);
  pinMode(echoPin, INPUT);

  attachInterrupt(digitalPinToInterrupt(buttonPin), toggleSensorFocus, FALLING);

  Serial.begin(9600);
}

void loop() {
  if (focusOnUltrasonic) {
    handleUltrasonicSensor();
  } else {
    handlePIRSensor();
  }

  Serial.print("LED state: ");
  Serial.println(ledState);
  delay(500);
}

void toggleSensorFocus() {
  focusOnUltrasonic = !focusOnUltrasonic;
  Serial.print("Button pressed. Now focusing on ");
  Serial.println(focusOnUltrasonic ? "Ultrasonic sensor" : "PIR sensor");
}

void handlePIRSensor() {
  if (digitalRead(pirPin) == HIGH) {
    activateLED("Motion detected");
  } else {
    deactivateLED();
  }
}

void handleUltrasonicSensor() {
  digitalWrite(triggerPin, LOW);
  delayMicroseconds(2);
  digitalWrite(triggerPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(triggerPin, LOW);

  long timeSpan = pulseIn(echoPin, HIGH);
  int distance = timeSpan * 0.034 / 2;

  if (distance < 50) {
    activateLED("Object detected");
  } else {
    deactivateLED();
  }
}

void activateLED(const char* message) {
  digitalWrite(ledPin, HIGH);
  ledState = HIGH;
  Serial.println(message);
}

void deactivateLED() {
  digitalWrite(ledPin, LOW);
  ledState = LOW;
}
