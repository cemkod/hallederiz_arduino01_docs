# Proje 10 - Dijital Su Terazisi

## Giriş

Bu projede LSM6DS3TR ivmeölçer sensörü kullanarak dijital bir su terazisi yapacaksın. Geleneksel su terazilerindeki hava baloncuğunu LCD ekranda hareket eden bir "O" karakteri ile simüle edeceğiz. Ayrıca tam eğim açısını da göstereceğiz ve yüzey seviye olduğunda LED yanacak.

Bu proje ile öğreneceklerin:
- I2C protokolü ile sensör iletişimi
- İvmeölçer verilerinden açı hesaplama
- LCD ekranda dinamik gösterim
- Matematiksel fonksiyonları kullanma (atan2, map)

## Elektronik

### LSM6DS3TR İvmeölçer Sensörü

LSM6DS3TR, 3 eksende ivme ve açısal hız ölçebilen bir sensör. Bu projede sadece X ekseni ivme verilerini kullanacağız. Sensör I2C protokolü ile Arduino'ya bağlanıyor:

- **VCC**: 5V veya 3.3V besleme
- **GND**: Toprak bağlantısı  
- **SCL**: I2C saat hattı (Arduino A5 pini)
- **SDA**: I2C veri hattı (Arduino A4 pini)
- **I2C Adresi**: 0x6B

### MCP23008 I2C LCD Kontrolcüsü

LCD ekran doğrudan Arduino'ya bağlanmak yerine MCP23008 I2C genişletici entegresi üzerinden kontrol ediliyor. Bu sayede sadece 2 pin (SDA/SCL) kullanarak LCD'yi yönetebiliyoruz.

- **I2C Adresi**: 0x21
- **LCD Boyutu**: 16x2 karakter

### LED Göstergesi

D10 pinine bağlı LED, yüzey seviye olduğunda yanarak görsel geri bildirim sağlıyor.

## Kod

```c
/*
Proje 10 - Dijital Su Terazisi
Arduino Hepsi Bir Arada Başlangıç Seti

Bu projede MPU-6050 ivmeölçer sensörü kullanarak dijital bir su terazisi 
yapacaksın. LCD ekranda hareket eden bir "O" karakteri ile geleneksel su 
terazisindeki hava baloncuğunu simüle edeceğiz. Ayrıca eğim açısını da 
göstereceğiz. Yüzey seviye olduğunda LED yanacak.

MPU-6050 sensörü I2C protokolü ile 0x68 adresinden okunuyor.
LCD ekran MCP23008 I2C expander ile 0x21 adresinden kontrol ediliyor.
LED pin 10'a bağlı ve seviye durumunda yanıyor.
*/

#include <Wire.h>
#include <Adafruit_LiquidCrystal.h>

// LCD MCP23008 I2C adresi (0x21)
Adafruit_LiquidCrystal lcd(1);

// Pin tanımlamaları
const int ledPin = 10;        // Seviye LED'i
const int MPU6050_addr = 0x68; // MPU-6050 I2C adresi

// Değişkenler
float egimAcisi = 0.0;        // X ekseni eğim açısı
int baloncukPozisyonu = 8;    // Baloncuk karakterinin LCD'deki pozisyonu (0-15)
unsigned long sonGuncelleme = 0;
const int guncellemeAraligi = 100; // 100ms aralıklarla güncelle

// Seviye toleransı (derece olarak)
const float seviyeToleransi = 2.0;

void setup() {
  // Pin ayarları
  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW);
  
  // Seri iletişim başlat (hata ayıklama için)
  Serial.begin(9600);
  Serial.println("Dijital Su Terazisi Başlatılıyor...");
  
  // I2C iletişimini başlat
  Wire.begin();
  
  // LCD'yi başlat (16x2)
  lcd.begin(16, 2);
  lcd.clear();
  
  // MPU-6050'yi başlat
  mpu6050Baslat();
  
  // Başlangıç mesajı
  lcd.setCursor(0, 0);
  lcd.print("Su Terazisi");
  lcd.setCursor(0, 1);
  lcd.print("Hazirlanyor...");
  delay(2000);
  lcd.clear();
  
  Serial.println("Sistem hazır!");
}

void loop() {
  // Belirli aralıklarla güncelle
  if (millis() - sonGuncelleme >= guncellemeAraligi) {
    sonGuncelleme = millis();
    
    // MPU-6050'den veri oku
    egimAcisi = egimAcisiOku();
    
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

void mpu6050Baslat() {
  /*
  MPU-6050 sensörünü başlatmak için power management register'ına
  yazı yazmamız gerekiyor. Bu sensör başlangıçta uyku modunda geliyor.
  */
  Wire.beginTransmission(MPU6050_addr);
  Wire.write(0x6B); // PWR_MGMT_1 register
  Wire.write(0);    // Uyku modundan çık (sıfır yaz)
  Wire.endTransmission(true);
  
  Serial.println("MPU-6050 başlatıldı");
  
  // Sensörün dengede olması için kısa bir bekleme
  delay(100);
}

float egimAcisiOku() {
  /*
  MPU-6050'den X ekseni ivme verisini okuyoruz.
  İvme verilerinden eğim açısını hesaplıyoruz.
  */
  Wire.beginTransmission(MPU6050_addr);
  Wire.write(0x3B); // ACCEL_XOUT_H register'ının adresi
  Wire.endTransmission(false);
  Wire.requestFrom(MPU6050_addr, 6, true); // 6 byte veri iste (X,Y,Z için 2'şer byte)
  
  // X, Y, Z ivme verilerini oku (16-bit signed)
  int16_t ivmeX = Wire.read() << 8 | Wire.read();
  int16_t ivmeY = Wire.read() << 8 | Wire.read();
  int16_t ivmeZ = Wire.read() << 8 | Wire.read();
  
  /*
  Eğim açısını hesaplamak için atan2 fonksiyonunu kullanıyoruz.
  Bu fonksiyon, Y ve Z ivme bileşenlerinden X ekseni etrafındaki 
  dönüş açısını hesaplar.
  */
  float acıRadyan = atan2(ivmeY, ivmeZ);
  float acıDerece = acıRadyan * 180.0 / PI;
  
  return acıDerece;
}

void lcdGuncelle() {
  /*
  LCD'nin ilk satırını temizle ve baloncuğu çiz
  İkinci satırda eğim açısını göster
  */
  
  // İlk satır: Baloncuk gösterimi
  lcd.setCursor(0, 0);
  lcd.print("                "); // Satırı temizle
  lcd.setCursor(baloncukPozisyonu, 0);
  lcd.print("O"); // Baloncuk karakteri
  
  // İkinci satır: Eğim açısı
  lcd.setCursor(0, 1);
  lcd.print("Egim: ");
  if (egimAcisi >= 0) {
    lcd.print("+");
  }
  lcd.print(egimAcisi, 1);
  lcd.print("°   "); // Ekstra boşluklar eski verileri temizler
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

1. **Y Ekseni Ekleme**: Kodu değiştirerek Y ekseni eğimini de ölçmeyi dene. LCD'nin üçüncü ve dördüncü satırlarını kullanabilirsin. ^1

2. **Hassasiyet Ayarlama**: `seviyeToleransi` değişkenini değiştirerek LED'in ne kadar hassas olacağını ayarla. ^2

3. **Sesli Uyarı**: Buzzer ekleyerek yüzey seviye olduğunda sesli uyarı ver. ^3

4. **Kalibrasyon**: Başlangıçta 5 saniyelik kalibrasyon süresi ekle ve o andaki açıyı sıfır referansı olarak kullan. ^4

5. **Ortalama Filtresi**: Son 5 ölçümün ortalamasını alarak daha kararlı okumalar elde et. ^5

--8<-- "snippets/sorun-giderme.md"

---

^1: Y ekseni için `atan2(ivmeX, ivmeZ)` formülünü kullan
^2: Hassas uygulamalarda 0.5°, genel kullanımda 5° dene  
^3: `tone()` fonksiyonu ile 1000Hz frekansında 200ms ses çıkar
^4: `for` döngüsü ile 50 ölçüm al ve ortalamasını hesapla
^5: Array kullanarak son 5 değeri sakla ve ortalamasını al