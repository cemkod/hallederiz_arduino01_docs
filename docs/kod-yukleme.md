# Arduino'ya Kod Yükleme Rehberi

## Giriş

Bu rehberde Arduino kitine nasıl program yükleyeceğini adım adım öğreneceksin. Arduino'ya kod yüklemek, yazdığın programı Arduino kartının hafızasına kaydetmek ve çalıştırmak anlamına gelir. Bu işlem sayesinde Arduino'nun ne yapacağını sen belirleyebilirsin.

Arduino kartına program yüklemek için bilgisayarında Arduino IDE'nin kurulu olması gerekiyor. Eğer henüz kurmadıysan, önce [kurulum rehberini](windows-kurulum.md) takip etmelisin.

## Kod Yükleme İşleminin 5 Adımı

Arduino'ya kod yüklemek için şu adımları sırayla takip etmen gerekiyor:

1. [Arduino IDE'yi açmak](#1-arduino-ideyi-ac)
2. [Arduino kartını bilgisayara bağlamak](#2-arduino-kartini-bilgisayara-bagla)
3. [Kart ve port seçimi yapmak](#3-kart-ve-port-secimi)
4. [Kodu yazmak veya açmak](#4-kodu-yazmak-veya-acmak)
5. [Kodu Arduino'ya yüklemek](#5-kodu-arduinoya-yukle)

---

## 1. Arduino IDE'yi Aç

İlk olarak bilgisayarında Arduino IDE programını açman gerekiyor. Programı açmak için:

- **Windows'ta**: Başlat menüsünden "Arduino IDE" yazarak bulabilir veya masaüstündeki simgeye çift tıklayabilirsin
- **Masaüstü kısayolu varsa**: Arduino IDE simgesine çift tıkla

Arduino IDE açıldığında karşına şuna benzer bir ekran çıkacak:

```cpp
void setup() {
  // put your setup code here, to run once:

}

void loop() {
  // put your main code here, to run repeatedly:

}
```

Bu boş kod şablonu normal - Arduino her zaman `setup()` ve `loop()` fonksiyonları ile başlar.

---

## 2. Arduino Kartını Bilgisayara Bağla

Arduino kitini bilgisayarına bağlamak için USB kablosuna ihtiyacın var. Kitinle birlikte gelen USB kablosunu veya herhangi bir USB-C tipi kabloyu kullanabilirsin.

### USB Bağlantısı

1. **USB kablosunun bir ucunu** Arduino kartındaki USB portuna tak
2. **Diğer ucunu** bilgisayarının USB portuna tak
3. **Arduino kartındaki güç LED'i** yanmalı (kırmızı renkte, minicik)

!!! warning "Kablo Tipi Önemli"
    Sadece **data kablosu** kullan. Bazı USB kabloları sadece şarj için tasarlanmıştır ve veri aktarımı yapamazlar. 

### Arduino Kartı Hazır mı?

Arduino kartı doğru bağlandığında:
- Kartın üzerindeki **güç LED'i yanar**
- **Windows'ta**: Bilgisayarın sağ alt köşesinde "Cihaz tanındı" benzeri bir bildirim çıkabilir
- **Ses efekti**: Windows "cihaz bağlandı" sesi çıkarabilir

---

## 3. Kart ve Port Seçimi

Arduino IDE'nin hangi Arduino kartıyla ve hangi USB portu üzerinden iletişim kuracağını belirtmen gerekiyor.

### Kart Seçimi

Arduino kitindeki kart **Arduino Uno** olduğu için:

1. **Araçlar (Tools)** menüsüne tıkla
2. **Kart (Board)** seçeneğine git
3. **"Arduino AVR Boards"** altından **"Arduino Uno"** seçeneğini işaretle

### Port Seçimi

USB portu seçimi için:

1. **Araçlar (Tools)** menüsüne tıkla  
2. **Port** seçeneğine git
3. **Arduino Uno yazısının bulunduğu port** seçeneğini işaretle

İşletim sistemine göre port şuna benzer görünecek:

**Windows'ta**:
- `COM3 (Arduino Uno)`
- `COM4 (Arduino Uno)`

**macOS'ta**:
- `/dev/cu.usbmodem14101 (Arduino Uno)`
- `/dev/cu.usbmodem621 (Arduino Uno)`

**Linux'ta**:
- `/dev/ttyACM0 (Arduino Uno)`
- `/dev/ttyUSB0 (Arduino Uno)`

!!! note "Port Görünmüyor mu?"
    Eğer "Arduino Uno" yazısı olan bir port göremiyorsan:
    
    - Arduino kartının USB kabloyla doğru bağlandığından emin ol
    - USB kablosunu çıkarıp tekrar tak
    - Arduino IDE'yi kapat ve tekrar aç
    - Farklı bir USB portu dene

---

## 4. Kodu Yazmak veya Açmak

Arduino'ya yüklemek istediğin kodu IDE'ye yazman veya açman gerekiyor. İlk deneme için basit bir örnek kullanabilirsin.

Eğer daha önce birşeyler yazıp kaydettiysen ve onu yüklemek istiyorsan:
1. **Dosya (File)** menüsüne git
2. **Aç** seçeneğine tıkla
3. Kaydettiğin dosyayı seç (ino uzantılı olmalı)

Aşağıdaki kod, kitteki büyük kırmızı LED'i saniyede bir yakıp söndürecek. Direk buradan bu kodu kopyalayıp yapıştırabilirsin veya zaten birşeyler yazdıysan ve onu atmak istiyorsan bir sonraki adıma geçebilirsin.

```cpp
void setup() {
  pinMode(10, OUTPUT);
}

void loop() {
  digitalWrite(10, HIGH);
  delay(1000);
  digitalWrite(10, LOW);
  delay(1000);
}
```

## 5. Kodu Arduino'ya Yükle

### Adım 1: Kodu Doğrula (Mutlaka gerekli değil ama yap bence)

Bu buton yüklemeden önce kodunda hata olup olmadığını kontrol etmeni sağlar.

1. **Doğrula butonuna** ![Verify button](../images/verify_button.png) tıkla
2. IDE kodunu derleyip hata kontrolü yapacak
3. **Alt kısımda yeşil "Derleme tamamlandı" mesajı** çıkarsa kod hazır
4. **Kırmızı hata mesajları** varsa önce bunları düzeltmen gerekir

### Adım 2: Kodu Yükle

1. **Yükle butonuna** ![Upload button](../images/upload_button.png) tıkla
2. IDE kodu derleyecek ve Arduino'ya gönderecek
3. **Kitin uzerindeki Mavi LEDin  yanıp söndüğünü göreceksin**
4. **Alt kısımda "Yükleme tamamlandı" mesajı** çıkacak

### Yükleme Başarılı Oldu mu?

Kod başarıyla yüklendiğinde:

- **Alt pencerede** "Yükleme tamamlandı" mesajı görünür
- **Arduino kartı** kodu çalıştırmaya başlar
- **Blink örneği için**: Arduino'daki dahili LED yanıp sönmeye başlar

---

## Yaygın Sorunlar ve Çözümleri

### Arduino IDE Port Bulamıyor

**Problem**: Port menüsünde Arduino görünmüyor

**Çözüm**:

1. USB kablosunu çıkar, 5 saniye bekle ve tekrar tak

2. Arduino IDE'yi kapat ve yeniden aç  

3. Farklı bir USB kablosu dene

### "avrdude: stk500_recv(): programmer is not responding" Hatası

**Problem**: Arduino ile iletişim kurulamıyor

**Çözüm**:

1. Doğru port seçildiğinden emin ol

2. Arduino'yu USB'den çıkar ve tekrar tak

3. Arduino IDE'yi yeniden başlat

4. Kart seçiminin "Arduino Uno" olduğunu kontrol et

### Kod Yüklendikten Sonra Çalışmıyor

**Problem**: Kod yüklendi ama Arduino beklediğin gibi çalışmıyor

**Çözüm**:

1. Arduino'nun güç aldığından emin ol (minik kırmızı LED yanıyor mu?)

2. Kodunu tekrar gözden geçir

3. Seri monitörü aç (Araçlar > Seri Monitör) ve çıktıları kontrol et

4. Arduino'yu resetle (Reset butonu, büyük kırmızı LED'in solunda. Birazcık küçük)

---

!!! tip "Pro İpucu"
    Kod yazarken sık sık kaydet (Ctrl+S). Arduino IDE bazen beklenmedik şekilde kapanabilir ve kaydedilmemiş kodunu kaybedebilirsin.