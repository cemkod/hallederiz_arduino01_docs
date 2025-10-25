# macOS Sisteminde Arduino IDE Kurulumu

## Giriş

Bu rehberde macOS bilgisayarında Arduino IDE'yi nasıl kuracağını öğreneceksin. Arduino IDE, Arduino kartlarına kod yüklemek ve onları programlamak için kullandığımız özel bir yazılım. Mac'te kurulum çok basit - sadece indirdiğin dosyayı Applications klasörüne sürüklemen yeterli!

macOS'te program kurma işi genellikle çok kolay. App Store'dan uygulama indirmek gibi düşünebilirsin, ama bu sefer Arduino'nun resmi sitesinden indireceğiz.

## ADIM 1: Arduino IDE'yi İndirme

İlk olarak Arduino'nun resmi sitesine girmen gerekiyor. Bunun için aşağıdaki adrese git:

**<a href="https://www.arduino.cc/en/software/" target="_blank">https://www.arduino.cc/en/software/</a>**

Bu sayfa açıldığında karşına Arduino IDE'nin en güncel sürümü çıkacak. Sayfada **"DOWNLOAD"** yazan büyük yeşil butona tıkla.

<img src="../images/mac-install/01-arduino-indirme-sayfasi.png" alt="Arduino indirme sayfası">

Sistem otomatik olarak macOS bilgisayarın için uygun sürümü seçecek. Açılır menüden "macOS" seçildiğinden emin ol. Intel Mac mi Apple Silicon (M1/M2) Mac mi kullanıyorsan ona göre doğru sürümü seçecek.

## ADIM 2: İndirilen Dosyayı Bulma ve Açma

İndirme işlemi tamamlandıktan sonra dosyayı bulmak gerekiyor. İndirdiğin dosya büyük olasılıkla **Downloads** klasöründe olacak. Safari kullanıyorsan tarayıcının sağ üst köşesindeki indirme simgesinden de dosyaya ulaşabilirsin.

Dosya adı **"arduino-ide_2.3.6_macOS_64bit.dmg"** gibi bir şey olacak. Bu .dmg dosyasını **çift tıklayarak** açman gerekiyor. Çift tıkladığında disk image'ı mount edilecek ve kurulum penceresi açılacak.

## ADIM 3: Arduino IDE'yi Applications Klasörüne Kurma

.dmg dosyasını açtıktan sonra karşına çok basit bir kurulum penceresi çıkacak. Bu pencerede Arduino IDE simgesini göreyecesin ve yanında da Applications klasörü simgesi olacak.

<img src="../images/mac-install/03-macos-drag-drop-kurulum.png" alt="macOS drag-drop kurulumu">

Yapman gereken tek şey:

1. **Arduino IDE simgesini tutup sürükle**
2. **Applications klasörü simgesinin üzerine bırak**
3. **Kopyalama işleminin bitmesini bekle**

Bu işlem birkaç saniye sürer. İşlem bittiğinde Arduino IDE artık bilgisayarında kurulu olacak.

## ADIM 4: Arduino IDE'yi İlk Açış

Kurulum bittikten sonra Arduino IDE'yi açmak için birkaç yol var:

1. **Launchpad'den**: Launchpad'i aç ve Arduino IDE simgesini bul, tıkla
2. **Applications klasöründen**: Finder'da Applications klasörüne git ve Arduino IDE'yi bul
3. **Spotlight'tan**: Cmd+Space tuşlarına basıp "Arduino" yaz ve Enter'a bas

<img src="../images/win-install/04-arduino-ide-arayuz.png" alt="Arduino IDE arayüzü">

Program ilk açıldığında macOS seni uyarabilir: "Arduino IDE is an app downloaded from the Internet. Are you sure you want to open it?" Bu normal bir güvenlik uyarısı. **"Open"** butonuna tıklaman yeterli.

İlk açılışta boş bir kod şablonu göreceksin. Bu normal - Arduino her zaman `setup()` ve `loop()` fonksiyonları ile başlar.

## ADIM 5: Dil Ayarını Türkçeye Çevirme

Arduino IDE varsayılan olarak İngilizce açılır. Eğer Türkçe kullanmak istersen aşağıdaki adımları takip et:

1. **Menüden "Arduino IDE" > "Settings"** (veya "File" > "Preferences") seçeneğine tıkla
2. **Açılan pencerede "Language" (Dil) bölümünü bul**
3. **Açılır menüden "Türkçe"yi seç**

<img src="../images/win-install/05-dil-ayarlari-menusu.png" alt="Dil ayarları menüsü">

4. **"OK" butonuna bas**
5. **Arduino IDE'yi yeniden başlat** - Program kapanıp açıldıktan sonra Türkçe menülerle karşılaşacaksın.

## Kurulum Tamamlandı! 

Tebrikler! Arduino IDE'yi başarıyla kurdun. Mac'te kurulum gerçekten çok basitti - sadece sürükle ve bırak! Artık Arduino projelerine başlayabilirsin. Program hazır ve kullanıma uygun durumda.

Kurulumla ilgili herhangi bir sorun yaşarsan, **Sorun Giderme** bölümüne göz atabilir veya **Linux kurulum** sayfasındaki genel ipuçlarını kullanabilirsin.

