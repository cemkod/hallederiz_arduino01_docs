# Proje 03 - Seri iletişim

## Giriş

Bu projede Arduino ile bilgisayar arasında nasıl seri iletişim yapabileceğini ögreneceksin.

Arduino'yu bilgisayarına bağlayan USB kablosu, Arduino'ya kod yüklemenin yanında bilgisayarla Arduino arasında veri alıp vermeye de yarıyor. Arduino IDE'nin içinde bu iletişimi test edebileceğin bir seri konsol da bulunuyor. Seri konsolun nasıl kullanıldığından da bahsedeceğim. 

## Kod

``` c
int sayi = 0;
void setup() {
  // Seri iletişime başlamadan önce Serial.begin() fonksiyonunun çağırılması
  // gerekli. 115200 sayısı baud cinsinden seri iletişimin hızını belirliyor. iletişimin
  // doğru çalışması için bilgisayar tarafında da seçilen hızın aynı olması
  // gerekir. 
  Serial.begin(115200);
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

Bu kez kodu yükleyip çalıştırdıktan sonra, ![Seri port ekranı](images/serial_button.png) butonuna basarak Seri port ekranını açman ve iletişim hızını kodda belirttiğimiz gibi 115200 olarak seçmen gerekiyor. Bunu yaptıktan sonra Arduino'dan saniyede bir gelen mesajları göreceksin.

--8<-- "snippets/sorun-giderme.md"

## Egzersizler

Arduino'dan bilgisayara USB bağlantısı üzerinden seri iletişim kurarak nasıl veri gönderebileceğini öğrenmiş oldun. Bu projede öğrendiklerinle aşağıdakileri yapabilir misin?

1. Seri iletişim hızını 300 baud'a ayarlayıp, seri konsolu da bu hızdakı iletişimi doğru okuyacak şekilde ayarlayabilir misin?
2. Sayının 0'dan başlayıp ileri saymak yerine, 300'den başlayıp geri saymasını sağlayabilir misin? [^1]

[^1]: ++ (artı-artı) operatörünün yaptığı işin tersini yapan operatör -- (eksi-eksi)
