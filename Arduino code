#include <WiFiS3.h>
#include <R4HttpClient.h>

// Wi-Fi credentials
const char* ssid = "Three_7AD76E";  // Replace with your Wi-Fi SSID
const char* password = "2vLuswu235z3256";  // Replace with your Wi-Fi password

WiFiServer server(80);  // Start HTTP server on port 80
const int buzzerPin = 8;  // Buzzer connected to pin D8

void setup() {
  // Initialize serial communication
  Serial.begin(115200);
  Serial.println("Initializing...");

  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to Wi-Fi...");
  }
  Serial.println("Connected to Wi-Fi!");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());

  // Start the server
  server.begin();
  pinMode(buzzerPin, OUTPUT);
  digitalWrite(buzzerPin, LOW);  // Ensure buzzer is off initially
}

void loop() {
  // Check for client requests
  WiFiClient client = server.available();
  if (client) {
    String request = client.readStringUntil('\r');
    Serial.println(request);

    // If the request contains "/alert", activate the buzzer
    if (request.indexOf("/alert") != -1) {
      Serial.println("Alert received! Activating buzzer...");
      digitalWrite(buzzerPin, HIGH);
      delay(5000);  // Buzzer stays on for 5 seconds
      digitalWrite(buzzerPin, LOW);
      Serial.println("Buzzer deactivated.");
    }

    // Send HTTP response
    client.println("HTTP/1.1 200 OK");
    client.println("Content-Type: text/plain");
    client.println("Connection: close");
    client.println();
    client.println("Alert received!");
    client.stop();
  }
}
