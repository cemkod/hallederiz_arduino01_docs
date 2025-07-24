# Proje 07 - Araba Simulasyonu

## Giriş

Bu projede basit bir araba simulasyonu yapacak ve servo motorun nasıl kontrol edildiğini ve dizileri kullanmayı (array) öğreneceksin.

## Elektronik

<img src="../images/servo.svg" alt="Servo devresi" style="height: 400px;">




``` c
#include <Servo.h>

// Pin tanımları
const int BUTON_PIN = 7;
const int BUZZER_PIN = 3;
const int SERVO_PIN = 9;

// Servo motor nesnesi
Servo rpmOlcer;

// Araba simülasyon değişkenleri
int vites = 0;
int rpm = 800;  // Başlangıç rölanti RPM

// Her vites için RPM aralıkları (min, max)
const int rpmMin[3] = {
  800,  // 1. Vites
  1500, // 2. Vites
  2500  // 3. Vites
};

const int rpmMax[3] = {
  6000, // 1. Vites
  4500, // 2. Vites
  5000  // 3. Vites
};

// Vites hızlanma oranları (her güncellemede RPM artışı)
const int hizlanmaIvme[3] = {
  500,  // 1. Vites
  300,  // 2. Vites
  150    // 3. Vites
};

// Vites yavaşlama oranları (her güncellemede RPM azalması)
const int yavaslamaIvme[3] = {
  700,  // 1. Vites
  400,   // 2. Vites
  200    // 3. Vites
};

// Zaman değişkenleri
unsigned long sonGuncelleme = 0;
const int GUNCELLEME_ARALIGI = 100;  // Her 100ms'de bir güncelle

void setup() { 
  // Buton'u giris, buzzer'i cikis olarak ayarliyoruz
  pinMode(BUTON_PIN, INPUT);
  pinMode(BUZZER_PIN, OUTPUT);
  
  // Servo motorun bağlı olduğu pini tanımlıyoruz
  rpmOlcer.attach(SERVO_PIN);
}

void loop() {
  // Bu kez ana döngümüzde iki farklı iş yapacağız.
  // İlk olarak yapabildiğimiz
  unsigned long suankiZaman = millis();
  

  // Araba simülasyonunu her 100ms'de güncelle
  if (suankiZaman - sonGuncelleme >= GUNCELLEME_ARALIGI) {
    // Buton durumunu oku (pull-up direnci nedeniyle tersine)
    arabaSimulasyonuGuncelle();
    motorSesiGuncelle();
    sonGuncelleme = suankiZaman;
  }
}


void arabaSimulasyonuGuncelle() {
  bool butonBasili = !digitalRead(BUTON_PIN);

  // Buton durumuna göre hedef RPM belirle
  if (butonBasili) {
    rpm += hizlanmaIvme[vites];
  } else {
    rpm -= yavaslamaIvme[vites];
  }
  rpm = constrain(rpm, rpmMin[vites], rpmMax[vites]);
  // Otomatik vites değiştirme
  vitesKontrol();

  
  // Servo pozisyonunu güncelle (RPM'yi servo açısına dönüştür)
  int servoAcisi = map(rpm, 800, 6000, 180, 0);
  rpmOlcer.write(servoAcisi);

}

void vitesKontrol() {
  // RPM çok yüksekse vites yükselt
  if (rpm >= rpmMax[vites]) {
    if (vites < 2) {
      vites++;
      // Yeni vites için RPM ayarla
      rpm = rpmMin[vites];
    }
  } else if (rpm <= rpmMin[vites]) {
    if (vites > 0) {
      // RPM çok düşükse ve gaz bırakılmışsa vites düşür
      vites--;
      // Yeni vites için RPM ayarla
      rpm = rpmMax[vites];
    }
  }
  
}

void motorSesiGuncelle() {
  // Çıkaracağımız motor sesinin frekansını, rpm'in 25'te biri olarak hesap ediyoruz
  int frekans = rpm / 25;
  
  tone(BUZZER_PIN, frekans);
}

```
