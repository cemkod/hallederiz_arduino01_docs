# Windows Sisteminde Arduino IDE Kurulumu

## Giriş

Bu rehberde Windows bilgisayarında Arduino IDE'yi nasıl kuracağını öğreneceksin. Arduino IDE, Arduino kartlarına kod yüklemek ve onları programlamak için kullandığımız özel bir yazılım. Bilgisayarına bu programı kurduktan sonra Arduino projelerine başlayabilirsin.

## ADIM 1: Arduino IDE'yi İndirme

İlk olarak Arduino'nun resmi sitesine girmen gerekiyor. Bunun için aşağıdaki adrese git:

**<a href="https://www.arduino.cc/en/software/" target="_blank">https://www.arduino.cc/en/software/</a>**

Bu sayfa açıldığında karşına Arduino IDE'nin en güncel sürümü çıkacak. Sayfada **"DOWNLOAD"** yazan büyük yeşil butona tıkla.

<img src="../images/win-install/01-arduino-indirme-sayfasi.png" alt="Arduino indirme sayfası" style="max-width: 100%; height: auto;">

Sistem otomatik olarak Windows bilgisayarın için uygun sürümü seçecek. Eğer farklı bir sürüm istiyorsan açılır menüden değiştirebilirsin, ama normalde otomatik seçimi kullanman yeterli.

## ADIM 2: İndirilen Dosyayı Bulma ve Açma

İndirme işlemi tamamlandıktan sonra dosyayı bulmak gerekiyor. İndirdiğin dosya büyük olasılıkla **İndirilenler (Downloads)** klasöründe olacak. Tarayıcının indirilen dosyalar menüsünden de bu dosyaya ulaşabilirsin.

<img src="../images/win-install/02-indirilen-kurulum-dosyasi.png" alt="İndirilen kurulum dosyası" style="max-width: 100%; height: auto;">

Dosya adı **"arduino-ide_2.3.6_Windows_64bit.exe"** gibi bir şey olacak. Bu exe dosyasını **çift tıklayarak** açman gerekiyor. Çift tıkladığında Arduino kurulum programı başlayacak.

## ADIM 3: Arduino IDE'yi Kurma

Kurulum dosyasını açtıktan sonra kurulum sihirbazı (wizard) başlayacak. Bu program seni adım adım yönlendirecek.

<img src="../images/win-install/03-kurulum-konum-secimi.png" alt="Kurulum konum seçimi" style="max-width: 100%; height: auto;">

Kurulum programında:

1. **Kurulum konumu**: Program nereye kurulacağını soracak. Varsayılan konum genellikle uygundur, değiştirmene gerek yok.

2. **"Install" (Kur) butonuna tıkla**: Kurulum başlatmak için mavi **"Install"** butonuna bas.

3. **Bekle**: Kurulum birkaç dakika sürebilir. Kurulum çubuğu dolana kadar beklemen yeterli.

Kurulum tamamlandığında **"Finish"** (Son) butonuna basarak kurulumu bitirebilirsin.

## ADIM 4: Arduino IDE'yi İlk Açış

Kurulum bittiğinde Arduino IDE otomatik olarak açılabilir. Eğer açılmazsa, masaüstündeki Arduino simgesine çift tıklayarak veya başlat menüsünden "Arduino IDE" yazarak programı bulup açabilirsin.

<img src="../images/win-install/04-arduino-ide-arayuz.png" alt="Arduino IDE arayüzü" style="max-width: 100%; height: auto;">

Program ilk açıldığında boş bir kod şablonu göreceksin. Bu normal - Arduino her zaman `setup()` ve `loop()` fonksiyonları ile başlar.

## ADIM 5: Dil Ayarını Türkçeye Çevirme

Arduino IDE varsayılan olarak İngilizce açılır. Eğer Türkçe kullanmak istersen aşağıdaki adımları takip et:

1. **Menüden "File" > "Preferences"** (Dosya > Tercihler) seçeneğine tıkla

2. **Açılan pencerede "Language" (Dil) bölümünü bul**

3. **Açılır menüden "Türkçe"yi seç**

<img src="../images/win-install/05-dil-ayarlari-menusu.png" alt="Dil ayarları menüsü" style="max-width: 100%; height: auto;">

4. **"OK" butonuna bas**

5. **Arduino IDE'yi yeniden başlat** - Program kapanıp açıldıktan sonra Türkçe menülerle karşılaşacaksın.

## Kurulum Tamamlandı! 

Tebrikler! Arduino IDE'yi başarıyla kurdun. Artık Arduino projelerine başlayabilirsin. Program hazır ve kullanıma uygun durumda.
