# Proje 08 - LCD Demo Animasyonu

## Giriş

Bu projede Arduino ile etkileşimli bir LCD demo animasyonu yapacağız. LCD ekranında yazı yazma, geri silme, müzik çalma ve LED kontrolü gibi birden fazla özelliği bir arada kullanacaksın. Sanki bilgisayar başında yazı yazıp hata düzeltiyormuş gibi gerçekçi bir animasyon göreceksin!

## Elektronik

<img src="../images/lcd-buzzer-led.svg" alt="LCD Demo devresi" style="height: 400px;">

Bu projede üç farklı modülü birlikte kullanıyoruz:

**LCD Ekran (16x2)**: I2C protokolüyle Arduino'ya bağlı. SDA ve SCL pinleri üzerinden veri alışverişi yapıyor. LCD'nin 0x21 adresinde çalıştığını varsayıyoruz.

**Buzzer**: Pin 3'e bağlı ve önceki buzzer projesindeki gibi çalışıyor. Müzik notalarını çalmak için tone() fonksiyonunu kullanacağız.

**LED**: Pin 10'a bağlı ve animasyon sonunda yanıp sönme efekti yapacak.

LCD'nin cursor() ve blink() özelliklerini kullanarak gerçek bir yazı editörü gibi imleç görünümü sağlayacağız. Bu da animasyonu daha gerçekçi kılacak.

## Hatırlayalım

Bu projede **Proje 05 - Buzzer** dersinde öğrendiğin iki önemli kavramı kullanacağız:

**Array (Dizi)**: Aynı türde birden fazla değeri bir arada saklamamızı sağlar. Müzik notalarının frekanslarını ve sürelerini tutmak için kullanacağız.

**For Loop (Döngü)**: Aynı işlemi tekrar tekrar yapmamızı sağlar. Müzik dizisindeki tüm notaları sırayla çalmak için kullanacağız.

Bu kavramları buzzer dersinde detaylı öğrenmiştik. Şimdi onları LCD kontrolü ve animasyonla birleştireceğiz!

## Kod

``` c
#include <Adafruit_LiquidCrystal.h>
Adafruit_LiquidCrystal lcd(1);

const int tick = 200;
const int buzzerPin = 3;
const int ledPin = 10;
const String originalText = " HALLEDERiZ";
const String correctedText = " HALLEDERSiNiZ";
bool animationComplete = false;

// Note frequencies
const int RE_4 = 587;
const int DO_D_4 = 554;
const int SI_3 = 494;
const int ES = -1;  // Rest

// Melody definition
int melodi_freq[] = {
  RE_4, ES, DO_D_4, ES, SI_3, ES,
  RE_4, ES, DO_D_4, ES, SI_3, ES,
  RE_4, ES, DO_D_4, ES, SI_3
};

int sureler[] = {
  1,1,1,1,1,1,
  1,1,1,1,1,1,
  1,1,1,1,2
};

const int noteDuration = 94; // 32nd note duration at 80 BPM

/*
String veri tipi, metinsel verileri saklamak için kullanılır.
Arduino'da String sınıfı birçok yararlı fonksiyon sunar:
- length(): Metindeki karakter sayısını verir
- charAt(index): Belirtilen pozisyondaki karakteri döndürür
*/

/*
Bu fonksiyon buzzer'dan belirtilen frekansta ve sürede ses çıkarır.
Eğer frekans ES (-1) ise, sessizlik (rest) anlamına gelir.
*/
void beep(int freq, int duration) {
  if (freq != ES) {
    tone(buzzerPin, freq, duration);
  }
  delay(duration);
  noTone(buzzerPin);
}

/*
Melodi çalma fonksiyonu. Dizi boyutunu sizeof() ile hesaplayarak
kaç nota olduğunu otomatik olarak buluyor.
*/
void melodi_cal() {
  for (int i = 0; i < sizeof(melodi_freq)/sizeof(melodi_freq[0]); i++) {
    int duration = sureler[i] * noteDuration;
    beep(melodi_freq[i], duration);
    // Short pause between notes (except for rests)
    if (melodi_freq[i] != ES) {
      delay(10);
    }
  }
}

void setup() {
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
  Serial.println("BEGIN");
  
  /*
  LCD ekranını başlatıyoruz. 16x2 boyutunda (16 karakter, 2 satır).
  cursor() fonksiyonu imleç çizgisini görünür kılar.
  blink() fonksiyonu imleci yanıp söndürür.
  */
  lcd.begin(16, 2);
  lcd.cursor();
  lcd.blink();
}

void loop() {
  /*
  static değişkenler, fonksiyon her çağrıldığında değerlerini korur.
  Normal değişkenler her seferinde sıfırlanırken, static değişkenler
  son değerlerini hatırlar. Bu sayede animasyonun hangi aşamada 
  olduğunu takip edebiliriz.
  */
  static unsigned long nextUpdate = 0;
  static int phase = 0;
  static int charPosition = 0;
  static bool ledState = false;
  
  /*
  Zamanlama kontrolü. Animasyonun çok hızlı geçmemesi için 
  belirtilen 'tick' süresini bekliyoruz.
  */
  if (millis() < nextUpdate) {
    return;
  }
  nextUpdate = millis() + tick;

  /*
  Switch-case yapısı ile farklı animasyon aşamalarını kontrol ediyoruz.
  Bu, state machine (durum makinesi) olarak adlandırılan bir programlama
  tekniğidir. Her aşama farklı bir görev yapar.
  */
  switch (phase) {
    case 0: // Type out original text
      if (charPosition < originalText.length()) {
        /*
        setCursor(x, y) ile LCD'de imleç pozisyonunu belirliyoruz.
        charAt() ile String'den belirtilen pozisyondaki karakteri alıyoruz.
        */
        lcd.setCursor(charPosition, 0);
        lcd.print(originalText.charAt(charPosition));
        beep(500, 50); // Keypress beep
        charPosition++;
      } else {
        phase = 1;
        charPosition = originalText.length() - 1;
        nextUpdate = millis() + (tick * 10); // Longer pause after typing
      }
      break;
      
    case 1: // Backspace to remove errors
      if (charPosition >= 9) { // Backspace to position before 'iZ'
        /*
        Geri silme efekti: imleç pozisyonuna boşluk karakteri yazdırarak
        o pozisyondaki karakteri siliyoruz.
        */
        lcd.setCursor(charPosition, 0);
        lcd.print(' ');
        beep(100, 30); // Slightly shorter delete beep
        charPosition--;
      } else {
        phase = 2;
        charPosition = 9; // Start position for correction ('S')
        nextUpdate = millis() + tick * 2; // Pause before typing correction
      }
      break;
      
    case 2: // Type corrected text ("RSiNiZ")
      if (charPosition - 7 < correctedText.length() - 7) {
        lcd.setCursor(charPosition, 0);
        lcd.print(correctedText.charAt(charPosition));
        beep(500, 50); // Keypress beep
        charPosition++;
      } else {
        phase = 3; // Animation complete
        nextUpdate = millis() + tick * 5;
      }
      break;
      
    case 3: // Animation complete
      if (!animationComplete) {
        melodi_cal(); // Play the melody
        animationComplete = true;
        /*
        Animasyon bittiğinde imleç efektlerini kapatıyoruz.
        noCursor() ve noBlink() fonksiyonları ile.
        */
        lcd.noCursor();
        lcd.noBlink();
      } else {
        phase = 4;
        nextUpdate = millis() + 1000;
      }
      break;
    
    case 4:
      /*
      LED'i sürekli yanıp söndürme aşaması.
      ledState değişkenini tersine çevirerek (!) LED'in durumunu değiştiriyoruz.
      */
      ledState = !ledState;
      digitalWrite(ledPin,ledState);
      nextUpdate = millis() + tick * 4;
      break;
  }
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında LCD'de yazı yazma, düzeltme, müzik çalma ve LED yanıp sönme animasyonunu göreceksin.

--8<-- "snippets/sorun-giderme.md"

Bu projede state machine programlama, String manipülasyonu ve çoklu modül kontrolünü öğrenmiş oldun. Aşağıdaki egzersizleri yapabilir misin?

1. Farklı bir kelime yazıp düzelten animasyon yapabilir misin?[^1]
2. Animasyon bittiğinde LED'in yanıp sönme hızını potansiyometreyle kontrol edebilir misin?[^2]

[^1]: originalText ve correctedText değişkenlerini değiştirmen gerekecek.
[^2]: analogRead() ile potansiyometre değerini okuyup delay süresini ayarlaman gerekecek.