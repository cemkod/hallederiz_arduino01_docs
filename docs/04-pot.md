# Proje 04 - Potansiyometre

Bu projede potansiyometreyi kullanarak nasıl analog değerleri okuyabileceğini ögreneceksin.

!!! note "Analog da ne ola ki?"
      Elektronikte iki çeşit sinyal tipi vardır, dijital ve analog. Dijital sinyaller herhangi bir anda sadece iki değerden birine sahip olabilir. Bu değerler Yüksek/Alçak (HIGH/LOW), Açık/Kapalı (ON/OFF) veya 0/1 diye bilinir. Analog sinyallerse bunun aksine herhangi bir anda belirli bir aralıktaki her gerilim seviyesinde bulunabilir. 

Bir potansiyometre, üzerinde kayar bir kontak olan bir dirençtir. Bu kontak direnci ayarlanabilir bir yerden böler. Kit'in üzerinde bulunan gibi ileri, geri hareket eden bir kontağı olan potansiyometrelere şerit potansiyometre denir. Döndürerek ayarlanabilen Döner Potansiyometreler de çok yaygındır. 

Potansiyometrenin 3 bacağı bulunur. Direncin iki ucunda iki bacak ve ortada da kayar kontağin bağlı olduğu bacak. 100KΩ'luk bir potansiyometren var diyelim. Bu potansiyometreyi en kısık olduğu konuma ayarlarsan, kayar kontağa yakın olan bacakla kayar kontak arasındaki direnç 0'a yakın, diğer bacakla kayar kontak arasında direnç ise 100KΩ'a yakın olur. Potansiyometre'yi ortaya ayarlarsan, her iki tarafın direnci de eşit ve 50KΩ olur. 


<img src="../images/potansiyometre.svg" alt="Potansiyometre devresi" style="height: 400px;">

Genellikle potansiyometreler, aynı bu şemadaki gibi, uç bacaklarından birine besleme gerilimi, birine de şase bağlanarak kullanılır. Bu şekilde kayar kontakta 0 ile besleme gerilimi arasında ayarlanabilir bir gerilim elde edilir. 

Şemadaki C ile işaretli diğer iki eleman ise kondansatör. Bunlar kayar kontağın sürtünmesi gibi mekanik gürültüleri filtreleyerek Arduino'nun okuduğu değerlerin daha kararlı olmasını sağlar. Şimdilik ona çok kafa yormaya gerek yok.

``` c
// Potansiyometre kitte A0 pinine bağlı.
int pot_pin = A0; 

void setup() {
  // Bir önceki derste öğrendiğimiz gibi seri iletişimi başlatıyoruz.
  Serial.begin(115200);
}

void loop() {
  // analogRead fonksiyonu, Arduino'nun analog pinlerinden birindeki
  // gerilim değerini döndürür.
  
  Serial.println(analogRead(pot_pin));
}
```



