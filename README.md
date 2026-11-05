# smart-fire-truck
a smart fire truck system using autonomous technology 
5/11/2026

/*
  SMART FIRE FIGHTING CAR
  Board: Arduino UNO
  Language: C++

  Functions:
  1. Detect fire using Flame Sensor.
  2. Confirm fire detection.
  3. Turn ON water pump using Relay.
  4. Activate buzzer.
  5. Turn ON red LED when fire is detected.
  6. Turn ON green LED when no fire is detected.
*/

// ---------------- PIN DEFINITIONS ----------------

const int flameSensorPin = 2;
const int relayPin       = 7;
const int buzzerPin      = 8;
const int redLedPin      = 9;
const int greenLedPin    = 10;


// ---------------- SETTINGS ----------------

// Most flame sensors detect fire with LOW
const int FIRE_DETECTED = LOW;

// Most relay modules are active LOW
const int PUMP_ON  = LOW;
const int PUMP_OFF = HIGH;

// Number of consecutive readings needed
const int CONFIRMATION_COUNT = 3;

// Time between sensor readings
const unsigned long SENSOR_INTERVAL = 100;


// ---------------- VARIABLES ----------------

int fireCounter = 0;

bool fireDetected = false;

unsigned long previousSensorTime = 0;


// ---------------- SETUP ----------------

void setup() {

  pinMode(flameSensorPin, INPUT);

  pinMode(relayPin, OUTPUT);

  pinMode(buzzerPin, OUTPUT);

  pinMode(redLedPin, OUTPUT);

  pinMode(greenLedPin, OUTPUT);


  // Make sure the pump is OFF at startup
  digitalWrite(relayPin, PUMP_OFF);

  digitalWrite(buzzerPin, LOW);

  digitalWrite(redLedPin, LOW);

  digitalWrite(greenLedPin, HIGH);


  Serial.begin(9600);

  Serial.println("==============================");
  Serial.println(" SMART FIRE FIGHTING CAR");
  Serial.println(" System Started Successfully");
  Serial.println("==============================");

}


// ---------------- MAIN LOOP ----------------

void loop() {

  unsigned long currentTime = millis();


  // Read the sensor periodically
  if (currentTime - previousSensorTime >= SENSOR_INTERVAL) {

    previousSensorTime = currentTime;

    int sensorValue = digitalRead(flameSensorPin);


    // Check if fire is detected

    if (sensorValue == FIRE_DETECTED) {

      fireCounter++;

      Serial.println("Fire signal detected!");

    }

    else {

      fireCounter = 0;

      if (fireDetected) {

        fireDetected = false;

        Serial.println("Fire is no longer detected.");

        updateSystem();

      }

    }


    // Confirm fire detection

    if (fireCounter >= CONFIRMATION_COUNT && !fireDetected) {

      fireDetected = true;

      Serial.println("WARNING: FIRE CONFIRMED!");

      updateSystem();

    }

  }

}


// ---------------- SYSTEM CONTROL ----------------

void updateSystem() {

  if (fireDetected) {

    // FIRE DETECTED

    digitalWrite(relayPin, PUMP_ON);

    digitalWrite(buzzerPin, HIGH);

    digitalWrite(redLedPin, HIGH);

    digitalWrite(greenLedPin, LOW);


    Serial.println("------------------------------");

    Serial.println(" FIRE DETECTED!");

    Serial.println(" WATER PUMP: ON");

    Serial.println(" ALARM: ON");

    Serial.println("------------------------------");

  }

  else {

    // NO FIRE DETECTED

    digitalWrite(relayPin, PUMP_OFF);

    digitalWrite(buzzerPin, LOW);

    digitalWrite(redLedPin, LOW);

    digitalWrite(greenLedPin, HIGH);


    Serial.println("------------------------------");

    Serial.println(" NO FIRE DETECTED");

    Serial.println(" WATER PUMP: OFF");

    Serial.println(" ALARM: OFF");

    Serial.println("------------------------------");

  }

}
