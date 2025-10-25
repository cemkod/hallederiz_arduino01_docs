# Proje 10 - Dijital Su Terazisi

## Giriş

Bu projede LSM6DS3TR ivmeölçer sensörü kullanarak dijital bir su terazisi yapacaksın. Geleneksel su terazilerindeki hava baloncuğunu LCD ekranda hareket eden bir "O" karakteri ile simüle edeceğiz. Ayrıca tam eğim açısını da göstereceğiz ve yüzey seviye olduğunda LED yanacak.

Bu proje ile öğreneceklerin:
- I2C protokolü ile sensör iletişimi
- İvmeölçer verilerinden açı hesaplama
- LCD ekranda dinamik gösterim
- Matematiksel fonksiyonları kullanma (atan2, map)

## Yeni Kavramlar

### İvmeölçer ve Açı Hesaplama Matematiği

**İvmeölçer Çalışma Prensibi**: İvmeölçer, yerçekimi kuvvetinin farklı eksenlere olan yansımalarını ölçer. Sensör düz durduğunda yerçekimi kuvveti sadece Z eksenine etki eder. Ancak sensör eğildiğinde, aynı yerçekimi kuvveti hem X hem de Z eksenine farklı oranlarda yansır.

<img src="../images/Ivmeolcer-atan.svg" alt="İvmeölçer açı hesaplama prensibi">

Yukarıdaki diyagramda gösterildiği gibi:
- Yerçekimi kuvveti her zaman aynı büyüklükte ve aşağı yönlü
- Sensör α açısıyla eğildiğinde, bu kuvvet X ve Z eksenlerine farklı oranlarda yansır
- X ekseni ölçümü: `acceleration.x`
- Z ekseni ölçümü: `acceleration.z`

### atan2() Fonksiyonu ile Açı Hesaplama

**atan2() fonksiyonu**, iki boyutlu koordinat sisteminde açı, verilen x ve y uzunluklarindan açıyı hesaplamak için kullanılan matematiksel fonksiyondur. atan2 fonksiyonu açıyı radyan cinsinden verir.

**Radyan** Açıları ölçmekte kullandığın 0-360 derece sisteminin bir alternatifidir. Bu sistemde açılar 0 ile 360 derece yerine, 0 ile 2π radyan arasında bir değer alır. Matematik ve programlamada bazen açılar için bu sistemi kullanırız.

- Tam bir daire (360°) = 2π radyan

- Yarım daire (180°) = π radyan

- Dik açı (90°) = π/2 radyan

- Arduino'da `PI` sabiti π sayısını temsil eder (yaklaşık 3.14159)

- Dereceye çevirmek için `180.0 / PI` ile çarpılır (çünkü π radyan = 180°)


```c
float aciRadyan = atan2(accelEvent.acceleration.x, accelEvent.acceleration.z);
float aciDerece = aciRadyan * 180.0 / PI;
```

- Sensör sola eğilirse: X negatif, Z pozitif → Negatif açı

- Sensör sağa eğilirse: X pozitif, Z pozitif → Pozitif açı

- Sensör düz durur: X sıfıra yakın, Z maksimum → Açı sıfıra yakın

## Elektronik

### LSM6DS3TR İvmeölçer Sensörü

LSM6DS3TR, 3 eksende ivme ve açısal hız ölçebilen bir sensör. Bu projede sadece X ekseni ivme verilerini kullanacağız. Sensör I2C protokolü ile Arduino'ya bağlanıyor:

- **VCC**: 5V veya 3.3V besleme
- **GND**: Toprak bağlantısı  
- **SCL**: I2C saat hattı (Arduino A5 pini)
- **SDA**: I2C veri hattı (Arduino A4 pini)
- **I2C Adresi**: 0x6B

### LED Göstergesi

D10 pinine bağlı LED, yüzey terazide olduğunda yanarak görsel geri bildirim sağlıyor.

## Kod

!!! note "Kütüphane kurulumu gerekli"
    Bu projenin çalışması için bazı kütüphanelerin kurulmuş olması  gerektiriyor. Detaylı kütüphane kurulum talimatları için [Kütüphane kurma](kutuphane-kurma.md) sayfasına bakabilirsin.

Bu proje için gerekli kütüphaneler:

- **Adafruit LSM6DS**
- **Adafruit BusIO**
- **Adafruit Unified Sensor**
- **Adafruit LiquidCrystal Attiny85**

```cpp
/*
Proje 10 - Dijital Su Terazisi
Halledersiniz Arduino 01

Bu projede LSM6DS3TR ivmeölçer sensörü kullanarak dijital bir su terazisi
yapacaksın. LCD ekranda hareket eden bir "O" karakteri ile geleneksel su
terazisindeki hava baloncuğunu simüle edeceğiz. Ayrıca eğim açısını da
göstereceğiz. Yüzey seviye olduğunda LED yanacak.

LSM6DS3TR sensörü I2C protokolü ile 0x6B adresinden okunuyor.
LCD ekran MCP23008 I2C expander ile 0x21 adresinden kontrol ediliyor.
LED pin 10'a bağlı ve seviye durumunda yanıyor.
*/

#include <Wire.h>
#include <Adafruit_LiquidCrystal.h>
#include <Adafruit_LSM6DS3TRC.h>
#include <Adafruit_Sensor.h>

// LCD MCP23008 I2C adresi (0x21)
Adafruit_LiquidCrystal lcd(0x21);
// Adafruit LSM6DS sensör nesnesi
Adafruit_LSM6DS3TRC lsm6ds;

// Pin tanımlamaları
const int ledPin = 10;        // Seviye LED'i

// Değişkenler
float egimAcisi = 0.0;        // X ekseni eğim açısı
int baloncukPozisyonu = 8;    // Baloncuk karakterinin LCD'deki pozisyonu (0-15)
int baloncukPozisyonuEski = baloncukPozisyonu; //eski baloncuk pozisyonunu. bununla ekrani guncelleyip guncellemeyecegimize karar verecegiz
unsigned long sonGuncelleme = 0;
const int guncellemeAraligi = 100; // 100ms aralıklarla güncelle

// Seviye toleransı (derece olarak)
const float seviyeToleransi = 2.0;

void setup() {
  // Pin ayarları
  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW);

  // Seri iletişim başlat (hata ayıklama için)
  Serial.begin(115200);
  Serial.println("Dijital Su Terazisi Başlatılıyor...");

  // I2C iletişimini başlat
  Wire.begin();

  // LSM6DS3TR sensörünü başlat
  if (!lsm6ds.begin_I2C(0x6B)) {
    Serial.println("LSM6DS3TR sensörü 0x6B adresinde bulunamadı.");
    while (1) {
      delay(10);
    }
  }
  Serial.println("LSM6DS3TR bulundu!");

  // İvmeölçer aralığını 4G olarak ayarla (orijinal koddaki gibi)
  lsm6ds.setAccelRange(LSM6DS_ACCEL_RANGE_4_G);

  // LCD'yi başlat (16x2)
  while (!lcd.begin(16, 2)) {
    Serial.println("LCD başlatılamadı. Bağlantıları kontrol edin.");
    delay(50);
  }
  lcd.clear();

  Serial.println("Sistem hazır!");
}

void loop() {
  // Belirli aralıklarla güncelle
  if (millis() - sonGuncelleme >= guncellemeAraligi) {
    sonGuncelleme = millis();

    // Sensör verilerini okumak için event nesneleri oluştur
    sensors_event_t accel;
    sensors_event_t gyro;
    sensors_event_t temp;
    lsm6ds.getEvent(&accel, &gyro, &temp);

    // Eğim açısını hesapla
    egimAcisi = egimAcisiHesapla(accel);

    // Eski baloncuk pozisyonunu sakla, bunu ekrani guncelleyip guncellemeyecegimizi gormek icin kullanacagiz
    baloncukPozisyonuEski = baloncukPozisyonu;
    // Baloncuk pozisyonunu hesapla (-45° ile +45° arası)
    baloncukPozisyonu = map(constrain(egimAcisi * 10, -450, 450), -450, 450, 0, 15);
    

    // LCD'yi güncelle
    lcdGuncelle();

    // Seviye kontrolü ve LED
    seviyeKontrolu();

    // Seri porta debug bilgisi gönder
    Serial.print("Eğim: ");
    Serial.print(egimAcisi, 1);
    Serial.print("° | Baloncuk: ");
    Serial.print(baloncukPozisyonu);
    Serial.print(" | Seviye: ");
    Serial.println(abs(egimAcisi) <= seviyeToleransi ? "EVET" : "HAYIR");
  }
}

float egimAcisiHesapla(sensors_event_t &accelEvent) {
  /*
  Eğim açısını hesaplamak için atan2 fonksiyonunu kullanıyoruz.
  Bu fonksiyon, X ve Z ivme bileşenlerinden Y ekseni etrafındaki
  dönüş açısını hesaplar. Adafruit kütüphanesi verileri m/s^2 olarak verir.
  */
  float aciRadyan = atan2(accelEvent.acceleration.x, accelEvent.acceleration.z);
  float aciDerece = aciRadyan * 180.0 / PI;

  return aciDerece;
}

void lcdGuncelle() {
  /*
  LCD'nin ilk satırını temizle ve baloncuğu çiz
  İkinci satırda eğim açısını göster
  */

  // İlk satır: Baloncuk gösterimi
  if(baloncukPozisyonuEski != baloncukPozisyonu){
    // baloncuk pozisyonu degismis. bu yuzden satırı güncelliyoruz
    // bunu yapma nedenimiz ekranı her güncellediğimizde ekranın titremesi, gereksiz titremeden kaçınmak için sadece pozisyon değişince güncelliyoruz
    lcd.setCursor(0, 0);
    lcd.print("                "); // Satırı temizle
    lcd.setCursor(baloncukPozisyonu, 0);
    lcd.print("O"); // Baloncuk karakteri
  }

  // İkinci satır: Eğim açısı
  lcd.setCursor(0, 1);
  lcd.print("Egim: ");
  if (egimAcisi >= 0) {
    lcd.print("+");
  }
  lcd.print(egimAcisi, 1);
  lcd.print("      "); // Ekstra boşluklar eski verileri temizler
}

void seviyeKontrolu() {
  /*
  Eğim açısı tolerans aralığında ise (neredeyse seviye) LED'i yak,
  değilse söndür. Bu, kullanıcıya yüzeyin seviye olduğunu gösterir.
  */
  if (abs(egimAcisi) <= seviyeToleransi) {
    digitalWrite(ledPin, HIGH); // LED'i yak
  } else {
    digitalWrite(ledPin, LOW);  // LED'i söndür
  }
}
```

## Kodun Açıklaması

### I2C İletişimi

Bu projede iki farklı I2C cihazı kullanıyoruz:

- **MPU-6050 (0x68)**: İvmeölçer sensörü

- **MCP23008 (0x21)**: LCD kontrolcüsü

I2C protokolü, sadece 2 hat (SDA/SCL) kullanarak birden fazla cihazla iletişim kurmamızı sağlıyor.

### İvme Verilerinden Açı Hesaplama

```c
float aciRadyan = atan2(ivme[0], ivme[2]); // X ve Z eksenlerini kullan
float aciDerece = aciRadyan * 180.0 / PI;
```

`atan2()` fonksiyonu, X ve Z ivme bileşenlerini kullanarak Y ekseni etrafındaki dönüş açısını hesaplıyor. Bu matematiksel işlem, gravity vektörünün yönünden eğim açısını bulma mantığına dayanıyor.

### Baloncuk Simülasyonu

```c
baloncukPozisyonu = map(constrain(egimAcisi * 10, -450, 450), -450, 450, 0, 15);
```

Eğim açısını LCD'nin 16 karakter genişliğinde bir pozisyona dönüştürüyoruz. `constrain()` fonksiyonu değeri -45° ile +45° arasında sınırlıyor, `map()` fonksiyonu ise bu aralığı 0-15 LCD pozisyonlarına çeviriyor.

### Zaman Kontrollü Güncelleme

```c
if (millis() - sonGuncelleme >= guncellemeAraligi) {
```

`millis()` fonksiyonu ile non-blocking zaman kontrolü yapıyoruz. Bu sayede Arduino her 100ms'de bir ekranı güncellemiş oluyor.

--8<-- "snippets/yukleme.md"

## Egzersizler

1. **Y Ekseni Ekleme**: Kodu değiştirerek Y ekseni eğimini de ölçmeyi dene. ^1

2. **Hassasiyet Ayarlama**: `seviyeToleransi` değişkenini değiştirerek LED'in ne kadar hassas olacağını ayarla. ^2

3. **Sesli Uyarı**: Buzzer ekleyerek yüzey seviye olduğunda sesli uyarı ver. ^3

4. **Kalibrasyon**: Setin üzerindeki butona basıldığında o anda ölçülen değeri sıfır kabul etmesini sağlayabilir misin? Böylece ölçülen bir eğimi başka bir yere taşımak çok kolay olur.


--8<-- "snippets/sorun-giderme.md"

---

^1: Y ekseni için `atan2(ivmeX, ivmeZ)` formülünü kullan
^2: Hassas uygulamalarda 0.5°, genel kullanımda 5° dene  
^3: `tone()` fonksiyonu ile 1000Hz frekansında 200ms ses çıkar
^4: Butona basıldığındaki değeri bir değişkende saklayıp, ölçümlerin o değerden farkını kullan.
