# Proje 04 - Potansiyometre

## Giriş

Bu projede potansiyometreyi kullanarak nasıl analog değerleri okuyabileceğini öğreneceksin. Analog değerler, dijital değerlerin aksine sadece 0 ve 1 değil, bu ikisi arasındaki tüm değerleri alabilir. Potansiyometre ile Arduino'nun analog girişlerini kullanmayı ve değişken gerilim değerlerini ölçmeyi öğreneceksin.

!!! note "Analog da ne ola ki?"
      Elektronikte iki çeşit sinyal tipi vardır, dijital ve analog. Dijital sinyaller herhangi bir anda sadece iki değerden birine sahip olabilir. Bu değerler Yüksek/Alçak (HIGH/LOW), Açık/Kapalı (ON/OFF) veya 0/1 diye bilinir. Analog sinyallerse bunun aksine herhangi bir anda belirli bir aralıktaki her gerilim seviyesinde bulunabilir. 

## Elektronik

Bir potansiyometre, üzerinde kayar bir kontak olan bir dirençtir. Bu kontak direnci ayarlanabilir bir yerden böler. Kit'in üzerinde bulunan gibi ileri, geri hareket eden bir kontağı olan potansiyometrelere şerit potansiyometre denir. Döndürerek ayarlanabilen Döner Potansiyometreler de çok yaygındır. 

Potansiyometrenin 3 bacağı bulunur. Direncin iki ucunda iki bacak ve ortada da kayar kontağin bağlı olduğu bacak. 100KΩ'luk bir potansiyometren var diyelim. Bu potansiyometreyi en kısık olduğu konuma ayarlarsan, kayar kontağa yakın olan bacakla kayar kontak arasındaki direnç 0'a yakın, diğer bacakla kayar kontak arasında direnç ise 100KΩ'a yakın olur. Potansiyometre'yi ortaya ayarlarsan, her iki tarafın direnci de eşit ve 50KΩ olur. 

<img src="../images/potansiyometre.svg" alt="Potansiyometre devresi">

Genellikle potansiyometreler, aynı bu şemadaki gibi, uç bacaklarından birine besleme gerilimi, birine de şase bağlanarak kullanılır. Bu şekilde kayar kontakta 0 ile besleme gerilimi arasında ayarlanabilir bir gerilim elde edilir. 

Şemadaki C ile işaretli diğer iki eleman ise kondansatör. Bunlar kayar kontağın sürtünmesi gibi mekanik gürültüleri filtreleyerek Arduino'nun okuduğu değerlerin daha kararlı olmasını sağlar. Şimdilik ona çok kafa yormaya gerek yok.

## Kod

``` c
/*
Potansiyometre kitte A0 pinine bağlı. Arduino'da analog pinler A0, A1, A2... 
şeklinde isimlendirilir. Analog pinler, dijital pinlerden farklı olarak 
sadece HIGH/LOW değil, 0 ile 1023 arasında değişen sayısal değerler okuyabilir.
*/
int potPin = A0; 

void setup() {
  /*
  Seri iletişimi başlatıyoruz. Bu sefer daha hızlı bir baud rate kullanıyoruz.
  115200, saniyede 115200 bit veri gönderebileceğimiz anlamına gelir.
  */
  Serial.begin(115200);
}

void loop() {
  /*
  analogRead() fonksiyonu, Arduino'nun analog pinlerinden birindeki gerilim 
  değerini okur ve bunu 0-1023 arasında bir sayıya çevirir.
  
  0 değeri = 0V (şase gerilimi)
  1023 değeri = 5V (besleme gerilimi)
  512 değeri = yaklaşık 2.5V (ortada bir değer)
  
  Bu değer potansiyometrenin konumuna göre değişir.
  */
  int potDegeri = analogRead(potPin);
  
  /*
  Okuduğumuz ham değeri (0-1023) gerçek gerilim değerine (0-5V) çevirebiliriz.
  Bu matematiksel işlem, elektronik projelerinde çok kullanılır.
  */
  float gerilim = potDegeri * (5.0 / 1023.0);
  
  Serial.print("Ham değer: ");
  Serial.print(potDegeri);
  Serial.print(" - Gerilim: ");
  Serial.print(gerilim);
  Serial.println("V");
  
  // Çok hızlı değişmesin diye biraz bekleyelim
  delay(100);
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında Seri Monitor'da potansiyometrenin konumuna göre değişen değerleri göreceksin. Potansiyometreyi hareket ettirdikçe hem ham değer (0-1023) hem de gerçek gerilim değeri (0-5V) değişecek.

--8<-- "snippets/sorun-giderme.md"

## Egzersizler

Böylece analogRead() fonksiyonunu kullanarak analog değerleri okumayı öğrenmiş oldun. Bu projede öğrendiklerinle aşağıdakileri yapabilir misin?

1. Potansiyometre değerine göre LED'in yanıp sönme hızını kontrol edebilir misin? [^1]
2. Potansiyometre tam ortada olduğunda LED'in yanmasını sağlayabilir misin? [^2]
3. Okuduğun analog değeri 0-100 arasında yüzde değerine çevirebilir misin? [^3]

[^1]: 
    analogRead() değerini delay() fonksiyonunda kullanman gerekecek. Değeri uygun bir aralığa getirmek için bölmeyi unutma!

[^2]:
    512 değeri orta konumu temsil eder. Bu değere yakın olup olmadığını kontrol etmen gerekecek.

[^3]:
    map() fonksiyonunu araştır, veya basit matematik kullan: (potDegeri * 100) / 1023