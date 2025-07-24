# Proje 01 - LED

## Giriş

Bu projede set üzerindeki LED'i nasıl yakıp söndürebileceğini öğreneceksin.

Arduino'da elektronik devreleri kontrol etmek için bir takım giriş ve 
çıkış pinleri bulunuyor. Kit üzerindeki kırmızı LED, Arduino'nun 10 numaralı 
dijital pinine bağlı ufak bir devreden oluşuyor. Bu devrenin şemasını aşağıda
bulabilirsin.

## Elektronik

<img src="../images/LED.svg" alt="LED devresi" style="height: 400px;">

Devrede Q1 olarak işaretlenmiş eleman bir transistör. Transistörleri elektrikle 
açılıp kapanabilen bir anahtar gibi düşünebilirsin. Şema'da solunda gözüken 
bacağına baz ismi veriliyor ve bu bacakla, alttaki emitör bacağı arasında bir 
gerilim olduğunda transistör iletime geçiyor. İletimde olduğunda üstteki bacağı  
kollektörden, emitöre doğru bir akım akmasına izin veriyor. Baz gerilimi 
kesildiğinde ise transistörden akım akmıyor. Transistör iletime geçtiğinde, 
kollektöre bağli LED üzerinden bir akım akıyor ve LED yanıyor.

## Kod

Şimdi Arduino IDE'yi bilgisayarında açıp aşağıdaki kodu yazabilirsin. Veya indirdiğin projeler arşivinin içindeki bu kodun hazır halini Arduino IDE'ye yükleyebilirsin.

``` c
/* 
Aşağıdaki satır, LedPin adında bir değişkene 10 değerini tanımlıyor.
Değişkenler, sonradan değiştirilebilir bir değer saklarlar
int, tanımlamak istediğimiz değişkenin integer yani tam sayı tipinde
olduğunu belirtiyor. 

LedPin değişkenimizin ismi. Değişken isimleri büyük küçük harflere 
duyarlı. Yani LedPin başka bir isim, ledpin başka bir isim.
Son olarak da "= 10" kısmı bu değişkene, LED devresinin
bağlı olduğu pin numarasi olan 10 değerini tanımlıyor. 
*/
int LedPin = 10;

/*
setup() fonksiyonu, her Arduino kodunda bulunur ve devre ilk kez güç
verildiğinde veya devre resetlendiğinde bir kez çalışır.
*/

void setup() {
  /* 
  pinMode() fonksiyonunu kullanarak, Arduino'nun pinlerini giriş veya 
  çıkış olarak ayarlayabilirsin. İlk güç verildiğinde tüm dijital 
  pinler giriş olarak ayarlıdır. Biz LedPin'i çıkış olarak 
  kullanacağımız için burada bu pini çıkış olarak ayarlayalım.
  */
  pinMode(LedPin, OUTPUT);
}

/*
loop() fonksiyonu, setup fonksiyonunun ardından sürekli olarak 
bir döngü halinde çalışır. Bu fonksiyon da her Arduino kodunda bulunur.
*/
void loop() {
  /*
  digitalWrite() fonksiyonunu kullanarak çıkış olarak ayarlanmış 
  bir dijital
  pinin çıkışını aktif (HIGH) ve inaktif (LOW) olarak ayarlayabilirsin.
  Burada LedPin'i aktif yaparak LED'in yanmasını sağlıyoruz.
  */
  digitalWrite(LedPin, HIGH);
  /*
  delay() fonksiyonu kodun işlemesini milisaniye cinsinden belirtilen süre
  kadar durdurur. Burada 1000 milisaniye, yani 1 saniye bekliyoruz.
  */
  delay(1000);

  // Pinin çıkışını inaktif hale getirerek LED'i söndürüyoruz.
  digitalWrite(LedPin, LOW);

  // Yeniden 1s bekliyoruz.
  delay(1000);

  /*
  loop fonksiyonu burada bitiyor. 
  Biter bitmez, başından tekrar başlayacak.
  */
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında kit üzerinde bulunan LED 1 saniye aralıklarla yanıp 
sönmeye başlayacak. 

--8<-- "snippets/sorun-giderme.md"

## Egzersizler

Böylece ilk Arduino kodunu çalıştırmış oldun. Bu projede öğrendiklerinle
aşağıdakileri yapabilir misin?

1. LED'in yanıp sönme hızını iki katına çıkarabilir misin?
2. LED'in yanıp sönme bekleme sürelerini 20ms gibi insan gözünün algılayamayacağı
  kadar hızlı yaparsan ne oluyor? [^1]
3. LED'i 30ms yanık, 10ms sönük tutarsan ne olur? [^2]

[^1]: 
    İnsan gözü LED'in bu hızda yanıp söndüğünü göremez ama artık gözüne toplamda 
    daha az ışık ulaştığı için LED'in parlaklığı yarı yarıya azalmış gibi olur.
[^2]:
    Şimdi artık LED, toplam 40ms'lik zamanın %75'ini yanık geçirdiği için, 
    LED'in görünen parlaklığı %75'ine inmiş olur. LED'lerin parlaklığını 
    değiştirmek için dahice bir yöntem!

