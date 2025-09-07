# Proje 07 - Araba Simulasyonu

## Giriş

Bu projede basit bir araba simulasyonu yapacak ve servo motorun nasıl kontrol edildiğini ve dizileri kullanmayı (array) öğreneceksin.

## Yeni Kavramlar

### Servo Kütüphanesi ve nesneler
**Servo kütüphanesi**, servo motorları kolayca kontrol etmemizi sağlar. `#include <Servo.h>` ile dahil edilir ve `Servo rpmOlcer;` şeklinde bir nesne oluşturulur. 

Nesneler, kendi içlerinde kendilerine dair değişkenler ve fonksiyonlar barındırırlar. 

Bu örnek kodda rpmOlcer isminde, Servo tipindeki nesnede yer alan **attach** ve **write** fonksiyonlarını kullanıyoruz. Bir nesneye ait fonksiyon veya değişkenlere erişmek için "." işareti kullanılır.

### Fonksiyon Organizasyonu
Karmaşık işlemleri **ayrı fonksiyonlara** bölerek kodun daha okunabilir ve bakımı kolay olmasını sağlıyoruz. `arabaSimulasyonuGuncelle()`, `vitesKontrol()`, `motorSesiGuncelle()` gibi özel fonksiyonlar tanımlayarak her birinin kendine özgü görevi olmasını sağlıyoruz.

```cpp
void arabaSimulasyonuGuncelle() {
  ...
}
```

Fonksiyonlar bu şekilde tanımlanıyor ve aşağıda belirtildipi gibi çağırılıyor:

```cpp
arabaSimulasyonuGuncelle();
```

Bir fonksiyonu çağırdığınızda, program akışı o fonksiyonun içindeki koddan devam ediyor. Fonksiyonun içindeki kod bittikten sonra ise program akışı kaldığı yerden devam eder.

### map() Fonksiyonu
**map()** fonksiyonu, bir aralıktaki değeri başka bir aralığa çevirir. `map(rpm, 800, 6000, 180, 0)` komutu RPM değerini (800-6000 arası) servo açısına (180-0 derece arası) dönüştürür. Matematikte öğrendiğimiz doğru orantı ve ters orantı yani. 800 rpm 180 derece ve 6000 rpm 0 dereceyse, verdiğim rpm değeri hangi dereceye karşılık gelir sorusunun cevabının verir. 

### constrain() Fonksiyonu
**constrain()** fonksiyonu, bir değeri belirtilen aralıkta tutar. 
Değer verilen maksimumdan büyükse, maksimum değeri; verilen minimumdan küçükse, minimum değeri; diğer durumlarda ise değişime uğramaksızın verilen değeri döndürür.

`constrain(rpm, rpmMin[vites], rpmMax[vites])` komutu RPM'in vites sınırlarını aşmasını önler. Simule ettiğimiz araba motorunun hızı 0'dan düşük, veya o vites için belirlenmiş maksimum hızdan daha büyük olamaz.

### Boolean Değişkenler
**Boolean değişkenler** doğru/yanlış (true/false) değerlerinde birine sahip olabilirler. Birşeyin açık mı kapalı mı olduğunu tutmak için mesela boolean türünde bir değişken kullanabilirsin.

### Değil (!) Operatörü
`!` veya Değil (NOT) operatörü boolean değeri tersine çevirir. Yani doğruysa yanlış, yanlışşa doğru değer döndürür. 

```cpp
  bool butonBasili = !digitalRead(BUTON_PIN);
```

Kodda bunu butonu okurken kullanıyoruz. Çünkü butona basılı olup olmadığı bilgisini saklamak istiyoruz bir değişkende. Ama buton devremiz bir yukarı çeken (pull-up) dirençle bağlı olduğu için, butona basıldığı durumda digitalRead() fonksiyonu yanlış değer döndürür, basılı olmadığında ise doğru değer döndürür. Oysa biz bunun tam tersini, yani butona basılıysa doğru, basılı değilse yanlış olan bir değer istiyoruz. 


## Elektronik

<img src="../images/servo.svg" alt="Servo devresi" style="height: 400px;">

Bu projede servo motor kullanarak gerçekçi bir araba simülasyonu yapacağız. Servo motor, belirli açılarda konum alabilen özel bir motor türüdür - tıpkı araba gösterge panelindeki RPM ölçer ibresi gibi!

### Servo Motor Nasıl Çalışır?

Servo motor, içinde üç önemli parça bulunan akıllı bir motor sistemidir:

1. **DC Motor** - Gerçek hareketi sağlayan küçük elektrik motoru
2. **Potansiyometre** - Motorun şu anki pozisyonunu ölçen sensör (Proje 04'ten tanıdığın gibi!)
3. **Kontrol Devresi** - Hedef pozisyon ile gerçek pozisyonu karşılaştırıp motoru doğru yöne çeviren akıllı devre

Servo motor, Arduino'dan gelen PWM (Pulse Width Modulation - Darbe Genişlik Modülasyonu) sinyaliyle kontrol edilir. Bu sinyal, elektrik darbesinin ne kadar süre HIGH kalacağını belirler:

- **1.5ms darbe** = 90° (orta konum)
- **1ms darbe** = 0° (sol uç)  
- **2ms darbe** = 180° (sağ uç)

Bu darbeler her 20ms'de bir (50Hz frekansta) tekrarlanır. Arduino'nun `Servo` kütüphanesi bu karmaşık timing hesaplarını bizim için halleder - sen sadece istediğin açıyı (0-180°) söylemen yeterli!

!!! info "Servoyu elle çevirmemeye dikkat et. Bu dişlilerin zarar görmesine neden olabilir!"

### Devre Açıklaması

Servo motor kit üzerinde Arduino'nun 9 numaralı PWM pinine bağlı. Bu pin, servo motorun hassas konum kontrolü için gerekli PWM sinyallerini üretebilen özel pinlerden biri.

Devrede ayrıca:
- **Buton** (Pin 7) - Gazı temsil ediyor. Proje 02'den tanıdığın pull-up direncli buton yapısı
- **Buzzer** (Pin 3) - Motor sesini simüle ediyor. Proje 05'ten tanıdığın PWM kontrollü buzzer
- **Güç filtreleme kondansatörleri** - Servo motorun hareket ederken çektiği ani akım değişimlerini düzenler

### Araba Simülasyonu Konsepti

Bu projede servo motor, araba gösterge panelindeki RPM (Revolutions Per Minute - Dakikadaki Devir Sayısı) ölçerini simüle ediyor. Gerçek arabalar gibi:

- **Gaz pedalı** = Buton (basılı = gaz ver, bırakılmış = gaz bırak)
- **Otomatik vites** = Kod RPM'e göre otomatik vites değiştirir
- **Motor sesi** = Buzzer RPM'e orantılı frekansla ses çıkarır
- **RPM göstergesi** = Servo motor RPM'i görsel olarak gösterir

Bu karmaşık simülasyon, diziler (arrays), zamanlama fonksiyonları ve çoklu fonksiyon organizasyonu gibi ileri programlama tekniklerini öğrenmeni sağlayacak.

## Kod

Şimdi Arduino IDE'yi açıp aşağıdaki kodu yazabilirsin:

```c
/*
Servo kütüphanesi, servo motorları kolayca kontrol etmemizi sağlar.
Bu kütüphane Arduino IDE ile birlikte gelir, ayrıca kurulum gerektirmez.
Servo motorlar, 0-180 derece arasında hassas konum kontrolü yapabilen
akıllı motorlardır.
*/
#include <Servo.h>

/*
Pin tanımlarımızı const int ile yapıyoruz. const, bu değerlerin
program boyunca değişmeyeceğini belirtir. 
*/
const int BUTON_PIN = 7;    // Gaz pedalı butonu (Proje 02'den tanıdık)
const int BUZZER_PIN = 3;   // Motor sesi için buzzer (Proje 05'ten tanıdık)
const int SERVO_PIN = 9;    // RPM göstergesi servo motoru

/*
Servo sınıfından bir nesne oluşturuyoruz. OOP (Object Oriented Programming)
konseptinde bu, servo motorumuzu temsil eden bir "nesne" dir.
rpmOlcer ismi, bu servo'nun RPM ölçer görevi yapacağını belirtir.
*/
Servo rpmOlcer;

/*
Araba simülasyonumuzun ana değişkenleri:
- vites: Şu anki vites pozisyonu (0=1.vites, 1=2.vites, 2=3.vites)
- rpm: Şu anki motor devir hızı (Revolutions Per Minute)
*/
int vites = 0;              // Başlangıçta 1. viteste
int rpm = 800;              // Rölanti RPM (motor boştayken devir sayısı)


// Her vites için minimum RPM değerleri
const int rpmMin[3] = {
  800,   // 1. Vites - En düşük devir (rölanti)
  1500,  // 2. Vites - Biraz daha yüksek minimum
  2500   // 3. Vites - En yüksek minimum devir
};

// Her vites için maximum RPM değerleri  
const int rpmMax[3] = {
  6000,  // 1. Vites - Redline (kırmızı çizgi) bölgesi
  4500,  // 2. Vites - Daha erken vites değişimi
  5000   // 3. Vites - Son vites, uzun menzil
};

/*
İvme dizileri - Gerçekçi araba davranışı için!
Gerçek arabalarda her vites farklı hızlanma ve yavaşlama karakteristiği gösterir.
1. vites: Güçlü hızlanma, güçlü fren etkisi
3. vites: Yavaş hızlanma, zayıf fren etkisi
*/

// Her vites için hızlanma ivmesi (gaz verildiğinde RPM artışı)
const int hizlanmaIvme[3] = {
  500,   // 1. Vites - Güçlü torque, hızlı ivme
  300,   // 2. Vites - Orta seviye ivme  
  150    // 3. Vites - Yumuşak ivme, hız için optimize
};

// Her vites için yavaşlama ivmesi (gaz bırakıldığında RPM azalması)
const int yavaslamaIvme[3] = {
  700,   // 1. Vites - Güçlü motor freni etkisi
  400,   // 2. Vites - Orta seviye yavaşlama
  200    // 3. Vites - Yavaş yavaşlama, uzun menzil
};

/*
ZAMANLAMA DEĞİŞKENLERİ - millis() Kullanımı

delay() fonksiyonu tüm programı durdurur, bu araba simülasyonu gibi
sürekli kontrol gerektiren uygulamalar için uygun değil. Bunun yerine
millis() fonksiyonunu kullanarak "non-blocking timing" yapıyoruz.

millis() Arduino'nun açıldığından beri geçen milisaniye sayısını döndürür.
unsigned long tipini kullanıyoruz çünkü bu sayı çok büyük olabilir.
*/
unsigned long sonGuncelleme = 0;           // Son güncelleme zamanı
const int GUNCELLEME_ARALIGI = 100;        // Her 100ms'de güncelle (10Hz)

void setup() { 
  /*
  Pin ayarlarımızı yapıyoruz. BUTON_PIN için pinMode ayarı yapmadık
  çünkü Arduino'da dijital pinler varsayılan olarak INPUT modunda.
  Pull-up direnci devrede mevcut olduğu için ek ayar gerekmiyor.
  */
  pinMode(BUZZER_PIN, OUTPUT);    // Buzzer çıkış pini
  
  /*
  attach() fonksiyonu servo motoru belirtilen pine bağlar.
  Bu fonksiyon çağrıldıktan sonra servo motor PWM sinyalleri almaya hazır.
  Arduino otomatik olarak 50Hz frekansta (her 20ms) servo kontrol sinyali gönderir.
  */
  rpmOlcer.attach(SERVO_PIN);
  
  /*
  Servo'yu başlangıç pozisyonuna getir. map() fonksiyonu ile
  RPM değerini (800) servo açısına (0-180°) çeviriyoruz.
  */
  int baslamaAcisi = map(rpm, 800, 6000, 180, 0);
  rpmOlcer.write(baslamaAcisi);
}

void loop() {

    arabaSimulasyonuGuncelle();   // RPM ve vites hesaplamalarını yap
    motorSesiGuncelle();          // Buzzer frekansını güncelle

    delay(GUNCELLEME_ARALIGI);  // 100ms bekle
  
  /*
  loop() fonksiyonunun sonuna geldi, Arduino otomatik olarak
  tekrar başa dönecek. 
  */
}

/*
ANA SİMÜLASYON FONKSİYONU
Bu fonksiyon araba simülasyonunun kalbini oluşturur.
Gaz pedalı, vites değişimi ve RPM göstergesi kontrolünü yapar.
*/
void arabaSimulasyonuGuncelle() {
  /*
  Buton okuma - Pull-up direnci mantığı
  
  Pull-up dirençli butonda:
  - Buton basılı DEĞİL = digitalRead() HIGH döndürür  
  - Buton basılı = digitalRead() LOW döndürür
  
  ! operatörü boolean değeri tersine çevirir:
  - !HIGH = false (basılı değil)
  - !LOW = true (basılı)
  */
  bool butonBasili = !digitalRead(BUTON_PIN);

  /*
  RPM HESAPLAMA 
  
  Gerçek arabalarda gaz pedalına basıldığında motor hemen maksimum
  devire çıkmaz, kademeli olarak artar. Aynı şekilde gaz bırakıldığında
  da kademeli olarak düşer. Bu davranışı ivme dizileri ile simüle ediyoruz.
  */
  if (butonBasili) {
    // Gaz basılı: RPM'i hızlanma ivmesi ile artır
    rpm += hizlanmaIvme[vites];  // Dizi erişimi: vites indeksindeki değeri al
  } else {
    // Gaz bırakılmış: RPM'i yavaşlama ivmesi ile azalt  
    rpm -= yavaslamaIvme[vites]; // Motor freni etkisi
  }
  
  /*
  constrain() FONKSİYONU - Değer Sınırlama
  
  constrain(değer, minimum, maksimum) fonksiyonu bir değeri
  belirtilen aralıkta tutar. RPM'in vites sınırlarını aşmasını önler.
  
  Örnek: vites=0 iken rpm, rpmMin[0]=800 ile rpmMax[0]=6000 arasında kalır
  */
  rpm = constrain(rpm, rpmMin[vites], rpmMax[vites]);
  
  // Otomatik vites değişimi kontrolü
  vitesKontrol();

  /*
  SERVO MOTOR KONTROLÜ - RPM'den Açıya Çevirme
  
  map() fonksiyonu bir aralıktaki değeri başka bir aralığa çevirir:
  - Kaynak aralık: 800-6000 RPM
  - Hedef aralık: 180-0 derece (servo açısı)
  
  180-0 sıralaması önemli! RPM arttıkça servo açısı azalmalı
  çünkü gösterge ibresi saat yönünde hareket eder.
  */
  int servoAcisi = map(rpm, 800, 6000, 180, 0);
  
  /*
  write() fonksiyonu servo motoru belirtilen açıya hareket ettirir.
  Servo kendi kontrolörü sayesinde bu pozisyona hassas şekilde gider
  ve orada sabit kalır. 
  */
  rpmOlcer.write(servoAcisi);
}

/*
OTOMATİK VİTES SİSTEMİ
Gerçek arabalardaki otomatik vites mantığını simüle eder.
*/
void vitesKontrol() {
  /*
  VİTES YÜKSELTME 
  RPM maksimum sınıra ulaştığında bir üst vitese geç.
  Bu gerçek arabalarda "redline" bölgesinde yapılan işlemdir.
  */
  if (rpm >= rpmMax[vites]) {
    // Henüz en üst viteste değilsek (vites < 2, yani 3.viteste değilsek)
    if (vites < 2) {
      vites++;                    // Bir vites yükselt
      rpm = rpmMin[vites];        // Yeni vites için minimum RPM'e ayarla
      
      /*
      Gerçek arabalarda vites değiştiğinde RPM düşer çünkü
      dişli oranı değişir. Aynı motor gücü, farklı çıkış hızı.
      */
    }
  } 
  /*
  VİTES DÜŞÜRME   
  RPM minimum sınıra düştüğünde bir alt vitese geç.
  Motor güç kaybını önlemek için gerekli.
  */
  else if (rpm <= rpmMin[vites]) {
    // Henüz en alt viteste değilsek (vites > 0, yani 1.viteste değilsek)
    if (vites > 0) {
      vites--;                    // Bir vites düşür
      rpm = rpmMax[vites];        // Yeni vites için maximum RPM'e ayarla
      
      /*
      Vites düştüğünde RPM yükselir çünkü aynı tekerlek hızı
      için motor daha hızlı dönmek zorunda kalır.
      */
    }
  }
}

/*
MOTOR SESİ 
RPM değerine göre motor sesi üretir.
*/
void motorSesiGuncelle() {
  /*
  FREKANS HESAPLAMA - Motor Sesi
  
  Gerçek motor sesleri RPM ile doğru orantılıdır.
  Devir sayısı arttıkça ses frekansı da artar.
  
  - 800 RPM = 32 Hz (pes ses)
  - 6000 RPM = 240 Hz (tiz ses)
  */
  int frekans = rpm / 25;
  
  /*
  tone() fonksiyonu PWM kullanarak buzzer'dan belirtilen
  frekansta ses çıkarır. Bu ses sürekli devam eder,
  başka bir tone() çağrısı veya noTone() çağrısı gelene kadar.
  */
  tone(BUZZER_PIN, frekans);
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında butona basarak gazı kontrol edebileceksin. Butona bastığında RPM artacak ve servo motor ibresini hareket ettirecek, buzzer'dan da motor sesi gelecek. RPM yeterince yüksek olduğunda otomatik olarak vites değişecek ve farklı hızlanma karakteristiği göreceksin.

--8<-- "snippets/sorun-giderme.md"

## Egzersizler

Böylece servo motor kontrolünü, boolean değerleri ve fonksiyonları öğrenmiş oldun. Bu projede öğrendiklerinle aşağıdakileri yapabilir misin?

1. **Servo Motor Kalibrasyonu**: Servo motorun hareket aralığını değiştirerek, ibrenin 180 dereceden daha dar bir aralıkta çalışmasını sağlayabilir misin? [^1]

2. **Seri Monitor Gösterge**: Araba simülasyonunu çalışırken Seri Monitor'da anlık vites ve RPM değerlerini gösterebilir misin? [^2]

3. **Maksimum RPM Alarm**: RPM 5000'i geçtiğinde buzzer'dan farklı bir alarm sesi çıkaracak şekilde kodu değiştirebilir misin? [^3]

4. **Yakıt Ekonomisi Modu**: Potansiyometre kullanarak "Eco Mode" yapabilir misin? Potansiyometre değeri düşükken ekonomik mod aktif olsun ve hızlanma ivmeleri yarıya düşsün. [^4]

[^1]: 
    `map()` fonksiyonundaki son iki parametre servo'nun hareket aralığını belirler. 180-0 yerine daha dar bir aralık kullanırsan ne olur?

[^2]:
    Proje 03'te seri iletişimi öğrenmiştin. `setup()` ve `arabaSimulasyonuGuncelle()` fonksiyonlarına hangi komutları eklermen gerekir? Vites numarasını gösterirken +1 eklemeyi unutma!

[^3]:
    Motor sesinin çıktığı fonksiyonda RPM değerini kontrol edebilirsin. Yüksek RPM'de farklı bir frekans kullanmayı dene.

[^4]:
    Proje 04'teki potansiyometre okuma yöntemini hatırlıyor musun? Okunan değeri kullanarak hızlanma dizilerindeki değerleri nasıl değiştirebilirsin?
