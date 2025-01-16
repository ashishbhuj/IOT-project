import time
from grove.factory import Factory
from seeed_dht import DHT
import requests

# ThingSpeak details
CHANNEL_ID = "2735512"
WRITE_API_KEY = "17TNT3YWIJS6BMBU"
THINGSPEAK_URL = f"http://api.thingspeak.com/update"  # Changed to http

# Arduino details
ARDUINO_IP = "192.168.0.209"  # Replace with your Arduino's IP
ARDUINO_PORT = 80
ARDUINO_ALERT_URL = f"http://{ARDUINO_IP}:{ARDUINO_PORT}/alert"

# Initialize DHT sensor on GPIO pin 5 (corresponds to Grove D5 port)
dht_sensor = DHT('11', 5)  # '11' for DHT11, '5' for GPIO pin

# Initialize Grove 16x2 LCD
lcd = Factory.getDisplay("JHD1802")

def upload_to_thingspeak(temperature, humidity):
    """Send temperature and humidity data to ThingSpeak."""
    try:
        response = requests.post(
            THINGSPEAK_URL,
            params={
                "api_key": WRITE_API_KEY,
                "field1": temperature,
                "field2": humidity,
            },
        )
        if response.status_code == 200:
            print("Data sent to ThingSpeak successfully.")
        else:
            print(f"Failed to send data to ThingSpeak: {response.status_code}")
    except Exception as e:
        print(f"Error uploading to ThingSpeak: {e}")

def send_alert_to_arduino():
    """Send an alert to the Arduino when temperature exceeds threshold."""
    try:
        response = requests.get(ARDUINO_ALERT_URL)
        if response.status_code == 200:
            print("Alert sent to Arduino successfully.")
        else:
            print(f"Failed to send alert to Arduino: {response.status_code}")
    except Exception as e:
        print(f"Error sending alert to Arduino: {e}")

def display_on_lcd(temperature, humidity):
    """Display temperature and humidity on the Grove LCD."""
    try:
        lcd.setCursor(0, 0)
        lcd.write(f"Temp: {temperature:.1f}C")
        lcd.setCursor(1, 0)
        lcd.write(f"Hum: {humidity:.1f}%")
    except Exception as e:
        print(f"Error displaying on LCD: {e}")

def main():
    THRESHOLD = 30.0  # Temperature threshold for the alert
    while True:
        try:
            temp, hum = dht_sensor.read()
            if temp is not None and hum is not None:
                print(f"Temperature: {temp:.1f}C, Humidity: {hum:.1f}%")

                # Display data on LCD
                display_on_lcd(temp, hum)

                # Upload data to ThingSpeak
                upload_to_thingspeak(temp, hum)

                # Send alert to Arduino if temperature exceeds threshold
                if temp > THRESHOLD:
                    print("Temperature exceeded threshold! Sending alert to Arduino...")
                    send_alert_to_arduino()
            else:
                print("Failed to read from DHT sensor.")

            # Wait 15 seconds (ThingSpeak rate limit)
            time.sleep(15)
        except KeyboardInterrupt:
            print("Program stopped by user.")
            break
        except Exception as e:
            print(f"Unexpected error: {e}")
            time.sleep(15)

if __name__ == "__main__":
    main()
