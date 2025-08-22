# Proje 02 - Buton

## Giriş

Bu projede Kit üzerindeki butonu nasıl okuyabileceğini öğreneceksin.

Arduino'nun dijital pinlerinin giriş olarak da ayarlanabileğini anlatmıştım. Bu 
kez butonun bağlı olduğu 7 nolu pin'i kullanarak LED'i kontrol etmeyi deneyeceğiz.

## Elektronik 

<img src="../images/buton.svg" alt="Buton devresi" style="height: 400px;">

Şemaya bakacak olursan, Arduino'nun 7 nolu pininin, R8 direnci üzerinden 5 voltluk
besleme gerilimine bağlı olduğunu farkedeceksin. Bu direnç butona basılmadığı durumda
pinde 5Vluk bir gerilim olmasını, yani pinin değerinin yüksek (HIGH) olarak okunmasını
sağlıyor. 

Dijital girişlerde gerilim kaynağına veya şaseye bağlı bu tür dirençler çok kullanılır. Bunun sebebi hiçbir şeye bağlı olmayan bir giriş bacağının değerinin belirsiz olmasıdır. Böyle boşta duran bir bacağı okuduğunda bazen LOW bazen HIGH değer okursun, ne olacağı hiç belli olmaz. Bu yüzden buton basılı değilken sürekli HIGH okumak içın böyle bir dirence ihtiyaç var. Bu dirence Pull-Up (Yukarı çeken) direnç ismi verilir.

Butonun da bir tarafı arduino'ya bir tarafı ise şaseye, yani USB'den aldığımız
besleme geriliminin eksi tarafına bağlı. Böylece butona basıldığında pin üzerinde
şase ile aynı gerilim seviyesi, yani sıfır volt oluyor. Bu da pin'in değerinin düşük
(LOW) olarak okunmasını sağlıyor.

## Yeni Kavramlar

### INPUT Sabiti
**INPUT**, Arduino'nun önceden tanımlanmış bir sabitidir. Bir pini giriş olarak ayarlamak için `pinMode()` fonksiyonunda kullanılır. Bu sabit sayesinde pin üzerinden gelen elektriksel sinyalleri okuyabilirsin.

### Pull-up Direnci 
**Pull-up direnci**, dijital giriş pinlerinin belirsizlik sorununu çözer. Hiçbir şeye bağlı olmayan bir pin "havada asılı" kalır ve değeri sürekli değişir. Pull-up direnci, buton basılmadığında pini 5V'a çekerek sabit HIGH değer sağlar. Su musluğu kapalıyken bile boruda basınç olması gibi düşünebilirsin.

### Karşılaştırma Operatörü (==)
**==** operatörü, iki değerin birbirine eşit olup olmadığını kontrol eder. `digitalRead(butonPin) == LOW` ifadesi "buton pininin değeri LOW'a eşit mi?" sorusunu sorar. Eğer eşitse `true` (doğru), değilse `false` (yanlış) sonucu döner.

### Boolean Mantığı
Arduino'da **HIGH** ve **LOW** değerleri aslında boolean (mantıksal) değerlerdir. HIGH = true (doğru/1), LOW = false (yanlış/0) anlamına gelir. Bu ikili sistem tüm dijital elektroniğin temelidir.

### Dijital Okuma Prensibi
`digitalRead()` fonksiyonu, pin üzerindeki gerilimi okur ve yorumlar. 3.3V'un üzerindeki gerilimi HIGH, 1.7V'un altındakini LOW olarak kabul eder. Bu sayede elektronik sinyaller sayısal değerlere dönüşür.

## Kod

``` c
// Burada butonPin ve LedPin isimli iki tane tamsayı (int) tipinde değişken
// tanımladık. Biri butonun bağlı olduğu 7 nolu pini, diğeri LED'in bağlı
// olduğu 10 numaralı pini işaret ediyor.
int butonPin = 7;
int LedPin = 10;

void setup() {
  // LedPin'i ilk örnekteki gibi çıkış (OUTPUT), butonPin'i ise giris (INPUT)
  // olarak ayarlayalım.
  pinMode(LedPin, OUTPUT);
  pinMode(butonPin, INPUT);
}

void loop() {
  // Bazı şeyleri koşullu olarak yapmak için, bu if-else yapısını kullanıyoruz.
  // Koşulumuz parantezlerin içindeki digitalRead(butonPin) == LOW ifadesi
  // digitalRead() fonksiyonu giriş olarak ayarlanmış bir pinin o sırada
  // yüksek (HIGH) mi yoksa düşük (LOW) mü olduğunu geri döndürüyor.
  // == LOW ifadesi ise bu if blokunun digitalRead'in döndürdüğü değerin 
  // LOW olması halinde çalışmasını sağlıyor.
  if (digitalRead(butonPin) == LOW) {
    // butonPin LOW ise, yani buton basılı durumdaysa bu kod çalışacak 
    // ve LED'in yanmasını sağlayacak.
    digitalWrite(LedPin, HIGH);
  } else {
    // butonPin HIGH ise, bu kod çalışacak ve LED'i söndürecek.
    digitalWrite(LedPin, LOW);
  }
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında butona basılı tuttuğun sürece LED yanacak, bıraktığında ise sönecek. 

--8<-- "snippets/sorun-giderme.md"

## Egzersizler

Böylece digitalRead() fonksiyonunu kullanarak nasıl bir girişi okuyabileceğini
öğrenmiş oldun. Bu projede öğrendiklerinle aşağıdakileri yapabilir misin?

1. LED'in butona basılmadığı zaman yanıp, basıldığında sönmesini sağlayabilir misin? [^1]
2. LED'in butona her basıldığında 1 saniye yanıp sonra sönmesini sağlayabilir misin? [^2]

[^1]:
  Tek bir kelimeyi değiştirerek yapmak mümkün.

[^2]:
  delay() fonksiyonunu hatırladın mı?


