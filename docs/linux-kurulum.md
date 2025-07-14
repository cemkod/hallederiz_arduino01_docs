## Linux Sisteminde Arduino'yu İndirme

**ADIM 1:**

Arduino resmi sitesine gir ve Arduino'yu indirmeye başla. Bunun için 'SOFTWARE' (YAZILIM) kısmına tıklaman gerekiyor. Arduino'nun resmi sitesi şu adreste:  
[https://www.arduino.cc/](https://www.arduino.cc/)

**ADIM 2:**

Bilgisayarına uygun Linux sürümünü seç ve indir.

**ADIM 3:**

Arduino'yu indirmek için 'DOWNLOAD' kısmına tıklayın.
'JUST DOWNLOAD' (SADECE İNDİR) butonuna tıkladıktan sonra, dosyayı nereye kaydetmek istediğini seç ve indirme işlemini başlat.

**ADIM 4:**

Arduino IDE Kurulumu
Artık Arduino IDE'yi kurmaya hazırsın!

1. İndirdiğin Arduino arşiv dosyasını kaydettiğin klasöre git  
(genellikle burası: `/home/[Kullanıcı-Adı]/Downloads`).
2. İndirdiğin Arduino arşivini çıkarman gerekecek. Bunu yapmak için:
   - `Ctrl+Alt+T` kısayoluyla terminali aç
   - Aşağıdaki komutu çalıştırarak Downloads klasörüne geç:  
     ```cd /home/[kullanıcı adı]/Downloads```

   - Arşiv klasörünü çıkarmak için terminalde şu komutu çalıştır:  
     ```
     tar xf [Sıkıştırılmış-dosya-adı]
     ```
     
3. Terminalde aşağıdaki komutu çalıştırarak yeni çıkarılan Arduino klasörüne git:  
   ```
   cd [Çıkarılan-klasör-adı]
   ```

4. Terminalde `ls -l` komutunu çalıştırarak Arduino klasöründeki dosya listesini görebilir ve kuracağımız `install.sh` kurulum scriptini bulabilirsin.

Şimdi Arduino'yu kurmaya hazırız! Arduino'yu kurmak için terminalde aşağıdaki komutu sudo yetkisiyle çalıştır:  
  ```
  sudo ./install.sh
  ```

Kurulum tamamlanana kadar biraz beklemen gerekecek.

