# Proje 05 - Buzzer

## Giriş

Bu projede Arduino'nun nasıl ses çıkarabileceğini öğreneceksin. Tabii ki profesyonel bir müzik aleti kadar kaliteli olmayacak ama... internetin en ünlü şarkısını çalmaya yetecek kadar iyi! 🎵

## Elektronik

<img src="../images/buzzer.svg" alt="Buzzer devresi" style="height: 400px;">

Devrede Q103 olarak işaretlenmiş eleman bir NPN transistör. LED devresinde gördüğün transistörle aynı çalışma prensibi var - elektrikle açılıp kapanabilen bir anahtar gibi düşünebilirsin. 

Arduino'nun 3 numaralı dijital pini, R284 direnci (2KΩ) üzerinden transistörün baz bacağına bağlı. R302 direnci (10KΩ) ise bir "pull-down" direnci olarak görev yapıyor - Arduino pini aktif olmadığında transistörün bazını sıfır volta çekerek transistörün tamamen kapalı kalmasını sağlıyor.

Arduino'nun 3 numaralı pininden HIGH (5V) sinyal geldiğinde, R284 üzerinden transistörün bazına akım gider ve transistör iletime geçer. Bu durumda R303 direnci ve buzzer üzerinden akım akmaya başlar. Buzzer'ın içindeki piezoelektrik kristal bu akımla titreşerek ses çıkarır.

D004 olarak işaretlenmiş 1N4148 diyodu, buzzer'ın bacakları arasına ters olarak bağlanmış bir koruma diyodu. Buzzer gibi bobinli elemanlar aniden kapatıldığında ters yönde yüksek gerilim üretebilir. Bu diyot o zararlı gerilimi sönümleyerek transistörü korur.

C19 kondansatörü (100nF) güç kaynağındaki elektrriksel gürültüleri filtreleyerek daha temiz bir ses elde edilmesini sağlar.

Buzzer'dan çıkacak sesin frekansı, Arduino'nun `tone()` fonksiyonuyla belirlenir. Bu fonksiyon pin 3'e belirlenen frekansta HIGH-LOW sinyalleri göndererek buzzer'ı o frekansla titreştirir ve böylece istenen notayı çalar.

## Kod

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

/*
Diziler (arrays), aynı tipte birden fazla değeri bir arada saklamamızı sağlar.
Burada melodi[] dizisi, çalmak istediğimiz notaların frekanslarını sırayla tutuyor.
Her bir nota, dizinin bir elemanı olarak saklanıyor. Dizideki ilk eleman 0. sırada,
ikinci eleman 1. sırada... şeklinde devam eder.
*/
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

/*
sureler[] dizisi, her notanın ne kadar süre çalınacağını milisaniye cinsinden tutuyor.
melodi[] dizisindeki her nota ile aynı sıradaki süre birbirine karşılık gelir.
Örneğin: melodi[0] notası, sureler[0] süre kadar çalınır.
*/
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
  /*
  pinMode() fonksiyonunu kullanarak, Arduino'nun pinlerini giriş veya 
  çıkış olarak ayarlayabilirsin. Buzzer için pin 3'ü çıkış olarak ayarlıyoruz.
  */
  pinMode(buzzerPin, OUTPUT);
}

void loop() {
  /*
  for döngüsü, aynı işlemi tekrar tekrar yapmamızı sağlar.
  Bu döngü 3 kısımdan oluşur:
  1. Başlangıç: int i = 0 (i değişkenini 0'dan başlat)
  2. Koşul: i < notaSayisi (i, toplam nota sayısından küçük olduğu sürece devam et)
  3. Artış: i++ (her döngüde i'yi 1 artır)
  
  Bu sayede melodi dizisindeki tüm notaları sırayla çalmış oluyoruz.
  */
  for (int i = 0; i < notaSayisi; i++) {
    if (melodi[i] > 0) {
      /*
      tone() fonksiyonu buzzer'dan ses çıkartmamızı sağlar.
      3 parametre alır:
      1. buzzerPin: Ses çıkacak pin numarası
      2. melodi[i]: Çalınacak notanın frekansı (Hz cinsinden)
      3. sureler[i]: Notanın çalınma süresi (milisaniye)
      
      Frekans ne kadar yüksekse, ses o kadar tiz olur.
      Örneğin: DO_3 (262 Hz) → RE_3 (294 Hz) daha tizdir.
      */
      tone(buzzerPin, melodi[i], sureler[i]);
    } else {
      /*
      Eğer melodi[i] negatif bir değerse (ES = -1), bu sessizlik demektir.
      noTone() fonksiyonu buzzer'ı susturur.
      */
      noTone(buzzerPin);
    }
    
    /*
    ÖNEMLİ: tone() fonksiyonu ses çalmaya başlar ama kodun devam etmesini beklemez!
    Eğer delay() koymazsak, kod hemen bir sonraki notaya geçer ve her nota sadece 
    çok kısa bir süre (mikrosaniye) çalar. Bu durumda melodi çok hızlı geçer ve 
    tanınmaz hale gelir. delay(sureler[i]) ile her notanın belirlenen süre kadar 
    çalmasını sağlıyoruz.
    */
    delay(sureler[i]);
    
    /*
    Notayı temiz bir şekilde bitirmek için noTone() çağırıyoruz.
    Sonra kısa bir ara (delay(sureler[i] * 0.3)) vererek notalar 
    arasında boşluk bırakıyoruz. Bu, gerçek müzikte olduğu gibi 
    notaların birbirinden ayrılmasını sağlar.
    */
    noTone(buzzerPin);
    delay(sureler[i] * 0.3);
  }
  
  /*
  Melodi bittiğinde 2 saniye (2000 milisaniye) bekleriz.
  Bu sayede melodi tekrar çalmadan önce bir duraklama olur.
  */
  delay(2000);
}
```

--8<-- "snippets/yukleme.md"

Kod çalıştığında buzzer'dan çalan tanıdık bir melodi duyacaksın. 

--8<-- "snippets/sorun-giderme.md"

Böylece buzzer'dan ses çıkartmayı ve for döngülerini kullanmayı öğrenmiş oldun. Bu projede öğrendiklerinle aşağıdakileri yapabilir misin?

1. Butonla beraber kullanarak butona basıldığında bu melodiyi çalan bir kapı zili yapabilir misin?
2. Potansiyometre ile melodimin çalma hızını kontrol edebilir misin?[^1]

[^1]: delay() değerlerini birşeyle çarpman gerekecek.


