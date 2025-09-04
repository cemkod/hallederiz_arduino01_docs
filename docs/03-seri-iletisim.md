# Proje 03 - Seri iletişim

## Giriş

Bu projede Arduino ile bilgisayar arasında nasıl seri iletişim yapabileceğini ögreneceksin.

Arduino'yu bilgisayarına bağlayan USB kablosu, Arduino'ya kod yüklemenin yanında bilgisayarla Arduino arasında veri alıp vermeye de yarıyor. Arduino IDE'nin içinde bu iletişimi test edebileceğin bir seri konsol da bulunuyor. Seri konsolun nasıl kullanıldığından da bahsedeceğim. 

## Yeni Kavramlar

### Serial.begin() Fonksiyonu
**Serial.begin()** fonksiyonu, Arduino'nun seri haberleşme özelliğini başlatır. Parantez içindeki sayı (örneğin 115200), iletişim hızını belirtir. Bu fonksiyon çalıştırılmadan Arduino bilgisayarla konuşamaz. Telefon konuşması başlamadan önce "Alo?" demek gibi düşünebilirsin.

### Baud Rate (İletişim Hızı)
**Baud rate**, saniyede kaç bit veri gönderileceğini belirtir. 115200 baud = saniyede 115200 bit demektir. Yaygın hızlar: 9600, 19200, 38400, 57600, 115200. Hem Arduino'da hem de bilgisayar tarafında aynı hız seçilmesi gerekir, yoksa mesajlar anlaşılmaz.

### ++ Artış Operatörü
**++** operatörü, bir değişkenin değerini 1 artırır. `sayi++` ifadesi `sayi = sayi + 1` ile aynı işi yapar ama daha kısa yazılır. Sayaç uygulamaları için çok kullanışlıdır. Tersine çalışan **--** operatörü de vardır.

### USB Üzerinden Veri İletişimi
Arduino'daki **USB portu**, aslında seri iletişimi USB protokolüne dönüştürür. Arduino'da bulunan özel çip (USB-Serial dönüştürücü) sayesinde bilgisayar Arduino'yu seri port olarak görür. Bu sayede USB kablosu hem programlama hem de veri iletişimi için kullanılır.

### Arduino IDE Seri Monitor
**Seri Monitor**, Arduino IDE'nin alt kısmındaki büyüteç simgesiyle açılır. Gelen mesajları gösterir ve Arduino'ya mesaj göndermenize yarar. Sağ alt köşesinden baud rate'i değiştirebilir, satır sonu karakterlerini ayarlayabilirsin.

### Seri İletişim Protokolü
**Seri iletişim**, verileri tek seferde bir bit olarak gönderir. Her veri paketi start bit ile başlar, 8 bit veri içerir, stop bit ile biter. Telefon hattında konuşma gibi: konuşmaya başladığını belirt, mesajını söyle, bittiğini belirt.

## Kod

``` c
int sayi = 0;
void setup() {
  // Seri iletişime başlamadan önce Serial.begin() fonksiyonunun çağırılması
  // gerekli. 9600 sayısı baud cinsinden seri iletişimin hızını belirliyor. iletişimin
  // doğru çalışması için bilgisayar tarafında da seçilen hızın aynı olması
  // gerekir. 
  Serial.begin(9600);
}

void loop() {
  // Serial.print() ve Serial.println() fonksiyonları seri porta bilgi 
  // yazmaya yarar. Aralarındaki fark println fonksiyonunun, gönderilen
  // veriden sonra özel bir satır sonu karakteri göndererek sonra gelenlerin
  // bir alt satıra yazılmasını sağlamasıdır.
  // Saniyede bir "Naberduino?" yazısını ve sayi değişkeninin değerini 
  // gönderiyoruz.
  Serial.print("Naberduino? ");
  Serial.println(sayi);
  sayi++; // sayi değişkenindeki değeri bir arttırır.
  delay(1000);
}
```

--8<-- "snippets/yukleme.md"

Bu kez kodu yükleyip çalıştırdıktan sonra, ![Seri port ekranı](images/serial_button.png) butonuna basarak Seri port ekranını açman ve iletişim hızını kodda belirttiğimiz gibi 9600 olarak seçmen gerekiyor. Bunu yaptıktan sonra Arduino'dan saniyede bir gelen mesajları göreceksin.

--8<-- "snippets/sorun-giderme.md"

## Egzersizler

Arduino'dan bilgisayara USB bağlantısı üzerinden seri iletişim kurarak nasıl veri gönderebileceğini öğrenmiş oldun. Bu projede öğrendiklerinle aşağıdakileri yapabilir misin?

1. Seri iletişim hızını 300 baud'a ayarlayıp, seri konsolu da bu hızdakı iletişimi doğru okuyacak şekilde ayarlayabilir misin?
2. Sayının 0'dan başlayıp ileri saymak yerine, 300'den başlayıp geri saymasını sağlayabilir misin? [^1]

[^1]: ++ (artı-artı) operatörünün yaptığı işin tersini yapan operatör -- (eksi-eksi)
