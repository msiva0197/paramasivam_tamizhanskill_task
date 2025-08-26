#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>
#include <DHT.h>


const char* ssid = "Wokwi-GUEST";   
const char* password = "";          


#define BOT_TOKEN "8275429058:AAHG5r8PRMIA2OohpckGtygHG_AocD-1bwE"
#define CHAT_ID "2033174578"   

WiFiClientSecure secured_client;
UniversalTelegramBot bot(BOT_TOKEN, secured_client);


#define MQ2_PIN 34     
#define RED_LED 18     
#define GREEN_LED 19   
#define BUZZER 5       

#define DHTPIN 4       
#define DHTTYPE DHT22  
DHT dht(DHTPIN, DHTTYPE);

int threshold = 2000;      
float tempThreshold = 40.0; 
bool fireAlert = false; 
unsigned long lastAlertTime = 0;   
const unsigned long alertInterval = 30000; 

void beepAlarm() {
  
  for (int i = 0; i < 3; i++) {
    digitalWrite(BUZZER, HIGH);
    delay(500);   
    digitalWrite(BUZZER, LOW);
    delay(200);   
  }
}

void setup() {
  Serial.begin(115200);

  pinMode(MQ2_PIN, INPUT);
  pinMode(RED_LED, OUTPUT);
  pinMode(GREEN_LED, OUTPUT);
  pinMode(BUZZER, OUTPUT);

  digitalWrite(RED_LED, LOW);
  digitalWrite(GREEN_LED, HIGH); 
  digitalWrite(BUZZER, LOW);

  dht.begin();

  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\n✅ WiFi connected");
  
  secured_client.setInsecure();
  bot.sendMessage(CHAT_ID, "✅ Fire Detection System Started", "");
}

void loop() {
  int gasValue = analogRead(MQ2_PIN);
  float temp = dht.readTemperature();
  

  Serial.print("Gas: ");
  Serial.print(gasValue);
  Serial.print(" | Temp: ");
  Serial.print(temp);

  Serial.println("%");

  unsigned long currentTime = millis();

  // 🚨 Fire condition if gas OR temp above limit
  if (gasValue > threshold || temp > tempThreshold) {
    digitalWrite(RED_LED, HIGH);
    digitalWrite(GREEN_LED, LOW);

    beepAlarm(); // 🔊 Alarm pattern

    // Send alert every 30 sec
    if (currentTime - lastAlertTime >= alertInterval) {
      String alertMsg = "🚨 FIRE ALERT! 🔥\n";

      if (gasValue > threshold) {
        alertMsg += "Gas Level = " + String(gasValue) + "\n";
      }
      if (temp > tempThreshold) {
        alertMsg += "🌡 Temp = " + String(temp) + " °C \n";
      }


      bot.sendMessage(CHAT_ID, alertMsg, "");
      lastAlertTime = currentTime;
    }
    fireAlert = true;
  } 
  else if ((gasValue <= threshold && temp <= tempThreshold) && fireAlert) {
    // ✅ Back to safe
    digitalWrite(RED_LED, LOW);
    digitalWrite(GREEN_LED, HIGH);
    digitalWrite(BUZZER, LOW);

    String safeMsg = "✅ SAFE: Conditions back to normal\n"
                     "Gas Level = " + String(gasValue) + "\n"
                     "🌡 Temp = " + String(temp) + " °C\n";

    bot.sendMessage(CHAT_ID, safeMsg, "");
    fireAlert = false;
  }

  delay(200); 
}
