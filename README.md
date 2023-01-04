# SistemEmbedded
### Disusun Oleh
- Sari Wahyuningsih 4.31.20.0.23

# Daftar Laporan JobSheet Sistem Embedded

- [JobSheet 1 DASAR PEMROGRAMAN ESP32 UNTUK PEMROSESAN DATA INPUT/OUTPUT ANALOG DAN DIGITAL](https://github.com/sariwhyu/JobSheet1)
- [JobSheet 1.1 JARINGAN SENSOR NIRKABEL MENGGUNAKAN ESP-NOW](https://github.com/sariwhyu/JobSheet1.1)
- [JobSheet 2 PROTOKOL KOMUNIKASI DAN SENSOR](https://github.com/sariwhyu/JobSheet2)
- [JobSheet 3 TOPOLOGI JARINGAN LOKAL DAN WIFI](https://github.com/sariwhyu/JobSheet3)
- [Tugas Nomor 1 Cayenne(MQTT)+Sensor+Button+Website Monitoring](https://github.com/sariwhyu/TugasNo1)
- [Tugas Nomor 2 Adafruit.IO+Sensor+LED+Suara](https://github.com/sariwhyu/TugasNo2)
- [Tugas Nomor 3 ThingSpeak+Sensor](https://github.com/sariwhyu/TugasNo3)
- [Tugas Nomor 4 ESP Now+IOT](https://github.com/sariwhyu/TugasNo4)


# Tugas Nomor 4 ESP Now+IOT

## Coding

### ESP NOW + IOT

```bash
  #include <esp_now.h>
#include <WiFi.h>
#include "DHT.h"
#define DHTPIN 4
#define DHTTYPE DHT11

uint8_t broadcastAddress[] = {0x94, 0xB5, 0x55, 0x2C, 0x47, 0xB4};
const int espNumber = 1;

typedef struct struct_message {
  int id;
  float temp;
  float hum;
} struct_message;
struct_message myData;
esp_now_peer_info_t peerInfo;

void OnDataSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
  Serial.print("\r\nLast Packet Send Status:\t");
  Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Delivery Success" : "Delivery Fail");
}

// Variable to hold readings
float temperature;
float humidity;

WiFiClient  client;
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  dht.begin();
  WiFi.mode(WIFI_STA);
  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }
  esp_now_register_send_cb(OnDataSent);

  memcpy(peerInfo.peer_addr, broadcastAddress, 6);
  peerInfo.channel = 0;
  peerInfo.encrypt = false;

  if (esp_now_add_peer(&peerInfo) != ESP_OK) {
    Serial.println("Failed to add peer");
    return;
  }
}

void loop() {
  humidity = dht.readHumidity();
  temperature = dht.readTemperature();
  if (isnan(humidity) || isnan(temperature)) {
    Serial.println(F("Failed to read from DHT sensor!"));
    return;
  }
  Serial.print("Temperature (ºC): ");
  Serial.print(temperature);
  Serial.print(" Humidity (%): ");
  Serial.println(humidity);

  myData.id = espNumber;
  myData.temp = temperature;
  myData.hum = humidity;
  esp_err_t result = esp_now_send(broadcastAddress, (uint8_t *) &myData, sizeof(myData));

  if (result == ESP_OK) {
    Serial.println("Sent with success");
  }
  else {
    Serial.println("Error sending the data");
  }

  delay(1000);
}

```

## Hasil

![App Screenshot](https://github.com/sariwhyu/TugasNo4/blob/main/IMG20230101142840.jpg)

![App Screenshot](https://github.com/sariwhyu/TugasNo4/blob/main/IMG20230101143016.jpg)

![App Screenshot](https://github.com/sariwhyu/TugasNo4/blob/main/Screenshot%20(35).png)
