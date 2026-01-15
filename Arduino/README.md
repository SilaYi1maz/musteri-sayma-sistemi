Bu klasörde Arduino kodları bulunmaktadır.
#include <WiFi.h>
#include <HTTPClient.h>

#define GIRIS_SENSOR_PIN D9   // Giriş sensörü
#define CIKIS_SENSOR_PIN D10  // Çıkış sensörü

const char* ssid = "FiberHGW_VEEE39_2.4GHz";
const char* password = "hpfz9jnb3Mwr";

String googleURL = "https://script.google.com/macros/s/AKfycbytA01IAibJxvq-or_GxxXBrgQItnUxJ7wO9waKLYVsZogwegoH4cRH8xLc0dcs8Ts/exec";

int musteriSayisi = 0;

// Önceki durumlar
int oncekiGirisDurum = HIGH;
int oncekiCikisDurum = HIGH;

void setup() {
  Serial.begin(9600);

  pinMode(GIRIS_SENSOR_PIN, INPUT);
  pinMode(CIKIS_SENSOR_PIN, INPUT);

  WiFi.begin(ssid, password);
  Serial.print("WiFi baglaniyor");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi baglandi");
}

void loop() {
  int girisState = digitalRead(GIRIS_SENSOR_PIN);
  int cikisState = digitalRead(CIKIS_SENSOR_PIN);

  // ===== GİRİŞ ALGILAMA =====
  if (girisState == LOW && oncekiGirisDurum == HIGH) {
    musteriSayisi++;
    Serial.println("GIRIS algilandi! Toplam: " + String(musteriSayisi));

    if (WiFi.status() == WL_CONNECTED) {
      HTTPClient http;
      String url = googleURL + "?sayi=" + String(musteriSayisi);
      http.begin(url);
      http.GET();
      http.end();
    }
  }

  // ===== ÇIKIŞ ALGILAMA =====
  if (cikisState == LOW && oncekiCikisDurum == HIGH) {
    if (musteriSayisi > 0) {
      musteriSayisi--;
    }
    Serial.println("CIKIS algilandi! Toplam: " + String(musteriSayisi));

    if (WiFi.status() == WL_CONNECTED) {
      HTTPClient http;
      String url = googleURL + "?sayi=" + String(musteriSayisi);
      http.begin(url);
      http.GET();
      http.end();
    }
  }

  // Önceki durumları güncelle
  oncekiGirisDurum = girisState;
  oncekiCikisDurum = cikisState;

  delay(300);
}
