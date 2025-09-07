# Güç Verme

## Giriş

Arduino 01 kitine güç vermek için birden fazla metod var. Bu rehberde farklı güç verme yöntemlerini ve her birinin avantajlarını öğreneceksin. Kitinin çalışması için 5V gerilim ve minimum 500mA akıma ihtiyacı var.

## Bilgisayar ile Güç Verme

<img src="../images/power/power1.png" alt="Bilgisayar USB bağlantısı" style="height: 300px;">

Kiti programlarken bilgisayara USB ile bağlandığında bilgisayarın aynı zamanda kite güç verdiğini de göreceksin. Bu yöntem özellikle geliştirme aşamasında çok pratik:

- **Avantajlar**: Program yükleme ve güç verme aynı kablo ile
- **Dezavantajlar**: Bilgisayar başında olmak gerekiyor
- **Kullanım**: Kod geliştirme ve test aşamasında ideal

USB portu üzerinden gelen 5V gerilim, kitinin tüm modüllerini çalıştırmaya yeterli.

## Duvar Adaptörü ile Güç Verme

<img src="../images/power/power2.png" alt="Duvar adaptörü bağlantısı" style="height: 300px;">

Derslerde de öğrendiğin gibi, kite bir kere kodu yükledikten sonra artık bilgisayara ihtiyacın yok. Bu durumda herhangi bir **5V (minimum 500mA)** USB adaptörünü kullanabilirsin:

- **Uygun adaptörler**: Telefon şarj cihazları, tablet adaptörleri
- **Teknik özellikler**: 5V çıkış, minimum 500mA akım
- **Bağlantı**: USB-C kablo ile kit bağlantısı

## Powerbank ile Mobil Güç

<img src="../images/power/power3.png" alt="Powerbank bağlantısı" style="height: 300px;">

Kiti mobil hale getirmenin en kolay yöntemi powerbank kullanmak. Telefon şarj etmek için kullanılan powerbankler bu iş için ideal:

- **Avantajlar**: Taşınabilir, uzun çalışma süresi
- **Kapasite**: 10.000mAh powerbank ile saatlerce çalışma
- **Kullanım alanları**: Dış mekan projeleri, demo sunumları
- **Bağlantı**: USB-C çıkışlı powerbank tercih edin

Powerbankler genellikle 5V/2A çıkış sağladığı için kit için fazlasıyla yeterli güç sunuyor.

## Güç Tüketimi ve Süre Hesabı

Kitinin yaklaşık güç tüketimi:

- **Temel çalışma**: ~100mA
- **Tüm LED'ler açık**: ~200mA  
- **Servo motor aktif**: ~300mA
- **Maksimum tüketim**: ~500mA

Bu bilgileri kullanarak elindeki powerbank ile yaklaşık ne kadar süre çalışabileceğini hesaplayabilirsin.

**Temel Formül**: Çalışma Süresi = Powerbank Kapasitesi ÷ Kit Tüketimi

**Örnekler**:

- **Temel kullanım**: 10.000mAh ÷ 100mA = **100 saat**
- **LED'ler açık**: 10.000mAh ÷ 200mA = **50 saat** 
- **Maksimum kullanım**: 10.000mAh ÷ 500mA = **20 saat**

!!! tip "Pratik İpucu"
    Gerçekte hesapladığından biraz daha kısa süre çalışır. Bu normal!