# Proje 06 - Mesafe Sensörü (Teremin)

## Giriş

Bu projede mesafe sensörü kullanarak müzik yapmayı öğreneceksin. Elektronik bir "teremin" yapacağız - bu, elini alçına yaklaştırıp uzaklaştırarak farklı notalar çıkarabileceğin özel bir müzik aleti!

Teremin, 1920'lerde Rus mucit Lev Sergeyeviç Termen tarafından icat edilmiş, dokunmadan çalınan ilk elektronik müzik aleti. Bizim versiyonumuz ultrasonic (sesötesi) mesafe sensörü kullanarak elinin sensöre olan uzaklığını ölçüyor ve bu mesafeyi müzik notalarına dönüştürüyor. Elin sensöre ne kadar yakınsa ses o kadar tiz, uzaksa o kadar pes oluyor.

Bu projede HC-SR04 ultrasonic sensörünü ve önceki projelerden tanıdığın buzzer'ı kullanacağız. Ayrıca Arduino'nun `map()` fonksiyonunu ve mesafe ölçümünün nasıl çalıştığını öğreneceksin.

## Elektronik

HC-SR04 ultrasonic sensörü mesafe ölçmek için sesötesi (ultrasonic) dalgalar kullanır - tıpkı yarasaların geceleyin yön bulmak için kullandığı sistem gibi!

Sensör şu şekilde çalışır:

1. **Trigger (Tetik) pini** - Arduino'nun 6 numaralı pininden gelen sinyal ile sensöre "mesafe ölç" komutu verilir

2. **Echo (Yankı) pini** - Arduino'nun 5 numaralı pininden sensörün ölçüm sonucunu okur

3. Sensör, trigger sinyali aldığında 40kHz frekansta 8 adet ses dalgası 
gönderir 

4. Bu dalgalar önündeki nesneden yansıyıp geri döner

5. Echo pini, dalganın gidip gelme süresini Arduino'ya bildirir

6. Arduino bu süreyi mesafeye çevirir (ses hızı: yaklaşık 343 m/s)

Devre üzerinde buzzer'ın 3 numaralı pine bağlı olduğunu görüyorsun. Bu pin, PWM (Pulse Width Modulation) destekleyen pinlerden biri ve `tone()` fonksiyonu ile farklı frekanslarda ses üretebiliyor.

Mesafe sensörünün menzili yaklaşık 2cm ile 400cm arasında. Projemizde bu aralığı 2-32cm olarak sınırladık çünkü müzik yapmak için çok uzun mesafeler pratik değil.

## Kod

Şimdi Arduino IDE'yi açıp aşağıdaki kodu yazabilirsin:

```c
/*
HCSR04 kütüphanesi, HC-SR04 ultrasonic mesafe sensörünü kolayca 
kullanabilmemizi sağlar. Bu kütüphane trigger ve echo pinlerini 
yönetir, mesafe hesaplamasını bizim için yapar.
*/
#include <HCSR04.h>

/*
Mesafe sensörünün pin bağlantılarını tanımlayalım.
triggerPin: Arduino'dan sensöre "mesafe ölç" komutu gönderen pin
echoPin: Sensörden Arduino'ya ölçüm sonucunu gönderen pin
*/
const byte triggerPin = 6;
const byte echoPin = 5;

/*
UltraSonicDistanceSensor nesnesi oluşturuyoruz. Bu nesne, mesafe 
ölçümü için gerekli tüm işlemleri bizim için halleder. 
mesafeOlcer ismini verdik.
*/
UltraSonicDistanceSensor mesafeOlcer(triggerPin, echoPin);

// Buzzer'ın bağlı olduğu pin (önceki projelerden tanıdık)
int buzzerPin = 3;

/*
Teremin'in çalışma aralığını belirleyen değerler:
- min_mesafe: En yakın mesafe (2cm) - bundan yakın olursa en tiz ses
- max_mesafe: En uzak mesafe (32cm) - bundan uzak olursa ses kesilir
Bu aralık müzik yapmak için optimize edilmiş, çok pratik bir alan sağlıyor.
*/
const int min_mesafe = 2;
const int max_mesafe = 32;

/*
Frekans aralığımızı da belirleyelim:
- min_frekans: En pes ses (262 Hz = DO_3 notası)
- max_frekans: En tiz ses (1048 Hz = DO_5 notası)
Bu aralık tam 2 oktav kapsıyor, güzel bir müzik aralığı.
*/
const int min_frekans = 262;
const int max_frekans = 1048;

/*
Müzik notalarının frekanslarını tanımlayalım. Bu değerler fiziksel olarak
belirlenen standart nota frekansları. Örneğin LA_3 = 440 Hz, bu müzikte
standart akort notası olarak kullanılır.
*/

// 3. Oktav (pes sesler)
const int DO_3 = 262;   // C3
const int RE_3 = 294;   // D3
const int MI_3 = 330;   // E3
const int FA_3 = 349;   // F3
const int SOL_3 = 392;  // G3
const int LA_3 = 440;   // A3 (akort notası)
const int SI_3 = 494;   // B3

// 4. Oktav (orta sesler)
const int DO_4 = 523;   // C4 (orta do)
const int RE_4 = 587;   // D4
const int MI_4 = 659;   // E4
const int FA_4 = 698;   // F4
const int SOL_4 = 784;  // G4
const int LA_4 = 880;   // A4
const int SI_4 = 987;   // B4

// 5. Oktav başlangıcı (tiz ses)
const int DO_5 = 1048;  // C5

/*
notalar[] dizisi, tüm notalarımızı sırayla tutuyor. Bu dizideki sıralama,
pestten tize doğru gidiyor. Mesafe sensöründen okunan değer bu dizide
hangi notanın çalınacağını belirleyecek.
*/
int notalar[] = { DO_3, RE_3, MI_3, FA_3, 
  SOL_3, LA_3, SI_3, DO_4, 
  RE_4, MI_4, FA_4, SOL_4, 
  LA_4, SI_4, DO_5 };

/*
nota_adedi değişkeni, dizimizde kaç nota olduğunu tutuyor. Bu değeri
map() fonksiyonunda kullanacağız. 15 nota var ama diziler 0'dan başladığı
için son indeks 14 olacak.
*/
int nota_adedi = 15;

void setup() {
  /*
  Bu projede setup() fonksiyonunda özel bir ayar yapmamıza gerek yok.
  HCSR04 kütüphanesi kendi pin ayarlarını hallediyor, buzzer pini için
  de pinMode ayarı tone() fonksiyonu tarafından otomatik yapılıyor.
  */
}

void loop() {
  /*
  measureDistanceCm() fonksiyonu mesafe ölçümü yapıyor ve sonucu
  santimetre cinsinden float (ondalıklı sayı) olarak döndürüyor.
  */
  float mesafe = mesafeOlcer.measureDistanceCm();
  
  /*
  round() fonksiyonu ondalıklı sayıyı en yakın tam sayıya yuvarlar.
  Müzik için bu hassasiyet yeterli, ayrıca daha kararlı ses elde ederiz.
  */
  int mesafeMm = round(mesafe);
  
  /*
  Eğer ölçülen mesafe maximum aralığımızdan büyükse, teremin'i sustur.
  return komutu loop() fonksiyonunu hemen bitirir ve yeniden başlatır.
  */
  if (mesafeMm > max_mesafe) {
    noTone(buzzerPin);  // Buzzer'ı sustur
    delay(10);          // Kısa bekle
    return;             // Bu döngüyü bitir, yeniden başla
  }
  
  /*
  map() fonksiyonu çok önemli! Bir aralıktaki değeri başka bir aralığa
  orantılı olarak dönüştürür. Burada:
  - mesafeMm: Ölçtüğümüz mesafe (min_mesafe ile max_mesafe arasında)
  - 0, nota_adedi-1: Hedef aralık (dizimizin indeks aralığı: 0-14)
  
  Örnek: mesafe 17cm ise, bu 2-32 aralığının ortasında bir değer.
  map() bunu 0-14 aralığının ortasına çevirir, yani yaklaşık 7. indeks.
  7. indeks = DO_4 notası
  */
  int indeks = map(mesafeMm, min_mesafe, max_mesafe, 0, nota_adedi-1);
  
  /*
  tone() fonksiyonu ile buzzer'dan belirlenen frekansta ses çıkarır.
  notalar[indeks] ile diziden mesafeye karşılık gelen notayı seçer.
  3. parametre vermediğimiz için ses sürekli çalar (başka bir tone() 
  çağrısı veya noTone() çağrısı gelene kadar).
  */
  tone(buzzerPin, notalar[indeks]);
  
  /*
  10ms bekleriz. Bu, sensörün aşırı hızlı okunmasını önler ve
  daha kararlı ses elde etmemizi sağlar. Çok kısa bir süre olduğu için
  müzik çalarken hiç gecikme hissetmezsin.
  */
  delay(10);
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında elini mesafe sensörünün önüne yaklaştırıp uzaklaştırdığında farklı notalar duyacaksın. Elin sensöre ne kadar yakınsa ses o kadar tiz, uzaklaştırdıkça daha pes sesler çıkacak. 2cm'den yakın veya 32cm'den uzak mesafelerde ses kesilecek.

--8<-- "snippets/sorun-giderme.md"

## Egzersizler

Böylece mesafe sensörüyle müzik yapmayı ve `map()` fonksiyonunu kullanmayı öğrenmiş oldun. Bu projede öğrendiklerinle aşağıdakileri yapabilir misin?

1. Teremin'in mesafe aralığını genişletebilir misin? Örneğin 2-50cm yaparsak ne olur? [^1]
2. LED'i de ekleyerek, mesafeye göre hem ses hem de LED parlaklığı değişen bir sistem yapabilir misin? [^2]
3. Sadece belirli notalarda (örneğin sadece DO-RE-MI-FA-SOL) çalan bir pentatonik teremin yapabilir misin? [^3]

[^1]: 
    `max_mesafe` değerini 50 yaparak deneyebilirsin. Daha geniş alan daha fazla kontrol imkanı sağlar ama uzun mesafelerde sensör bazen kararsız ölçümler yapabilir.

[^2]:
    `analogWrite()` fonksiyonunu kullanarak LED'in parlaklığını kontrol edebilirsin. Mesafeyi 0-255 aralığına map'leyip LED'e uygula. LED'i 10. pine bağlamayı unutma!

[^3]:
    Yeni bir dizi oluştur: `int pentatonik[] = { DO_3, RE_3, MI_3, SOL_3, LA_3, DO_4, RE_4, MI_4, SOL_4, LA_4 };` Bu dizinin boyutunu 10 yapman gerekecek.

