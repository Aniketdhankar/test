# IoT Experiments — Project Notes & Code

A clean, well-structured **README.md** that organizes the provided experiments, descriptions, wiring images, and the cleaned Arduino sketches. Use this as a lab report or quick reference for uploading to a repo.

---

## Table of Contents

1. [Experiment: Water Level / Intrusion Detection](#water-level--intrusion-detection)
   1.1 [Ultrasonic (HC-SR04) + Buzzer](#ultrasonic-hc-sr04--buzzer)
   1.2 [Soil/Moisture Sensor](#moisture-sensor)
   1.3 [Relay Test](#relay-test)
2. [Experiment 1: Blink 5 LEDs Back-and-Forth (Knight Rider)](#experiment-1-blink-5-leds-back-and-forth)
3. [Experiment 5: Smart Street Light using LDR](#experiment-5-smart-street-light-using-ldr)
4. [Experiment 8: Gas Leak Detection (MQ-5) with Buzzer](#experiment-8-gas-leak-detection-mq-5-with-buzzer)
5. [UART Example — Two Arduino UNO Communication](#uart-example-two-arduino-uno-communication)
6. [Images / Wiring Diagrams](#images--wiring-diagrams)
7. [Notes & Tips](#notes--tips)

---

## Water level / Intrusion detection

This set contains three separate sketches used in the water level / intrusion detection experiment.

### Ultrasonic (HC-SR04) + Buzzer

**Description:** Measure distance with ultrasonic sensor; if distance is less than threshold (50 cm) trigger buzzer and print alert.

```cpp
// Ultrasonic Sensor + Buzzer (Water Level / Intrusion detection)
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
  // Trigger pulse
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Read echo and convert to cm
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2; // cm

  // Alert condition
  if (distance < 50) { // threshold: 50 cm
    digitalWrite(buzzerPin, HIGH); // alarm ON
    Serial.println("Intrusion Detected!");
  } else {
    digitalWrite(buzzerPin, LOW); // alarm OFF
  }

  // Debugging output
  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  delay(500);
}
```

---

### Moisture Sensor (simple threshold)

**Description:** Read analog moisture sensor and categorize as Dry or Wet based on threshold.

```cpp
// Moisture sensor
const int moisturePin = A0;
int moistureValue = 0;
const int moistureThreshold = 700;

void setup() {
  Serial.begin(9600);
  pinMode(moisturePin, INPUT);
}

void loop() {
  moistureValue = analogRead(moisturePin);
  Serial.print("Moisture Value: ");
  Serial.println(moistureValue);

  if (moistureValue > moistureThreshold) {
    Serial.println("Dry Waste Detected");
  } else {
    Serial.println("Wet Waste Detected");
  }

  delay(1000);
}
```

---

### Relay Test (simple on/off)

**Description:** Toggle a relay module on and off every 2 seconds. Relay connected to digital pin 7.

```cpp
// Relay toggle test
const int relayPin = 7;

void setup() {
  pinMode(relayPin, OUTPUT);
  digitalWrite(relayPin, LOW); // start OFF
}

void loop() {
  digitalWrite(relayPin, HIGH);
  delay(2000);
  digitalWrite(relayPin, LOW);
  delay(2000);
}
```

---

## Experiment 1 — Blink 5 LEDs Back-and-Forth

**Description:** Five LEDs blink in a forward and backward sequence (Knight Rider style).

```cpp
#define NUM_LEDS 5
int ledPin[NUM_LEDS] = {2, 3, 4, 5, 6};
int waitTime = 500;

void setup() {
  for (int i = 0; i < NUM_LEDS; i++) {
    pinMode(ledPin[i], OUTPUT);
  }
}

void loop() {
  // Forward
  for (int i = 0; i < NUM_LEDS; i++) {
    digitalWrite(ledPin[i], HIGH);
    delay(waitTime);
    digitalWrite(ledPin[i], LOW);
  }

  // Backward (skip first and last to create bounce effect)
  for (int i = NUM_LEDS - 2; i > 0; i--) {
    digitalWrite(ledPin[i], HIGH);
    delay(waitTime);
    digitalWrite(ledPin[i], LOW);
  }
}
```

---

## Experiment 5 — Smart Street Light using LDR

**Description:** Read LDR value and switch an LED on when it’s dark (value below threshold).

```cpp
// Smart street light (LDR)
int sensorPin = A0;
int sensorValue = 0;
int ledPin = 9;

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  sensorValue = analogRead(sensorPin);
  Serial.println(sensorValue);

  if (sensorValue < 100) {
    Serial.println("LED light on");
    digitalWrite(ledPin, HIGH);
    delay(1000);  // keep it on briefly
  }

  // turn off and delay proportional to sensor value
  digitalWrite(ledPin, LOW);
  delay(sensorValue);
}
```

> **Tweak:** Adjust the threshold (`100`) and delays to suit ambient lighting.

---

## Experiment 8 — Gas Leak Detection (MQ-5) with Buzzer

**Description:** Read MQ-5 gas sensor; if value exceeds threshold, sound buzzer and print alert.

```cpp
// Gas sensor (MQ-5) with buzzer
const int gasSensorPin = A0;
const int buzzerPin = 8;
const int gasThreshold = 500;

void setup() {
  pinMode(buzzerPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  int sensorValue = analogRead(gasSensorPin);
  Serial.print("Gas Sensor Value: ");
  Serial.println(sensorValue);

  if (sensorValue > gasThreshold) {
    Serial.println("ALERT: Gas detected! Buzzer ON!");
    digitalWrite(buzzerPin, HIGH);
  } else {
    Serial.println("Safe: Gas level normal.");
    digitalWrite(buzzerPin, LOW);
  }

  delay(200);
}
```

---

## UART Example — Two Arduino UNO Communication

**Description:** Simple UART TX and RX examples. Use `SoftwareSerial` if you want to keep USB Serial Monitor free; below is the simplest example using hardware `Serial` for demonstration.

### Transmitter (sends a message every second)

```cpp
void setup() {
  Serial.begin(9600); // hardware UART
}

void loop() {
  String message = "Hello from Transmitter Arduino!";
  Serial.println(message);
  delay(1000);
}
```

### Receiver (prints received messages)

```cpp
void setup() {
  Serial.begin(9600); // hardware UART
}

void loop() {
  if (Serial.available() > 0) {
    String receivedData = Serial.readStringUntil('\n');
    Serial.println("Received: " + receivedData);
  }
}
```

**Wiring note:** For direct UNO-to-UNO hardware UART:

* Connect TX of Board A → RX of Board B (Pin 1 → Pin 0)
* Connect RX of Board A ← TX of Board B (Pin 0 ← Pin 1)
* Connect GND ↔ GND
  **Warning:** Hardware Serial is shared with USB; opening Serial Monitor may interfere. Use `SoftwareSerial` for development while using Serial Monitor.

---

## Images / Wiring Diagrams

Include wiring pictures or diagrams in your repo under an `images/` folder. Example references (replace with your paths):

* Water level / Ultrasonic wiring: `images/ultrasonic_wiring.png`
* Smart street light (LDR) wiring: `images/ldr_wiring.png`
* Gas sensor (MQ-5) wiring: `images/mq5_wiring.png`
* LED blink wiring: `images/leds_wiring.png`

If you have uploaded images to GitHub already, use those URLs or relative links so they show in the Markdown.

---

## Notes & Tips

* Always share a **common ground** between sensors/actuators and Arduino.
* Use proper power and level shifting for modules that require different voltages.
* For MQ-series sensors, allow a warm-up time and test calibration in known conditions.
* Debounce readings or add smoothing (running average) for noisy sensors.
* When using relays, place the relay coil on a separate power source if it draws too much current, and use flyback diodes or optocoupled relay modules for protection.
* For robust UART comms, implement a simple packet format and a checksum if needed.

---

If you want, I can:

* produce this as an actual `README.md` file and include image links, or
* generate a printable lab report version (PDF), or
* add diagrams and wiring pinouts for each experiment.

Which would you like next?
