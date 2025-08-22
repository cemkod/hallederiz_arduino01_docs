# Proje 11 - Sinirmatik

## Giriş

Bu projede çok özel ve biraz sinir bozucu bir cihaz yapacaksın: **Sinirmatik**! Bu cihaz karanlıkta ve sessizlikte rastgele bip sesleri çıkarır, ama işin püf noktası şu - eğer birileri onu ararken ışık yakarsa veya ses çıkarırsa zamanlayıcısını iptal eder. Böylece sadece onu arayan kişiler varken bip sesi çıkarır. Çok sinir bozucu ama aynı zamanda çok zekice! 😈

Bu projede öğreneceklerin:
- I²C protokolü ile ışık sensörü kullanımı
- Analog ses sensörü okuma
- Karmaşık zamanlayıcı mantığı (timer cancellation)
- Rastgele sayı üretimi
- Non-blocking kod yazma teknikleri

## Elektronik

Sinirmatik devresinde üç ana bileşen var:

### BH1750 Işık Sensörü (I²C - 0x5C)
Bu sensör ortamdaki ışık miktarını lux cinsinden ölçer. I²C protokolü kullanarak Arduino ile haberleşir. 100 lux'ın altındaki değerleri "karanlık" olarak kabul ediyoruz.

### Ses Sensörü (Analog Pin A1)  
Mikrofondan gelen analog sinyali okuyarak ortamdaki ses seviyesini ölçer. 100'ün altındaki değerleri "sessiz" olarak kabul ediyoruz.

### Buzzer (Digital Pin D3)
PWM destekli pin kullanarak tone() fonksiyonu ile kısa bip sesleri çıkarır.

## Kod

```c
/*
Bu projede bir "Sinirmatik" cihaz yapacaksin. Bu cihaz karanlikta ve sessizlikte
rastgele bip sesi cikarir, ama eger birileri onu ararken isik yakarsa veya ses
cikarirsa zamanlayiciyi iptal eder. Boylece sadece onu arayan kisiler varken
bip sesi cikarir - cok sinir bozucu!
*/

#include <Wire.h>
#include <BH1750.h>

/*******************Isik Sensoru*******************/
BH1750 lightMeter(0x5c);      // BH1750 isik sensoru nesnesi (I2C adresi 0x5c)
float lux;                    // Olculu isik siddetini lux cinsinden saklar

/*******************Ses Sensoru*******************/
#define SOUND_PIN A1          // Ses sensoru analog pin (A1)
int soundThreshold = 100;     // Sessizlik esigi (dusuk deger = sessiz)

/*******************Buzzer Kontrolu*******************/
int buzzerPin = 3;            // Buzzer pin (PWM destekli pin 3)

/*******************Zamanlayici Degiskenleri*******************/
unsigned long timerStart = 0;        // Zamanlayici baslangic zamani
unsigned long randomInterval = 0;    // Rastgele bekleme suresi (milisaniye)
bool timerActive = false;            // Zamanlayici aktif mi?

void setup() {
  /*
  Serial haberlesmeyi baslatiyoruz (115200 baud). Bu sensor degerlerini
  ve cihazin durumunu gormek icin kullanilacak.
  */
  Serial.begin(115200);
  while (!Serial);  // Serial monitor baglantisini bekle

  /*
  I2C haberlesmesini baslatiyoruz. BH1750 isik sensoru I2C protokolu
  kullanarak Arduino ile iletisim kurar.
  
  I2C (Inter-Integrated Circuit), iki telli bir haberleşme protokolüdür:
  - SDA (Serial Data): Veri hattı
  - SCL (Serial Clock): Saat sinyali hattı
  
  Birden fazla cihaz aynı I2C hattına bağlanabilir, her birinin kendine
  özgü adresi vardır. BH1750'nin adresi 0x5C'dir.
  */
  Wire.begin();

  /*
  BH1750 isik sensorunu yuksek cozunurluk modunda baslatiyoruz.
  Bu mod 1 lux hassasiyetinde olcum yapar.
  
  CONTINUOUS_HIGH_RES_MODE: Sürekli yüksek çözünürlük modu
  - Ölçüm hassasiyeti: 1 lux
  - Ölçüm süresi: ~120ms
  - Sürekli ölçüm yapar (tek seferlik değil)
  */
  if (lightMeter.begin(BH1750::CONTINUOUS_HIGH_RES_MODE, 0x5c, &Wire)) {
    Serial.println(F("BH1750 sensoru basariyla baslatildi"));
  } else {
    Serial.println(F("BH1750 sensoru baslatilamadi!"));
  }

  /*
  Pin ayarlarini yapiyoruz:
  - Ses sensoru pin: giris (INPUT) - zaten analog pinler otomatik giris
  - Buzzer pin: cikis (OUTPUT) - ses cikarmak icin
  */
  pinMode(SOUND_PIN, INPUT);
  pinMode(buzzerPin, OUTPUT);

  /*
  Rastgele sayi uretecini baslatmak icin analog pin 0'dan okunan
  gurultu degerini tohum olarak kullaniyoruz.
  
  randomSeed() fonksiyonu, random() fonksiyonunun her çalıştırmada
  farklı sayılar üretmesini sağlar. Tohum olarak analogRead(0) 
  kullanıyoruz çünkü bağlı olmayan analog pin gürültü değeri verir.
  */
  randomSeed(analogRead(0));

  Serial.println("Sinirmatik hazir! Karanlik ve sessiz ortamda bekliyor...");
}

void loop() {
  /*
  Her dongude sensor degerlerini okuyoruz ve zamanlayici durumunu
  kontrol ediyoruz.
  
  ÖNEMLI: Bu kodda delay() kullanmıyoruz (sadece çok kısa 10ms).
  Bunun yerine millis() fonksiyonu ile "non-blocking" zamanlayıcı
  kullanıyoruz. Bu sayede Arduino diğer işleri yapmaya devam edebilir.
  */
  
  // Isik seviyesini olc
  bool lightReady = false;
  if (lightMeter.measurementReady(false)) {  // Bloklamayan okuma
    lux = lightMeter.readLightLevel();
    lightReady = true;
  }

  /*
  measurementReady(false): BH1750'den yeni ölçüm hazır mı kontrol eder
  - false parametresi = bloklamayan kontrol (beklemez)
  - true olsaydı = ölçüm hazır olana kadar beklerdi
  
  Bu sayede sensor ölçüm yaparken kod diğer işleri yapabilir.
  */

  // Ses seviyesini olc
  int soundLevel = analogRead(SOUND_PIN);

  /*
  analogRead() fonksiyonu 0-1023 arası değer döndürür:
  - 0: 0V (tam sessizlik)
  - 1023: 5V (çok yüksek ses)
  
  Ses sensörü ortam sesine göre analog değer üretir.
  */

  // Koşulları kontrol et
  bool isDark = (lux <= 100);      // 100 lux'in altinda karanlik sayiyoruz
  bool isQuiet = (soundLevel < soundThreshold);  // Esik altinda sessiz sayiyoruz

  /*
  Sinirmatik'in ana mantigi:
  1. Hem karanlik HEM sessiz ise: zamanlayiciyi baslat veya devam ettir
  2. Isik yanarsa VEYA ses cikarirsa: zamanlayiciyi iptal et
  3. Zamanlayici dolursa: kisa bir bip sesi cikar ve sifirla
  
  Bu mantık sayesinde cihaz sadece arayan kişiler varken bip çıkarır!
  */

  if (isDark && isQuiet) {
    // Karanlik VE sessiz - zamanlayici baslatilabilir veya devam edebilir
    if (!timerActive) {
      // Yeni zamanlayici baslat
      timerStart = millis();
      /*
      millis() fonksiyonu Arduino'nun açılışından itibaren geçen
      milisaniye sayısını döndürür. Bu değer hiç sıfırlanmaz ve
      yaklaşık 50 gün sonra taşar (overflow).
      */
      
      // 2-5 dakika arasi rastgele bekleme (120000-300000 milisaniye)
      randomInterval = random(120000, 300001);
      /*
      random(min, max) fonksiyonu min (dahil) ile max (hariç) arasında
      rastgele sayı üretir. 300001 yazdık çünkü 300000'i dahil etmek için.
      
      120000 ms = 2 dakika
      300000 ms = 5 dakika
      */
      
      timerActive = true;
      
      Serial.println("Zamanlayici baslatildi! Bekleme suresi: " + 
                    String(randomInterval / 1000) + " saniye");
    } else {
      // Zamanlayici aktif - sure doldu mu kontrol et
      if (millis() - timerStart >= randomInterval) {
        /*
        Zamanlayıcı kontrolü:
        - timerStart: Zamanlayıcının başladığı millis() değeri
        - millis() - timerStart: Geçen süre
        - randomInterval: Beklenmesi gereken süre
        
        Geçen süre >= bekleme süresi ise zamanlayıcı dolmuş demektir.
        */
        
        // Zamanlayici doldu - BIP!
        playBeep();
        
        // Zamanlayiciyi sifirla
        timerActive = false;
        Serial.println("BIP! Zamanlayici sifirlandi.");
      }
    }
  } else {
    // Isik var VEYA ses var - zamanlayiciyi iptal et
    if (timerActive) {
      timerActive = false;
      Serial.println("Zamanlayici iptal edildi! (Birileri ariyor...)");
      /*
      Bu çok önemli! Eğer karanlık ve sessiz değilse (yani birileri
      ışık yakıyorsa veya ses çıkarıyorsa) zamanlayıcıyı iptal ediyoruz.
      Böylece cihaz sadece arayan kişiler varken bip çıkarır.
      */
    }
  }

  /*
  Debug bilgilerini her 2 saniyede bir yazdir
  */
  static unsigned long lastDebugTime = 0;
  /*
  static anahtar kelimesi, değişkenin değerinin fonksiyon çağrıları
  arasında korunmasını sağlar. Normal değişkenler her çağrıda sıfırlanır,
  static değişkenler ise değerlerini korur.
  */
  
  if (lightReady && millis() - lastDebugTime > 2000) {
    Serial.print("Isik: ");
    Serial.print(lux);
    Serial.print(" lx, Ses: ");
    Serial.print(soundLevel);
    Serial.print(", Karanlik: ");
    Serial.print(isDark ? "EVET" : "HAYIR");
    Serial.print(", Sessiz: ");
    Serial.print(isQuiet ? "EVET" : "HAYIR");
    Serial.print(", Zamanlayici: ");
    Serial.println(timerActive ? "AKTIF" : "PASIF");
    
    lastDebugTime = millis();
  }

  // Kisa gecikme - cok hizli donguyu engellemek icin
  delay(10);
  /*
  10ms'lik kısa gecikme, CPU'yu çok fazla yormamak için konulmuştur.
  Bu olmasa loop() saniyede binlerce kez çalışır ve gereksiz enerji harcar.
  */
}

/*
Kisa bir bip sesi cikaran fonksiyon. Sinir bozucu ama cok uzun olmayan
bir ses cikarmak icin 1000 Hz frekansinda 150 milisaniye ses cikariyor.
*/
void playBeep() {
  tone(buzzerPin, 1000, 150);  // 1000 Hz, 150 milisaniye
  /*
  tone() fonksiyonu üç parametre alır:
  1. Pin numarası: Buzzer'ın bağlı olduğu pin
  2. Frekans: Sesin perdesi (Hz cinsinden)
  3. Süre: Sesin çalma süresi (milisaniye, opsiyonel)
  
  1000 Hz orta perdeli bir sestir. Çok tiz değil, çok bas da değil.
  150 ms kısa ama duyulabilir bir süredir.
  */
  
  delay(150);                  // Sesin tamamen cikmasini bekle
  noTone(buzzerPin);          // Buzzer'i kapat
  /*
  noTone() buzzer'dan çıkan sesi durdurur. tone() fonksiyonunda süre
  belirtmiş olsak da, temiz bir ses bitişi için ekstra noTone() çağırıyoruz.
  */
}
```

--8<-- "snippets/yukleme.md"

Kodu yükledikten sonra Serial Monitor'ü açarak (115200 baud) cihazın durumunu izleyebilirsin. Karanlık bir ortamda (ışığı kapat) ve sessiz bir yerde (konuşma) bırak. 2-5 dakika içinde bir bip sesi duyacaksın!

İşin püf noktası: Ses duyunca ışık yakıp onu aramaya başladığın anda zamanlayıcı iptal olacak ve tekrar karanlık + sessiz olana kadar bip çıkarmayacak. 

--8<-- "snippets/sorun-giderme.md"

Bu proje ile I²C haberleşmesi, analog sensör okuma, karmaşık zamanlayıcı mantığı ve non-blocking programlama öğrenmiş oldun. Sinirmatik'in mantığını anlayınca aşağıdakileri yapabilir misin?

## Egzersizler

1. **Zamanlayıcı Sürelerini Değiştir**: Bekleme süresini 30 saniye - 2 dakika arası yapmaya çalış.[^1]

2. **Ses Eşiğini Ayarla**: Mikrofon hassasiyetini ortama göre ayarlayabileceğin bir kod yaz.[^2]

3. **Çoklu Bip**: Tek bip yerine 3 kısa bip çıkaran bir versiyon yap.[^3]

4. **Işık Eşiği Ayarı**: Potansiyometre ile ışık eşiğini ayarlayabilir misin?[^4]

[^1]: random(30000, 120001) kullanman gerekecek.

[^2]: Setup kısmında birkaç saniye boyunca analogRead() yapıp ortalama değeri hesaplayabilirsin.

[^3]: playBeep() fonksiyonunu for döngüsü ile çağırman gerekecek.

[^4]: analogRead(A0) ile potansiyometre değerini okuyup map() fonksiyonu ile lux eşiğine çevirebilirsin.