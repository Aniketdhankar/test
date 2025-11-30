

//water level detection 
// Pin definitions
const int trigPin = 9;
const int echoPin = 10;
const int buzzerPin = 7;
long duration;
int distance;
void setup() {
 pinMode(trigPin, OUTPUT);
 pinMode(echoPin, INPUT);
 pinMode(buzzerPin, OUTPUT);
 Serial.begin(9600);
}

void loop() {
 // --- Ultrasonic sensor distance measurement ---
 digitalWrite(trigPin, LOW);
 delayMicroseconds(2);
 
 digitalWrite(trigPin, HIGH);
 delayMicroseconds(10);
 digitalWrite(trigPin, LOW);
 duration = pulseIn(echoPin, HIGH);
 distance = duration * 0.034 / 2; // Convert to cm
 // --- Intrusion Detection Condition ---
 if (distance < 50 ) { // Threshold: 50 cm or sound detected
 digitalWrite(buzzerPin, HIGH); // Alarm ON
 Serial.println("Intrusion Detected!");
 } else {
 digitalWrite(buzzerPin, LOW); // Alarm OFF
 }
 // Print distance for debugging
 Serial.print("Distance: ");
 Serial.print(distance);
 Serial.println(" cm");
 delay(500);
}









//moisture
const int moisturePin = A0;
int moistureValue = 0;
const int threshold = 700;
void setup()
{
Serial.begin(9600);
pinMode(moisturePin, INPUT);
}
void loop()
{
moistureValue = analogRead(moisturePin);
Serial.print("Moisture Value: ");
Serial.println(moistureValue);
if (moistureValue > threshold)
{
Serial.println("Dry Waste Detected");
}
else
{
Serial.println("Wet Waste Detected");
}
delay(1000);
}






7--relay
const int relayPin = 7;
void setup()
{
pinMode(relayPin, OUTPUT);
digitalWrite(relayPin, LOW);
}
void loop()
{
digitalWrite(relayPin, HIGH);
delay(2000);
digitalWrite(relayPin, LOW);
delay(2000);
}




****


Experiment 5: Develop a program to deploy smart street light systemusing LDR sensor

<img width="901" height="517" alt="image" src="https://github.com/user-attachments/assets/28c667ec-dab0-46bd-9fbc-6dd1259cd68f" />


CODE:
int sensorPin = A0;
int sensorValue = 0;
int led = 9;
void setup() {
pinMode(led, OUTPUT);
Serial.begin(9600);
}
void loop(){
sensorValue = analogRead(sensorPin);
Serial.println(sensorValue);
if(sensorValue < 100){
Serial.println("LED light on");
digitalWrite(led,HIGH);
delay(1000);
}
digitalWrite(led,LOW);
delay(sensorValue);
}




//exp 8 gas leaksge---
<img width="717" height="453" alt="image" src="https://github.com/user-attachments/assets/a6594ba3-09a1-467b-81d4-47a756690ac5" />

const int gasSensorPin = A0;
const int buzzerPin = 8;
const int threshold = 500;

void setup() {
    pinMode(buzzerPin, OUTPUT);
    Serial.begin(9600);
}

void loop() {
    int sensorValue = analogRead(gasSensorPin);
    Serial.print("Gas Sensor Value: ");
    Serial.println(sensorValue);
    if (sensorValue > threshold) {
        Serial.println("ALERT: Smoke detected! Buzzer ON!");
        digitalWrite(buzzerPin, HIGH);
    } else {
        Serial.println("Safe: Smoke level normal.");
        digitalWrite(buzzerPin, LOW);
    }
    delay(200);
}


//Experiment 1: Develop a program to blink 5 LEDs backandforth.

<img width="947" height="591" alt="image" src="https://github.com/user-attachments/assets/112c59cd-87a9-419b-be3c-daef2814d2ee" />



#define numOfLed 5
int ledPin[numOfLed] = {2, 3, 4, 5, 6};
int wait = 500;

void setup() {
    for (int i = 0; i < numOfLed; i++) {
        pinMode(ledPin[i], OUTPUT);
    }
}

void loop() {
    for (int i = 0; i < numOfLed; i++) {
        digitalWrite(ledPin[i], HIGH);
        delay(wait);
        digitalWrite(ledPin[i], LOW);
    }
    for (int i = numOfLed - 2; i > 0; i--) {
        digitalWrite(ledPin[i], HIGH);
        delay(wait);
        digitalWrite(ledPin[i], LOW);
    }
}


/uart 


void setup() {
  // Initialize UART communication at 9600 baud rate
  Serial.begin(9600);
}

void loop() {
  // String to transmit
  String message = "Hello from Transmitter Arduino!";

  // Send string
  Serial.println(message);

  // Small delay before sending again
  delay(1000);
}




void setup() {
  // Initialize UART communication at 9600 baud rate
  Serial.begin(9600);
}

void loop() {
  // Check if data is available
  if (Serial.available() > 0) {
    // Read incoming string
    String receivedData = Serial.readStringUntil('\n');
    // Print received string to Serial Monitor
    Serial.println("Received: " + receivedData);
  }
}

