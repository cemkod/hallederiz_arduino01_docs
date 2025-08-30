# Kumanda Kodları Referansı

Bu sayfada kit ile birlikte gelen IR (kızılötesi) kumandasının buton kodları listelenmiştir. Bu kodları Arduino projelerinde IR alıcı modülü ile kullanabilirsiniz.

## Buton Kodları Tablosu

| Buton | Hex Kod (0x) |
|-------|----------|
| CH- | 45 |
| CH | 46 |
| CH+ | 47 |
| << | 44 |
| >> | 40 |
| PLAY/PAUSE | 43 |
| - | 7 |
| + | 15 |
| EQ | 9 |
| 0 | 16 |
| 100+ | 19 |
| 200+ | D |
| 1 | C |
| 2 | 18 |
| 3 | 5E |
| 4 | 8 |
| 5 | 1C |
| 6 | 5A |
| 7 | 42 |
| 8 | 52 |
| 9 | 4A |

## Arduino Kullanımı

Bu kodları Arduino projelerinizde kullanmak için IR alıcı kütüphanesi (örneğin IRremote) ile karşılaştırabilirsiniz:

```c
if (results.value == 0x45) {
  // CH- butonu basıldı
}
```

## Tam Arduino Örneği

İşte IR kumanda butonlarını okuyarak LED kontrolü yapan eksiksiz bir örnek:

### Donanım Bağlantıları
- IR alıcı modülü → Pin 2
- LED → Pin 10 (dahili LED)

### Arduino Kodu

```c
#include <IRremote.hpp>
#include "kumanda_kodlari.h"

/*
IR Kumanda ile LED Kontrolü
Bu proje, IR kumandanın butonlarını okuyarak LED'i kontrol eder.
Header dosyasındaki sabit değerleri kullanır.
*/

// Pin tanımlamaları
const int IR_RECV_PIN = 2;
const int LED_PIN = 10;

// LED durumu
bool ledDurumu = false;

void setup() {
  /*
  Serial haberleşmeyi başlat ve IR alıcısını etkinleştir.
  LED pinini çıkış olarak ayarla.
  */
  Serial.begin(115200);
  IrReceiver.begin(IR_RECV_PIN, ENABLE_LED_FEEDBACK);
  pinMode(LED_PIN, OUTPUT);
  
  Serial.println("=== IR Kumanda LED Kontrolü ===");
  Serial.println("Buton işlevleri:");
  Serial.println("CH+ - LED'i aç");
  Serial.println("CH- - LED'i kapat");
  Serial.println("PLAY/PAUSE - LED'i aç/kapat");
  Serial.println(">> - LED'i yanıp söndür");
  Serial.println("==============================");
}

void loop() {
  /*
  IR sinyali geldiğinde butona göre farklı işlemler yap
  */
  if (IrReceiver.decode()) {
    // Gelen sinyal kodunu hex formatında al
    uint32_t butonKodu = IrReceiver.decodedIRData.decodedRawData;
    
    // Serial Monitor'e hangi butonun basıldığını yazdır
    Serial.print("Basılan buton kodu: 0x");
    Serial.println(butonKodu, HEX);
    
    // Buton kodlarına göre işlem yap
    switch(butonKodu) {
      case BUTTON_CH_PLUS: // CH+ butonu
        digitalWrite(LED_PIN, HIGH);
        ledDurumu = true;
        Serial.println("LED açıldı (CH+)");
        break;
        
      case BUTTON_CH_MINUS: // CH- butonu  
        digitalWrite(LED_PIN, LOW);
        ledDurumu = false;
        Serial.println("LED kapatıldı (CH-)");
        break;
        
      case BUTTON_PLAY_PAUSE: // PLAY/PAUSE butonu
        ledDurumu = !ledDurumu; // Durumu tersine çevir
        digitalWrite(LED_PIN, ledDurumu);
        Serial.print("LED durumu değiştirildi: ");
        Serial.println(ledDurumu ? "AÇIK" : "KAPALI");
        break;
        
      case BUTTON_NEXT: // >> butonu
        // LED'i yanıp söndür
        for(int i = 0; i < 3; i++) {
          digitalWrite(LED_PIN, HIGH);
          delay(200);
          digitalWrite(LED_PIN, LOW);
          delay(200);
        }
        Serial.println("LED yanıp söndü (>>)");
        break;
        
      default:
        Serial.println("Bu buton için işlem tanımlanmamış");
        break;
    }
    
    // Bir sonraki sinyali almaya hazırlan
    IrReceiver.resume();
    
    // Kısa bir gecikme ekle (buton bounce'unu önlemek için)
    delay(250);
  }
}
```

## Kullanım İpuçları

### Kütüphane Kurulumu
Arduino IDE'de `IRremote` kütüphanesini kurmak için:
1. **Araçlar** → **Kütüphane Yöneticisi**'ni açın
2. "IRremote" araması yapın
3. "IRremote by shirriff" kütüphanesini kurun

### Buton Kodlarını Öğrenme
Eğer farklı bir kumanda kullanıyorsanız, buton kodlarını öğrenmek için:

```c
// Sadece buton kodlarını görmek için basit kod
void loop() {
  if (IrReceiver.decode()) {
    Serial.print("Buton kodu: 0x");
    Serial.println(IrReceiver.decodedIRData.decodedRawData, HEX);
    IrReceiver.resume();
  }
}
```

### Sorun Giderme
- **Sinyal gelmiyor**: IR alıcının doğru pine bağlandığını kontrol edin
- **Yanlış kodlar**: Serial Monitor'ün 115200 baud'a ayarlı olduğundan emin olun
- **Tekrar eden sinyaller**: `delay()` ekleyerek buton bounce'unu engelleyin

## Header Dosyası (kumanda_kodlari.h)

Aşağıdaki header dosyasını projelerinizde kullanabilirsiniz:

```c
/*
 * IR Kumanda Kodları - Arduino Header Dosyası
 * Kit ile birlikte gelen IR kumandasının buton kodları
 */

#ifndef KUMANDA_KODLARI_H
#define KUMANDA_KODLARI_H

// Kanal kontrol butonları
#define BUTTON_CH_MINUS    0x45  // CH-
#define BUTTON_CH          0x46  // CH  
#define BUTTON_CH_PLUS     0x47  // CH+

// Medya kontrol butonları
#define BUTTON_PREV        0x44  // <<
#define BUTTON_NEXT        0x40  // >>
#define BUTTON_PLAY_PAUSE  0x43  // PLAY/PAUSE

// Ses kontrol butonları
#define BUTTON_VOL_MINUS   0x07  // -
#define BUTTON_VOL_PLUS    0x15  // +
#define BUTTON_EQ          0x09  // EQ

// Sayı butonları
#define BUTTON_0           0x16
#define BUTTON_1           0x0C
#define BUTTON_2           0x18
#define BUTTON_3           0x5E
#define BUTTON_4           0x08
#define BUTTON_5           0x1C
#define BUTTON_6           0x5A
#define BUTTON_7           0x42
#define BUTTON_8           0x52
#define BUTTON_9           0x4A

// Özel butonlar
#define BUTTON_100_PLUS    0x19  // 100+
#define BUTTON_200_PLUS    0x0D  // 200+

#endif // KUMANDA_KODLARI_H
```

Bu header dosyasını Arduino sketch'inizin yanına koyarak `#include "kumanda_kodlari.h"` ile dahil edebilirsiniz.
