# Arduino Sorun Giderme Rehberi

## Giriş

Arduino ile çalışırken karşılaştığın sorunlar için hazırlanmış bu rehber, en yaygın problemlerin çözümlerini içeriyor. Arduino kartına program yüklemekten, kod hatalarına kadar birçok soruna çözüm bulabilirsin. 

Bu rehberdeki çözümleri sırayla deneyerek problemini giderebilirsin. Eğer sorunun devam ederse, deneyimli Arduino kullanıcılarından yardım isteyebilir veya yerel maker topluluklarına katılabilirsin.

---

## Program Yükleme Sorunları

### Arduino kartıma program yükleyemiyorum

Arduino kartına program yüklemek için birçok bileşenin doğru çalışması gerekiyor. Bunlar arasında sürücüler, Arduino IDE'deki kart ve port ayarları, fiziksel bağlantı, bootloader ve daha pek çok şey bulunuyor. Aşağıdaki adımları sırayla takip ederek sorununu çözebilirsin.

#### Arduino IDE Ayarları

**1. Kart Seçimi**
`Araçlar > Kart` menüsünden doğru kart türünü seçtiğinden emin ol. Arduino Uno kullanıyorsan "Arduino Uno" seçmen gerekiyor. Daha eski Arduino Duemilanove kartların ATmega328P işlemcisi varken, çok eski olanların ATmega168 işlemcisi var. Kontrol etmek için kartındaki büyük çip üzerinde yazan yazıyı oku.

**2. Port Seçimi**  
`Araçlar > Port` menüsünden doğru portu seçtiğinden emin ol. Port görünmüyorsa, Arduino kartı bağlıyken IDE'yi yeniden başlat.

* **Windows'ta**: COM3, COM4 gibi bir port olacak. Doğru portu bulmak için Cihaz Yöneticisi > Portlar kısmına bak.
* **Mac'te**: `/dev/tty.usbmodem621` (Uno/Mega 2560 için) veya `/dev/tty.usbserial-A02f8e` (eski FTDI kartlar için) gibi görünecek.
* **Linux'te**: `/dev/ttyACM0` (Uno/Mega 2560 için) veya `/dev/ttyUSB0` (eski kartlar için) şeklinde olacak.

!!! warning "Port görünmüyorsa"
    Eğer kartın için uygun port görünmüyorsa, sürücü sorunu olabilir. Aşağıdaki "Sürücüler" bölümüne bak.

#### Sürücüler

Sürücüler, bilgisayarındaki yazılımın (Arduino IDE) donanımınla (Arduino kartı) iletişim kurmasını sağlar. Arduino Uno ve Mega 2560 işletim sisteminin standart sürücülerini kullanır. Diğer Arduino kartları genellikle FTDI sürücülerini kullanır.

Sürücülerin doğru kurulu olup olmadığını kontrol etmenin en kolay yolu Arduino kartı bağlıyken `Araçlar > Port` menüsüne bakmaktır. Kart bağlıyken bu menüde fazladan seçenek görünmeli.

**Windows'ta Sürücü Sorunları:**

* **Windows 7 (özellikle 64-bit)**: Cihaz Yöneticisi'ne git, karta sağ tıkla ve sürücüyü güncelle. Arduino yazılımının `drivers/` klasöründeki `.inf` dosyasını göster.

* **Windows XP'te "The system cannot find the file specified" hatası**: [Bu çözümü](http://forums.techguy.org/1680041-post1.html) dene (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion altına "RunOnce" anahtarı ekleme).

**Linux'te Sürücü Sorunları:**

Uno ve Mega 2560 Linux'te `/dev/ttyACM0` şeklinde görünür. Standart RXTX kütüphanesi bu cihazları desteklemez, ama Arduino yazılımıyla gelen özel RXTX sürümü destekler. 

Eğer kendi RXTX pakettini kullanıyorsan, şu komutları çalıştır:

```bash
sudo usermod -a -G tty kullaniciAdin
sudo usermod -a -G dialout kullaniciAdin
```

Sonra çıkış yap ve tekrar gir.

#### Seri Port Erişimi

**Windows'ta:**
* IDE yavaş başlıyorsa veya Araçlar menüsü açılmıyorsa, Cihaz Yöneticisi'nden Bluetooth seri portlarını devre dışı bırak.
* USB Cellular Wifi yazılımları, PDA senkron programları, Bluetooth sürücüleri gibi tüm seri portları tarayan programları kapat.
* ZoneAlarm gibi güvenlik duvarlarının seri portu engellemediğinden emin ol.

**Tüm Sistemler:**
* Processing, PD, vvvv gibi programlar Arduino ile seri bağlantı kullanıyorsa kapat.
* Linux'te geçici olarak Arduino IDE'yi root olarak çalıştırmayı dene.

#### Fiziksel Bağlantı

* Kartın açık olduğundan (yeşil LED yanıyor) ve bilgisayara bağlı olduğundan emin ol.
* Arduino Uno ve Mega 2560'ın Mac'te USB hub üzerinden bağlantı sorunu yaşayabilir. Kartı doğrudan bilgisayara bağla ve Arduino IDE'yi yeniden başlat.
* Program yüklerken 0 ve 1 numaralı dijital pinlerin bağlantısını kes (bunlar seri iletişim için kullanılır).
* Kartın üzerinde hiçbir şey bağlı değilken program yüklemeyi dene.
* Kartın metal bir yüzeye değmediğinden emin ol.
* Farklı bir USB kablosu dene.

#### Otomatik Sıfırlama

* Kartın otomatik sıfırlama özelliği yoksa (eski kartlar), program yüklemeden birkaç saniye önce sıfırlama butonuna bas.
* Bazı Diecimila kartlar yanlış bootloader ile gelmiştir ve manuel sıfırlama gerektirir.
* Bazı bilgisayarlarda program yükleme butonuna bastıktan sonra kart üzerindeki sıfırlama butonuna basman gerekebilir. Farklı zaman aralıkları dene (10 saniyeye kadar).

!!! tip "Bootloader Kontrolü"
    Kartında bootloader olup olmadığını kontrol etmek için kartı sıfırla. Pin 13'teki dahili LED yanıp sönmeli. Yanıp sönmüyorsa bootloader olmayabilir.

#### Forum Desteği

Hala çalışmıyorsa deneyimli bir Arduino kullanıcısından yardım isterken şu bilgileri paylaş:

* İşletim sistemin
* Kart türün (Mini, LilyPad vs. ise devrenin fotoğrafı)
* Daha önce program yükleyebildin mi? Çalışmayı ne zaman bıraktı?
* Ayrıntılı çıktı modu açık olarak denediğindeki hata mesajları

Ayrıntılı çıktı modunu açmak için: `Dosya > Tercihler > Aşağıdaki durumlarda ayrıntılı çıktı göster: > yükleme` seçeneğini işaretle.

---

### Kartım açılmıyor (yeşil güç LED'i yanmıyor)

Arduino Diecimila veya daha eski bir USB kart kullanıyorsan, USB fişinin yanındaki jumper'ın (küçük plastik parça) doğru konumda olduğundan emin ol.

* **Harici güç kaynağı kullanıyorsan** (güç girişine adaptör takılı): Jumper güç girişine en yakın iki pine takılmalı.
* **USB ile güç alıyorsan**: Jumper USB fişine en yakın iki pine takılmalı.

### Program yükleniyormuş gibi görünüyor ama bir şey olmuyor

**1. Yanlış İşlemci Seçimi**
`Araçlar > İşlemci` menüsünden doğru işlemciyi seçtiğinden emin ol. Kartındaki en büyük çip üzerindeki yazıyı oku (ATmega8 veya ATmega168).

**2. Gürültülü Güç Kaynağı**
Güç kaynağındaki gürültü çipin programını kaybetmesine neden olabilir.

**3. Program Çok Büyük**
ATmega8'de bootloader 1-2 Kb yer kaplar. Eski bootloader'lar daha büyükse sadece programının bir kısmı yüklenebilir. Bu durumda kart sürekli sıfırlanır.

Çözüm: Tercihler dosyasında `upload.maximum_size` değerini 7168'den 6144'e değiştir.

### Programım kart açılınca çalışmıyor

Bu sorun genellikle kart ilk açıldığında seri port üzerinden veri gönderilmesi nedeniyle olur. İlk birkaç saniye bootloader bilgisayardan yeni program gelmesini bekler. Sürekli veri geliyorsa hiç zaman aşımına uğramaz ve mevcut program hiç başlatılmaz.

**Çözümler:**
* `setup()` fonksiyonunda veri gönderen çipi etkinleştir
* Harici programlayıcı ile program yükle (bootloader'ı değiştir)

---

## Arduino IDE Sorunları

### Arduino IDE'deki menü yazıları bozuk görünüyor

Bu sorun genellikle Windows'taki standart olmayan fontların ClearType özelliği ile çakışmasından kaynaklanır.

**Çözüm:**
1. Arduino IDE kurulum klasöründeki `arduino.l4j.ini` dosyasını aç
2. Şu satırı ekle:
   ```
   -Dawt.useSystemAAFontSettings=on
   ```
3. Dosyayı kaydet ve Arduino IDE'yi yeniden başlat

### Mac'te "Build folder disappeared" hatası

Arduino.app dosyasını disk görüntüsünden (dmg) Uygulamalar klasörüne sürükledin mi? Sürüklemediysen örnekleri yükleyemezsin.

### Mac'te Java güncellemesi sonrası Arduino çalışmıyor

Apple'ın en son Java güncellemesi 64-bit kütüphaneler kullanmaya çalışır, ama Arduino IDE 32-bit RXTX kütüphanesiyle gelir.

**Çözüm:**
1. Finder'da Arduino uygulamasına (ör. Arduino 16.app) tıkla
2. `Dosya` menüsünden `Bilgi Al` seç
3. Bilgi panelinde `32 Bit Modunda Aç` seçeneğini işaretle

### Program derlerken java.lang.StackOverflowError hatası alıyorum

Arduino IDE kodunu işlemek için düzenli ifadeler (regular expressions) kullanır. Bazı metin dizileri bu işlemi karıştırabilir.

**Çözüm:**
Kodunda olağandışı karakter dizileri ara:
* Eksik tırnak işaretleri
* `\\"` dizisi (bunun yerine `\"` kullan)  
* Yorum satırları içindeki özel karakterler

### Windows'ta arduino.exe çalışmıyor

Windows'ta arduino.exe'ye çift tıklayınca hata alıyorsan:

`Arduino has encountered a problem and needs to close.`

Bu durumda `run.bat` dosyasını kullanarak Arduino'yu başlat. Arduino ortamının açılması biraz zaman alabilir.

---

## Bağlantı ve Donanım Sorunları

### Kartım Araçlar menüsünde görünmüyor

**1. Sürücü Kontrolü**
USB Arduino kart kullanıyorsan FTDI sürücülerini kurduğundan emin ol. USB-Seri dönüştürücü kullanıyorsan onun sürücülerini kur.

**2. Kart Bağlantısı**  
Kartın takılı olduğundan emin ol. Araçlar menüsü her açıldığında yenilenir, bu yüzden kartı yeni çıkardıysan menüde görünmez.

**3. Port Çakışması**
PDA senkronizasyon uygulamaları, Bluetooth sürücüleri, sanal daemon araçları gibi tüm seri portları tarayan programları kapat.

**Windows'ta İlave İpuçları:**
* COM port numarası çok yüksekse (COM17 gibi) daha düşük bir numaraya değiştir
* Cihaz Yöneticisi'nden sanal Bluetooth portlarını sil

### USB sürücü sorunları yaşıyorum

[FTDI'dan en son sürücüleri](http://www.ftdichip.com/Drivers/VCP.htm) indir veya [support1@ftdichip.com](mailto:support1@ftdichip.com) adresinden destek al.

### Windows'ta Arduino IDE çok yavaş açılıyor

Arduino IDE başlarken ve Araçlar menüsünü açarken bilgisayardaki tüm COM portlarını tarar. Bazı cihazlar bu işlemi yavaşlatabilir.

**Çözüm:**
Cihaz Yöneticisi'nden COM portu oluşturan cihazları (Bluetooth cihazları gibi) geçici olarak devre dışı bırak.

### Mac'te gnu.io.PortInUseException hatası alıyorum

Bu hata portu başka bir uygulamanın kullandığını gösterir.

```
Error inside Serial.<init>()
gnu.io.PortInUseException: Unknown Application
```

**Çözüm:**
* Seri port veya USB port kullanan diğer programları kapat
* Max/MSP gibi programlar kullanmasa da portu açık tutar - uygulamayı tamamen kapat
* Arduino 0004 ve öncesinde `macosx_setup.command` scriptini çalıştır ve bilgisayarı yeniden başlat

### Eski Mac OS X sürümlerde Arduino çalışmıyor

Şöyle bir hata alıyorsan:

```
Link (dyld) error:
dyld: /Applications/arduino-0004/Arduino 04.app/Contents/MacOS/Arduino Undefined symbols:
/Applications/arduino-0004/librxtxSerial.jnilib undefined reference to _printf$LDBL128
```

Mac OS X 10.3.9 veya daha yeni sürüme güncellemen gerekiyor.

### Windows'ta cygwin çakışması

Bilgisayarında cygwin kuruluysa Arduino IDE'de derleme yaparken şöyle bir hata alabilirsin:

```
6 [main] ? (3512) C:\Dev\arduino-0006\tools\avr\bin\avr-gcc.exe: *** fatal error - 
*** system shared memory version mismatch detected
```

**Çözüm:**
* Arduino kullanırken cygwin'in çalışmadığından emin ol
* Arduino klasöründeki `cygwin1.dll` dosyasını sil ve cygwin kurulumundakiyle değiştir (genellikle `c:\cygwin\bin`'de)

---

## Programlama Sorunları

### Programımın boyutunu nasıl küçültebilirim?

ATmega168 çipinde sadece 16 KB program alanı var (2 KB bootloader kullanır).

**Optimizasyon İpuçları:**
* Kayan nokta (float) hesaplamaları yerine tam sayı (integer) matematiği kullan (~2 KB tasarruf)
* Kullanmadığın `#include` kütüphanelerini sil  
* Kodunu kısaltmaya çalış
* Gereksiz değişkenleri sil

### analogWrite() fonksiyonu bazı pinlerde çalışmıyor

ATmega168 işlemcisi sadece belirli pinlerde PWM/analogWrite() destekler: **3, 5, 6, 9, 10, 11**.

Diğer pinlerde analogWrite() kullanırsan:
* 128'den büyük değerler için HIGH (5V) 
* 128'den küçük değerler için LOW (0V) verir

!!! info "Eski Arduino Kartları"
    ATmega8'li eski Arduino kartlarında sadece **9, 10, 11** pinleri PWM destekler.

### Tanımlanmamış fonksiyon/tip hataları alıyorum  

Arduino IDE fonksiyonların prototiplerini otomatik oluşturur, ama bu işlem mükemmel değildir.

**Yaygın Sorunlar:**
* **Özel tip tanımı**: Kendi veri tipini tanımlar ve fonksiyonda kullanırsan prototip tip tanımından önce oluşur
* **İki kelimeli dönüş tipi**: `unsigned int` gibi dönüş tipleri fonksiyon olarak algılanmaz

**Çözüm:** Fonksiyon tanımını kullanımından önce yap veya manuel prototip ekle.

### Geçersiz cihaz imzası hatası alıyorum

Şöyle bir hata alıyorsan:
```
avrdude: Yikes!  Invalid device signature.
Double check connections and try again, or use -F to override this check.
```

**Olası Nedenler:**
* Yanlış kart seçimi (`Araçlar > Kart`)
* Yanlış avrdude sürümü

Arduino'nun özel avrdude sürümünü kullandığından emin ol. Standart avrdude bootloader'ı tanımaz.

---

!!! success "Sorunun Çözülmedi mi?"
    Bu rehberdeki çözümler sorunu gidermediyse:
    
    * Yerel maker topluluklarından yardım iste
    * Arduino deneyimi olan arkadaşlarından destek al
    * Teknoloji forumlarında Türkçe Arduino gruplarını ara
    * Elektronik malzeme satıcılarından teknik destek al
    
    Problem bildirirken bu bilgileri paylaşmayı unutma:
    - İşletim sistemin
    - Arduino kart modelın  
    - Tam hata mesajı
    - Denediğin çözümler