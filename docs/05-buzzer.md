# Proje 05 - Buzzer

Bu projede Arduino'nun nasıl ses çıkarabileceğini öğreneceksin. Tabii ki profesyonel bir müzik aleti kadar kaliteli olmayacak ama... internetin en ünlü şarkısını çalmaya yetecek kadar iyi! 🎵

**Yazılım Kavramı**: 
- `tone()` fonksiyonu
- Notaları frekans olarak tanımlama
- Diziler (arrays) kullanımı
- `for` döngüsü

``` c
// Nota frekanslarını constant olarak tanımlayalım
// 3. Oktav
const int DO_3 = 262;
const int RE_3 = 294;
const int MI_3 = 330;
const int FA_3 = 349;
const int SOL_3 = 392;
const int LA_3 = 440;
const int SI_3 = 494;

// 4. Oktav
const int DO_4 = 523;
const int RE_4 = 587;
const int MI_4 = 659;
const int FA_4 = 698;
const int SOL_4 = 784;
const int LA_4 = 880;
const int SI_4 = 987;

// Es (sessizlik)
const int ES = -1;

// Transpoze edilmiş melodi (A minor) - Orijinalden 5 yarıton aşağı
int melodi[] = {
  MI_3, MI_3, RE_3, RE_3,
  DO_4, DO_4, LA_3, MI_3, MI_3, RE_3, RE_3, LA_3, LA_3, SOL_3, FA_3, MI_3,
  SOL_3, SOL_3, SOL_3, SOL_3,
  SOL_3, LA_3, FA_3, MI_3, RE_3, RE_3, RE_3, LA_3, SOL_3,
  MI_3, MI_3, RE_3, RE_3,
  DO_4, DO_4, LA_3, MI_3, MI_3, RE_3, RE_3, RE_4, FA_3, SOL_3, FA_3, MI_3,
  SOL_3, SOL_3, SOL_3, SOL_3,
  SOL_3, LA_3, FA_3, MI_3, RE_3, ES, RE_3, LA_3, SOL_3, ES
};

// Süreler aynı kalıyor (değişmedi)
int sureler[] = {
  100, 100, 100, 100,
  300, 300, 600, 100, 100, 100, 100, 300, 300, 300, 100, 200,
  100, 100, 100, 100,
  300, 300, 300, 100, 200, 200, 200, 400, 800,
  100, 100, 100, 100,
  300, 300, 600, 100, 100, 100, 100, 300, 300, 300, 100, 200,
  100, 100, 100, 100,
  300, 300, 300, 100, 200, 200, 200, 400, 800, 400
};

int buzzerPin = 3;
int notaSayisi = 54; // Toplam nota sayısı

void setup() {
  // Buzzer için pin ayarı
}

void loop() {
  for (int i = 0; i < notaSayisi; i++) {
    if (melodi[i] > 0) {
      tone(buzzerPin, melodi[i], sureler[i]);
    } else {
      noTone(buzzerPin);
    }
    delay(sureler[i]);
    noTone(buzzerPin);
    delay(sureler[i] * 0.3);
  }
  delay(2000);
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında buzzer'dan çalan bir melodi duyacaksın. 

--8<-- "snippets/sorun-giderme.md"

Böylece buzzer'dan ses çıkartmayı ve for döngülerini kullanmayı öğrenmiş oldun. Bu projede öğrendiklerinle aşağıdakileri yapabilir misin?

1. Butonla beraber kullanarak butona basıldığında bu melodiyi çalan bir kapı zili yapabilir misin?
2. Potansiyometre ile melodimin çalma hızını kontrol edebilir misin?[^1]

[^1]: delay() değerlerini birşeyle çarpman gerekecek.


