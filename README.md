# **SpectraHb-PoC**

Makine öğrenmesi ve yedi farklı dalga boyu kullanan cilt tonu önyargılarına duyarlı invaziv olmayan anemi tarama cihazı ve sınıflandırma modeli.

## **Proje Verileri**

Proje kapsamındaki tüm çalışma verileri test kayıtları ve makine öğrenmesi eğitim setleri **datas** klasörü içerisinde yer almaktadır. Geliştirme sürecinde kullanılan ham sinyal örneklerine bu klasörden doğrudan ulaşabilirsiniz.

## **Cihaz Tasarımı ve Bileşen Seçimi**

Cihazın donanım mimarisi optik tutarlılığı artırmak ve veri kalitesini en üst düzeye çıkarmak üzere özel olarak tasarlanmıştır. Sistemde kullanılan LED dalga boyları rastgele seçilmemiş olup her birinin spesifik bir bilimsel rolü bulunmaktadır.

660 nanometre dalga boyu kontrol ve karşılaştırma kanalı olarak işlev görür. Klasik nabız oksimetrelerinde sıkça kullanılan bu dalga boyu melanin pigmentinden en fazla etkilenen kanaldır. Cilt tonu etkisini modelin referans olarak görebilmesi adına sisteme dahil edilmiştir.

850 nanometre dalga boyu ana sinyal kanalıdır. Literatürde sinyal gürültü oranının en iyi olduğu bölgedir ve mevcut akademik veri setleri ile doğrudan karşılaştırma yapabilmemize olanak tanır.

940 nanometre dalga boyu ise cilt tonuna en dirençli kanaldır. Işığın doku içerisindeki serbest yolu bu dalga boyunda maksimum seviyeye ulaşır ve melanin kaynaklı ölçüm hatalarından en az etkilenen tarafsız bölgeyi oluşturur.

Optik sinyallerin toplanması aşamasında BPW34 fotodiyot ve ESP32 mikrodenetleyicisi kullanılmaktadır. LED sürücü entegreleri ile çoğullama yapılırken tüm ışık kaynakları fiber optik kılavuz yardımıyla tek bir noktadan dokuya iletilir. Işık yolu uzunluğunun tüm dalga boyları için eşit kalması bu sayede garanti altına alınır. Ayrıca ölçüm hatalarını azaltmak için cihaza temas basıncını denetleyen bir basınç sensörü eklenmiştir.

## **Ortam Işığı Kontrol Stratejileri**

Kontrolsüz bırakılmış dış ortam ışığı sinyalde ciddi sapmalara yol açarak doğruluğu düşürür. Bu sızıntıyı engellemek için cihazda hem pasif izolasyon hem de aktif yazılımsal kompanzasyon uygulanmaktadır.

Pasif strateji kapsamında cihazın dış gövdesi kapalı kasa olarak tasarlanmış, parmak temas yüzeyine ışık sızdırmaz conta eklenmiş ve iç yüzey mat malzeme ile kaplanmıştır.

Aktif strateji ise elektronik ve yazılım seviyesinde çalışır. Çoğullama işlemi sırasında LED kaynakları sırayla yakılırken aralarda tüm ışıkların kapalı tutulduğu karanlık pencereler bırakılır. Bu pencerelerde ölçülen salt ortam ışığı değeri aktif aydınlatma anında okunan değerlerden çıkarılarak kusursuz karanlık çerçeve kompanzasyonu sağlanır.

graph TD  
    subgraph Optik\_Kaynaklar  
        L1\[660 nm \- Cilt Tonu Referans Kanalı\]  
        L2\[850 nm \- Ana Sinyal Kanalı\]  
        L3\[940 nm \- Cilt Tonuna Dirençli Kanal\]  
    end

    subgraph Fiziksel\_Tasarim\_ve\_İzolasyon  
        FO\[Fiber Optik Kılavuz ile Tek Noktadan İletim\]  
        PK\[Pasif Kalkan \- Mat Kaplama ve Işık Contası\]  
        BS\[Basınç Sensörü \- Temas Kalitesi Ölçümü\]  
        PD\[BPW34 Fotodiyot Sinyal Algılama\]  
    end

    subgraph Aktif\_Kompanzasyon\_ve\_Sayisallastirma  
        MUX\[Zaman Paylaşımlı Çoğullama ve LED Sürüşü\]  
        DK\[Karanlık Pencere \- Ortam Işığı Ölçümü\]  
        FARK\[Saf Sinyal \- Çıkarma İşlemi\]  
        ADC\[ESP32 Yüksek Hızlı Sayısallaştırma\]  
    end

    Optik\_Kaynaklar \--\> FO  
    FO \--\> PK  
    PK \--\> PD  
    BS \-. Basınç Verisi .-\> ADC  
    PD \--\> MUX  
    MUX \--\> DK  
    DK \--\> FARK  
    FARK \--\> ADC  
    ADC \--\> SONUC\[Çoklu Dalga Boyu Net Sinyal Paketi\]  

