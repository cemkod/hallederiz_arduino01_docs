# Linux Sisteminde Arduino IDE Kurulumu

## Giriş

Bu rehberde Linux bilgisayarında Arduino IDE'yi nasıl kuracağını öğreneceksin. Linux'te Arduino kurmanın en kolay yolu **AppImage** kullanmak. AppImage dosyaları tek başına çalışabilir, kurulum gerektirmez - sadece indirip çift tıklaman yeterli!

## ADIM 1: Arduino IDE'yi İndirme

İlk olarak Arduino'nun resmi sitesine girmen gerekiyor. Bunun için aşağıdaki adrese git:

**<a href="https://www.arduino.cc/en/software/" target="_blank">https://www.arduino.cc/en/software/</a>**

Bu sayfa açıldığında karşına Arduino IDE'nin en güncel sürümü çıkacak. Sayfada **"DOWNLOAD"** yazan büyük yeşil butona tıkla.

<img src="../images/linux-install/01-arduino-indirme-sayfasi.png" alt="Arduino indirme sayfası" style="max-width: 100%; height: auto;">

Sistem otomatik olarak Linux bilgisayarın için uygun sürümü seçecek. **AppImage** formatını seçtiğinden emin ol - bu en kolay kurulum yöntemi.

## ADIM 2: AppImage ile Kurulum (Önerilen)

AppImage dosyası indirildikten sonra kurulum çok basit:

1. **İndirilen dosyayı bul**: Genellikle Downloads klasöründe **arduino-ide_2.3.6_Linux_64bit.AppImage** gibi bir isimde olacak.

2. **Dosyayı çalıştırılabilir yap**:
   - Dosyaya sağ tıkla
   - "Properties" (Özellikler) seçeneğine tıkla  
   - "Permissions" (İzinler) sekmesine git
   - "Execute" (Çalıştır) kutucuğunu işaretle

   Veya terminal kullanarak:
   ```bash
   chmod +x arduino-ide_2.3.6_Linux_64bit.AppImage
   ```

3. **Dosyayı çift tıklayarak çalıştır**: AppImage otomatik olarak Arduino IDE'yi başlatacak.

4. **İsteğe bağlı - Uygulama menüsüne ekle**: İlk çalıştırdığında "Would you like to integrate this AppImage?" diye soracak. "Yes" dersen uygulama menüsüne eklenir.

## Alternatif: Manuel Kurulum

Eğer AppImage çalışmazsa, geleneksel yöntemle kurabilirsin:

1. **Tar dosyasını indir**: Download sayfasından "Linux 64 bits" tar.xz dosyasını seç

2. **Terminal aç** (`Ctrl+Alt+T`) ve Downloads klasörüne git:
   ```bash
   cd ~/Downloads
   ```

3. **Arşivi çıkar**:
   ```bash
   tar -xf arduino-ide_*_Linux_64bit.tar.xz
   ```

4. **Kurulum scriptini çalıştır**:
   ```bash
   cd arduino-ide_*_Linux_64bit
   sudo ./install.sh
   ```

## ADIM 3: Arduino IDE'yi İlk Açış

AppImage kullandıysan, dosyaya çift tıklayarak programı açabilirsin. Manuel kurulum yaptıysan, uygulama menüsünden "Arduino IDE" aratabilir veya terminal'de `arduino-ide` yazabilirsin.

<img src="../images/win-install/04-arduino-ide-arayuz.png" alt="Arduino IDE arayüzü" style="max-width: 100%; height: auto;">

Program ilk açıldığında boş bir kod şablonu göreceksin. Bu normal - Arduino her zaman `setup()` ve `loop()` fonksiyonları ile başlar.

## ADIM 4: Dil Ayarını Türkçeye Çevirme

Arduino IDE varsayılan olarak İngilizce açılır. Eğer Türkçe kullanmak istersen aşağıdaki adımları takip et:

1. **Menüden "File" > "Preferences"** (Dosya > Tercihler) seçeneğine tıkla

2. **Açılan pencerede "Language" (Dil) bölümünü bul**

3. **Açılır menüden "Türkçe"yi seç**

<img src="../images/win-install/05-dil-ayarlari-menusu.png" alt="Dil ayarları menüsü" style="max-width: 100%; height: auto;">

4. **"OK" butonuna bas**

5. **Arduino IDE'yi yeniden başlat** - Program kapanıp açıldıktan sonra Türkçe menülerle karşılaşacaksın.

## Kurulum Tamamlandı!

Tebrikler! Arduino IDE'yi başarıyla kurdun. AppImage yöntemi gerçekten çok basitti - kurulum bile gerekmiyor! Artık Arduino projelerine başlayabilirsin.


