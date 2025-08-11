#include <BluetoothSerial.h>
BluetoothSerial SerialBT;
// Ultrasonic Sensor Pins
#define TRIG_PIN 34
#define ECHO_PIN 27
// PIR Sensor Pin
#define PIR_SENSOR_PIN 32
// LED and Buzzer Pins
#define LED_PIN 19
#define BUZZER_PIN 2
// Function prototypes
long measureDistance();
void alertSequence();
void setup() {
 // Initialize Serial and Bluetooth
 Serial.begin(115200);
 SerialBT.begin("ESP32_Train"); // Bluetooth name
 Serial.println("Bluetooth started, waiting for connection...");
 // Initialize sensor pins
 pinMode(TRIG_PIN, OUTPUT);
 pinMode(ECHO_PIN, INPUT);
 pinMode(PIR_SENSOR_PIN, INPUT);
 // Initialize LED and Buzzer pins
 pinMode(LED_PIN, OUTPUT);
 pinMode(BUZZER_PIN, OUTPUT);
 // Initial states
 digitalWrite(LED_PIN, LOW);
 digitalWrite(BUZZER_PIN, LOW);
}
void loop() {
 // Measure distance
 long distance = measureDistance ();
 bool motionDetected = digitalRead(PIR_SENSOR_PIN);
 // Print and send status via Bluetooth
 if (distance < 10 && motionDetected) {
 Serial.println("Criteria met: Living being detected");
 SerialBT.println("Distance: " + String(distance) + " cm Motion: Detected");
 SerialBT.println("Criteria met: Living being detected");
 // Trigger alert sequence
 alertSequence();
 } else {
 String motionStatus = motionDetected ? "Detected" : "Not Detected";
 Serial.println("Distance: " + String(distance) + " cm Motion: " + motionStatus);
 SerialBT.println("Distance: " + String(distance) + " cm Motion: " + motionStatus);
 }
 // Small delay for stable readings
 delay(500);
}
// Function to measure distance using ultrasonic sensor
long measureDistance() {
 digitalWrite(TRIG_PIN, LOW);
 delayMicroseconds(2);
 digitalWrite(TRIG_PIN, HIGH);
 delayMicroseconds(10);
 digitalWrite(TRIG_PIN, LOW);
 // Measure echo time
 long duration = pulseIn(ECHO_PIN, HIGH);
 long distance = duration * 0.034 / 2; // Convert to cm
 return distance;
}
// Function to trigger LED and Buzzer alert sequence simultaneously
void alertSequence() {
 unsigned long currentMillis = millis();
 // Pattern for buzzer and LED
 const int beepDurations[] = {100, 100, 150, 600, 80, 80}; // Beep durations in ms
 const int delaysAfterBeep[] = {80, 80, 300, 80, 80, 300}; // Delays after each beep in ms
 // Iterate over the pattern
 for (int i = 0; i < 6; i++) {
 // Turn ON LED and Buzzer
 digitalWrite(LED_PIN, HIGH);
 digitalWrite(BUZZER_PIN, HIGH);
 // Wait for the beep duration
 delay(beepDurations[i]);
 // Turn OFF LED and Buzzer
 digitalWrite(LED_PIN, LOW);
 digitalWrite(BUZZER_PIN, LOW);
 // Wait for the delay after the beep
 delay(delaysAfterBeep[i]);
 }
 // Ensure LED and Buzzer are OFF after the sequence
 digitalWrite(LED_PIN, LOW);
 digitalWrite(BUZZER_PIN, LOW);
}
