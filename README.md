int buzzerPin = 13;
const int led1 = 9;
const int led2 = 11;
const int led3 = 8;
const int led4 = A0;

int lightSensorPin = A1;
int BlindSensor1 = A4;
int BlindSensor2 = A5;

const int Trigger1 = 2;
const int Echo1 = 12;

// Function to measure distance using ultrasonic sensor
long getDistance() {
  digitalWrite(Trigger1, LOW);
  delayMicroseconds(2);

  digitalWrite(Trigger1, HIGH);
  delayMicroseconds(10);
  digitalWrite(Trigger1, LOW);

  long duration = pulseIn(Echo1, HIGH);
  return duration / 58;
}

// L298N - DC motor control (2 motors)
int in1 = 6;
int in2 = 7;
int in3 = 4;
int in4 = 5;

// ENA, ENB: PWM speed control pins (connected to L298N)
int enA = 3;   // PWM motor A (left)
int enB = 10;  // PWM motor B (right)

// Speed calibration for straight movement
// If the car drifts right → increase speedLeft or decrease speedRight
int speedLeft  = 100;
int speedRight = 100;

void MovingWay(int A) {

  // Move backward
  if (A == 0) {
    analogWrite(enA, speedLeft);
    analogWrite(enB, speedRight);
    digitalWrite(in1, HIGH);
    digitalWrite(in2, LOW);
    digitalWrite(in3, LOW);
    digitalWrite(in4, HIGH);
  }

  // Move forward (fast)
  else if (A == 1) {
    analogWrite(enA, 250);
    analogWrite(enB, 250);
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    digitalWrite(in3, HIGH);
    digitalWrite(in4, LOW);
    Serial.print("moving fast");
  }

  // Move forward (slow)
  else if (A == 11) {
    analogWrite(enA, 10);
    analogWrite(enB, 10);
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    digitalWrite(in3, HIGH);
    digitalWrite(in4, LOW);
    Serial.print("moving slow");
  }

  // Stop
  else if (A == 2) {
    analogWrite(enA, speedLeft);
    analogWrite(enB, speedRight);
    digitalWrite(in1, LOW);
    digitalWrite(in2, LOW);
    digitalWrite(in3, LOW);
    digitalWrite(in4, LOW);
  }

  // Turn left (gradual)
  else if (A == 3) {
    analogWrite(enA, 150);
    analogWrite(enB, 150);
    digitalWrite(in1, HIGH);
    digitalWrite(in2, LOW);
    digitalWrite(in3, HIGH);
    digitalWrite(in4, LOW);
  }

  // Turn left (sharp)
  else if (A == 4) {
    analogWrite(enA, 255);
    analogWrite(enB, 255);
    digitalWrite(in1, HIGH);
    digitalWrite(in2, LOW);
    digitalWrite(in3, HIGH);
    digitalWrite(in4, LOW);
  }

  // Turn right (gradual)
  else if (A == 5) {
    analogWrite(enA, 155);
    analogWrite(enB, 155);
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    digitalWrite(in3, LOW);
    digitalWrite(in4, HIGH);
  }

  // Turn right (sharp)
  else if (A == 6) {
    analogWrite(enA, 255);
    analogWrite(enB, 255);
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    digitalWrite(in3, LOW);
    digitalWrite(in4, HIGH);
  }
}

void setup() {
  pinMode(buzzerPin, OUTPUT);

  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);
  pinMode(led4, OUTPUT);

  pinMode(BlindSensor1, INPUT);
  pinMode(BlindSensor2, INPUT);

  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
  pinMode(enA, OUTPUT);
  pinMode(enB, OUTPUT);

  pinMode(Trigger1, OUTPUT);
  pinMode(Echo1, INPUT);
  digitalWrite(Trigger1, LOW);

  pinMode(lightSensorPin, INPUT);

  Serial.begin(9600);
}

void loop() {

  int value = analogRead(lightSensorPin);
  Serial.println(value);

  if (value > 100 && getDistance() < 25) {
    MovingWay(11);

    Serial.println("Distance < 25:");
    Serial.println(getDistance());

    analogWrite(led2, 255);
    analogWrite(led1, 0);
    analogWrite(led3, 0);

    digitalWrite(buzzerPin, HIGH);
  }
  else {
    MovingWay(1);

    Serial.println("Distance > 25:");
    Serial.println(getDistance());

    analogWrite(led2, 0);
    analogWrite(led1, 255);
    analogWrite(led3, 255);

    digitalWrite(buzzerPin, LOW);
  }

  if (digitalRead(BlindSensor1) == HIGH || digitalRead(BlindSensor2) == HIGH) {
    analogWrite(led4, 255);
    digitalWrite(buzzerPin, HIGH);
  }
  else {
    analogWrite(led4, 0);
    digitalWrite(buzzerPin, LOW);
  }

  delay(100);
}
