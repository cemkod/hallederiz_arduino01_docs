# Sorun Giderme Rehberi

## Giriş

Halledersiniz Arduino 01 ile çalışırken karşılaştığın sorunlar için hazırlanmış bu rehber, en yaygın problemlerin çözümlerini içeriyor. Kite program yüklemekten, kod hatalarına kadar birçok soruna çözüm bulabilirsin. 

Bu rehberdeki çözümleri sırayla deneyerek problemini giderebilirsin. Eğer sorunun devam ederse, bize mail atabilirsin.

---

## Program Yükleme Sorunları

### Arduino kartıma program yükleyemiyorum

Arduino 01 kitine program yüklemek için birçok bileşenin doğru çalışması gerekiyor. Bunlar arasında sürücüler, Arduino IDE'deki kart ve port ayarları, fiziksel bağlantı, bootloader ve daha pek çok şey bulunuyor. Aşağıdaki adımları sırayla takip ederek sorununu çözebilirsin.

#### Arduino IDE Ayarları

**1. Kart Seçimi**
`Araçlar > Kart` menüsünden doğru kart türünü seçtiğinden emin ol. Arduino 01 kitini programlamak için bu menüde "Arduino Uno"'yu seçmen gerekiyor. 

**2. Port Seçimi**  
`Araçlar > Port` menüsünden doğru portu seçtiğinden emin ol. Port görünmüyorsa, Arduino kartı bağlıyken Arduino IDE programını kapatıp ve yeniden açmayı dene.

* **Windows'ta**: COM3, COM4 gibi bir port olacak. Doğru portu bulmak için Windows Ayarlarında Cihaz Yöneticisi > Portlar kısmına bakabilirsin.
* **Mac'te**: `/dev/tty.usbmodem621` gibi görünecek.
* **Linux'te**: `/dev/ttyACM0` şeklinde olacak.

!!! tip "Port görünmüyorsa"
    Eğer kartın için uygun port görünmüyorsa, sürücü sorunu olabilir. Aşağıdaki "Sürücüler" bölümüne bak.

#### Sürücüler

Sürücüler, bilgisayarındaki yazılımın (Arduino IDE) donanımınla (Arduino kartı) iletişim kurmasını sağlar.
Sürücülerin doğru kurulu olup olmadığını kontrol etmenin en kolay yolu Arduino kartı bağlıyken Arduino IDE'de`Araçlar > Port` menüsüne bakmaktır. Kart bağlıyken bu menüde fazladan seçenek görünmeli. Eğer görünmüyorsa sürücü ile ilgili bir sorun yaşıyor olabilirsin.
Arduino 01 kiti CH340 isminde bir iletişim çipi kullanmakta ve bu çipin sürücülerine ihtiyac duymaktadır. Windows 10 ve 11'de bu sürücü otomatik olarak kurulmaktadır. 

**Linux'te Sürücü Sorunları:**

Arduino 01 kiti Linux'te `/dev/ttyACM0` şeklinde görünür. Standart RXTX kütüphanesi bu cihazları desteklemez, ama Arduino yazılımıyla gelen özel RXTX sürümü destekler. 

Eğer kendi RXTX pakettini kullanıyorsan, şu komutları çalıştır:

```bash
sudo usermod -a -G tty kullaniciAdin
sudo usermod -a -G dialout kullaniciAdin
```

Sonra çıkış yap ve tekrar gir.

#### Seri Port Erişimi

**Windows'ta:**
* IDE yavaş başlıyorsa veya Araçlar menüsü açılmıyorsa, Windows Cihaz Yöneticisi'nden Bluetooth seri portlarını devre dışı bırak.
* USB Cellular Wifi yazılımları, PDA senkron programları, Bluetooth sürücüleri gibi tüm seri portları tarayan programları kapat.
* ZoneAlarm gibi güvenlik duvarlarının seri portu engellemediğinden emin ol.

**Tüm Sistemler:**
* Processing, PD, vvvv gibi programlar Arduino ile seri bağlantı kullanıyorsa kapat.
* Linux'te geçici olarak Arduino IDE'yi root olarak çalıştırmayı dene.

#### Fiziksel Bağlantı

* Kartın bilgisayara bağlı olduğundan ve güç aldığından emin ol. Karta güç verdiğinde ortasındaki beyaz soketlerin üstündeki PWR yazılı kırmızı ledin yanması gerekmekte.
* Arduino 01 bazı USB hublar üzerinden bağlantı sorunu yaşayabilir. Kartı doğrudan bilgisayara bağla ve Arduino IDE'yi yeniden başlat.
* Farklı bir USB kablosu dene. Bazen USB kabloları yeterli akımı veremeyebilir ya da zarar görmüş olabilir. Farklı USB Type-C kabloları ile dene.

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

---

## Bağlantı ve Donanım Sorunları

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

Arduino01 kitindeki Atmega328 çipinde 32 KB program alanı var. Eğer daha büyük bir program yazarsan bunu kite yükleyemezsin.

**Optimizasyon İpuçları:**
* Kayan nokta (float) hesaplamaları yerine tam sayı (integer) matematiği kullan (~2 KB tasarruf)
* Kullanmadığın `#include` kütüphanelerini sil  
* Kodunu kısaltmaya çalış
* Gereksiz değişkenleri sil

### analogWrite() fonksiyonu bazı pinlerde çalışmıyor

ATmega328 işlemcisi sadece belirli pinlerde PWM/analogWrite() destekler: **3, 5, 6, 9, 10, 11**.

Diğer pinlerde analogWrite() kullanırsan:
* 128'den büyük değerler için HIGH (5V) 
* 128'den küçük değerler için LOW (0V) verir


### Geçersiz cihaz imzası hatası alıyorum

Şöyle bir hata alıyorsan:
```
avrdude: Yikes!  Invalid device signature.
Double check connections and try again, or use -F to override this check.
```

**Olası Nedenler:**
* Yanlış kart seçimi (`Araçlar > Kart`)
* Yanlış avrdude sürümü

Kart seçiminde Arduino UNO seçtiğine ve arduino'nun özel avrdude sürümünü kullandığından emin ol. 

---

!!! success "Sorunun Çözülmedi mi?"
    Bu rehberdeki çözümler sorunu gidermediyse:
    
    ogren@halledersiniz.com adresine mail at sana yardım edelim.
    
    Problem bildirirken bu bilgileri paylaşmayı unutma:
    - İşletim sistemin 
    - Tam hata mesajı
    - Denediğin çözümler