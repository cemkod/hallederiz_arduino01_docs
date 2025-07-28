# Proje 09 - Sıcaklık ve Nem Sensörü

## Giriş

Bu projede DHT20 sensörü kullanarak çevredeki sıcaklık ve nem değerlerini ölçeceğiz. Ölçtüğümüz verileri LCD ekranda göstererek kendi dijital thermometremizi yapacağız. Bu proje, gerçek zamanlı sensör verilerini okuma ve görüntüleme konusunda tecrübe kazanmanı sağlayacak.

## Elektronik

<img src="../images/dht20-lcd.svg" alt="DHT20 ve LCD devresi" style="height: 400px;">

**DHT20 Sensörü**: Bu sensör hem sıcaklık hem de nem ölçümü yapabilen dijital bir sensör. I2C protokolüyle Arduino'ya bağlanır ve hassas ölçümler sağlar. Sensörün içinde kalibre edilmiş dijital sinyal çıkışı vardır.

**I2C Bağlantısı**: DHT20 sensörü I2C protokolüyle çalışır. Bu protokol sadece 2 hat kullanır:
- **SDA (Serial Data)**: Veri hattı - Arduino'nun A4 pinine bağlı
- **SCL (Serial Clock)**: Saat sinyali hattı - Arduino'nun A5 pinine bağlı

**LCD Ekran**: Önceki projelerde de kullandığımız I2C LCD ekran. Sensör verileri bu ekranda görüntülenecek.

I2C'nin avantajı, birden fazla cihazın aynı 2 hat üzerinde farklı adreslerle çalışabilmesidir. DHT20 sensörü ve LCD ekran aynı I2C hattını paylaşır ama farklı adreslere sahiptir.

## Kod

``` c
// Gerekli kütüphaneleri dahil ediyoruz
#include <DHT20.h>                    // DHT20 sensörü için kütüphane
#include <Adafruit_LiquidCrystal.h>   // LCD ekran için kütüphane

// LCD ekranımızı tanımlıyoruz (0x21 adresinde)
Adafruit_LiquidCrystal lcd(0x21);

// DHT20 sensörümüzü tanımlıyoruz
DHT20 DHT;

void setup() {
  // Program başladığında bir kere çalışan fonksiyon
  
  /*
  LCD ekranı başlatıyoruz. İlk parametre sütun sayısı (16),
  ikinci parametre satır sayısı (2). Yani 16x2 boyutunda bir ekran.
  */
  lcd.begin(16, 2);
  
  /*
  I2C haberleşmesini başlatıyoruz. Wire kütüphanesi Arduino'da
  I2C protokolünü yönetir. begin() fonksiyonu I2C'yi aktifleştirir.
  */
  Wire.begin();
  
  /*
  DHT20 sensörünü başlatıyoruz. Bu fonksiyon sensörle ilk iletişimi
  kurar ve kalibrasyonu yapar.
  */
  DHT.begin();
}

void loop() {
  // Bu fonksiyon sürekli tekrar eder
  
  /*
  Sensörden veri okuyoruz. read() fonksiyonu sensöre ölçüm yapması
  için komut gönderir ve cevabı bekler. Fonksiyon başarı durumunu
  gösteren bir status kodu döndürür.
  */
  int status = DHT.read();

  /*
  LCD ekranın sol üst köşesine (0,0 pozisyonuna) git.
  home() fonksiyonu setCursor(0,0) ile aynı işi yapar.
  */
  lcd.home();
  
  /*
  Sıcaklık bilgisini ekrana yazdırıyoruz.
  "Sck: " yazısını yazdır (Sıcaklık'ın kısaltması)
  */
  lcd.print("Sck: ");
  
  /*
  DHT20 sensöründen sıcaklık değerini alıp yazdırıyoruz.
  getTemperature() fonksiyonu float tipinde Celsius cinsinden
  sıcaklık değeri döndürür.
  */
  lcd.print(DHT.getTemperature());
  
  // Santigrat simgesini yazdır
  lcd.print("C");
  
  /*
  LCD ekranın ikinci satırına (0,1) konumuna git.
  İlk parametre sütun (0), ikinci parametre satır (1).
  */
  lcd.setCursor(0,1);
  
  // "Nem: %" yazısını yazdır
  lcd.print("Nem: %");
  
  /*
  DHT20 sensöründen nem değerini alıp yazdırıyoruz.
  getHumidity() fonksiyonu float tipinde yüzde cinsinden
  nem değeri döndürür.
  */
  lcd.print(DHT.getHumidity());

  /*
  100 milisaniye (0.1 saniye) bekle. Bu süre sensörün
  yeni ölçüm yapması için yeterli. Çok sık okuma yapmak
  sensörü yorar ve doğruluğu azaltabilir.
  */
  delay(100);
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında LCD ekranında anlık sıcaklık ve nem değerlerini göreceksin. Sensöre nefes verirsen nem değerinin arttığını, eline alırsan sıcaklığın yükseldiğini gözlemleyebilirsin.

--8<-- "snippets/sorun-giderme.md"

Bu projede I2C protokolü, dijital sensör kullanımı ve gerçek zamanlı veri okumayı öğrenmiş oldun. Aşağıdaki egzersizleri yapabilir misin?

1. Sıcaklık 25°C'nin üzerindeyse LED yakar, altındaysa söndüren bir alarm sistemi yapabilir misin?[^1]
2. Nem %60'ın üzerine çıkarsa buzzer'dan uyarı sesi çıkaran bir sistem yapabilir misin?[^2]

[^1]: if koşulu ve digitalWrite() fonksiyonunu kullanman gerekecek.
[^2]: if koşulu ve tone() fonksiyonunu kullanman gerekecek.