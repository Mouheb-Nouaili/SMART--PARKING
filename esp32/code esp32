#include <NewPing.h>
#include <ESP32Servo.h>
#include <Wire.h>
#include <LiquidCrystal_PCF8574.h>
#include <WiFi.h>
#include <PubSubClient.h>

#define TRIGGER_PINS {12, 27, 25, 32, 18}
#define ECHO_PINS {14, 26, 33, 35, 19}
#define MAX_DISTANCE 200
const int entryBarrierPin = 13, exitBarrierPin = 15;
#define LCD_ADDRESS 0x27
#define LCD_COLUMNS 16
#define LCD_ROWS 2

bool slotOccupied[3] = {false};
bool slotReserved[3] = {false};
unsigned long startTime[3] = {0};
const float ratePerHour = 20;
Servo entryBarrier, exitBarrier;

const char* ssid = "ssid(nom de wifi)", *password = "mot de passe wifi";     // wifi, psswd 
const char* mqttServer = "ipv4adresse"; // ip mte3k fel wifi 
const int mqttPort = 1883;
const char* topicParkingStatus = "parking/status";
const char* topicReservation = "parking/reservation";

const int triggerPins[5] = {12, 27, 25, 32, 18};
const int echoPins[5] = {14, 26, 33, 35, 19};
NewPing sonar[5] = {
  NewPing(triggerPins[0], echoPins[0], MAX_DISTANCE),
  NewPing(triggerPins[1], echoPins[1], MAX_DISTANCE),
  NewPing(triggerPins[2], echoPins[2], MAX_DISTANCE),
  NewPing(triggerPins[3], echoPins[3], MAX_DISTANCE),
  NewPing(triggerPins[4], echoPins[4], MAX_DISTANCE)
};

LiquidCrystal_PCF8574 lcd(LCD_ADDRESS);
WiFiClient espClient;
PubSubClient client(espClient);

void updateSlotStatus(int index) {
  double distance = sonar[index].ping_cm();
  if (distance > 0 && distance < 10 && !slotOccupied[index]) {
    slotOccupied[index] = true;
    startTime[index] = millis();
  } else if (distance >= 10 && slotOccupied[index]) {
    slotOccupied[index] = false;
    unsigned long duration = (millis() - startTime[index]) / 1000;
    Serial.printf("Frais place %d: %.2f dinars\n", index + 1, (duration / 3600.0) * ratePerHour);
  }
}

void callback(char* topic, byte* payload, unsigned int length) {
  String message = "";
  for (int i = 0; i < length; i++) message += (char)payload[i];

  if (String(topic) == topicReservation) {
    if (message.startsWith("reserve")) {
      int slot = message.substring(7).toInt() - 1;
      if (slot >= 0 && slot < 3) slotReserved[slot] = true;
    } else if (message.startsWith("release")) {
      int slot = message.substring(7).toInt() - 1;
      if (slot >= 0 && slot < 3) slotReserved[slot] = false;
    }
  }
}

void reconnect() {
  while (!client.connected()) {
    Serial.println("Connexion au broker MQTT...");
    if (client.connect("ESP32Client")) {
      Serial.println("Connecté au broker MQTT");
      client.subscribe(topicReservation);
    } else delay(5000);
  }
}

void setup() {
  entryBarrier.attach(entryBarrierPin);
  exitBarrier.attach(exitBarrierPin);
  entryBarrier.write(0);
  exitBarrier.write(0);
  Wire.begin();
  lcd.begin(LCD_COLUMNS, LCD_ROWS);
  lcd.setCursor(0, 0);
  lcd.print("S1   S2   S3");
  Serial.begin(9600);
  WiFi.begin(ssid, password);
  client.setServer(mqttServer, mqttPort);
  client.setCallback(callback);
}

void loop() {
  if (WiFi.status() != WL_CONNECTED) {
    Serial.println("Connexion au WiFi...");
    delay(1000);
  } else if (!client.connected()) reconnect();
  client.loop();

  for (int i = 0; i < 3; i++) updateSlotStatus(i);

  bool carAtEntry = (sonar[3].ping_cm() > 0 && sonar[3].ping_cm() < 10);
  bool carAtExit = (sonar[4].ping_cm() > 0 && sonar[4].ping_cm() < 10);

  lcd.setCursor(0, 1);
  for (int i = 0; i < 3; i++) {
    lcd.print(slotOccupied[i] ? "p" : (slotReserved[i] ? "r" : "v"));
    if (i < 2) lcd.print("    ");
  }

  if (WiFi.status() == WL_CONNECTED && client.connected()) {
    String parkingStatus = String(slotOccupied[0]) + "," + String(slotOccupied[1]) + "," + String(slotOccupied[2]);
    client.publish(topicParkingStatus, parkingStatus.c_str());
  }

  entryBarrier.write((carAtEntry && (!slotOccupied[0] || !slotOccupied[1] || !slotOccupied[2])) ? 90 : 0);
  exitBarrier.write(carAtExit ? 90 : 0);

  delay(500);
}
