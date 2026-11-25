# Gezinomi: Kural Tabanlı Sınıflandırma

Bu projede Gezinomi'nin satış verisi üzerinden kural tabanlı sınıflandırma ile potansiyel müşteri getirisi hesaplamaya çalıştım. Miuul bootcamp'i kapsamında verilen bir görevdi. Soru şuydu: şehir, otel konsepti ve sezon bilgisine bakarak yeni gelen bir müşterinin şirkete ortalama ne kadar kazandıracağını tahmin edebilir miyiz? Örneğin yoğun sezonda Antalya'da herşey dahil bir otele gitmek isteyen biri hangi segmente düşer?

Veri `miuul_gezinomi.xlsx` dosyasında, bootcamp tarafından sağlandı. 59.164 satış ve 9 değişken var (satış tarihi, giriş tarihi, fiyat, konsept, şehir, giriş günü, satış ile giriş arasındaki gün farkı, sezon). Satışların büyük kısmı Antalya'da (31.649) ve konseptlerde de "Herşey Dahil" baskın (53.186).

## Yaklaşım

Önce şehir ve konsept bazında satış sayılarına, toplam ve ortalama fiyatlara baktım. Ardından satış ile otele giriş arasındaki gün farkını `EB_Score` adında kategorik bir değişkene çevirdim (0-7 gün Last Minuters, 8-30 Potential Planners, 31-90 Planners, 90 üstü Early Bookers) ve fiyat ortalamalarını şehir, konsept ile bu değişken, sezon ve giriş günü kırılımlarında inceledim.

Asıl sınıflandırma şehir, konsept ve sezon üçlüsü üzerinden. Her kombinasyon için ortalama fiyatı hesaplayıp `ANTALYA_HERŞEY DAHIL_HIGH` gibi bir satış tanımı oluşturdum, sonra bu ortalamaları `qcut` ile dört çeyreğe bölüp D'den A'ya segmentler atadım.

## Sonuçlar

Toplam 36 satış tanımı çıktı. Segmentlerin ortalama fiyatları şöyle:

```
SEGMENT  Ortalama  Maksimum
D           33.37     39.48
C           44.89     54.14
B           60.27     64.92
A           82.47    103.94
```

En yüksek ortalama Girne herşey dahil yoğun sezonda (103.94). Örnek soruya dönersek, `ANTALYA_HERŞEY DAHIL_HIGH` tanımı B segmentine düşüyor ve beklenen ortalama getiri 64.92.

Benim için en öğretici kısım, bu kadar basit bir kuralın bile işe yarar bir sezgi verebilmesi ama aynı zamanda nerede yanıltabileceğini görmek oldu. Segmentler grup ortalamalarının çeyreklerine göre belirleniyor, gruplardaki satış sayısı hesaba katılmıyor. Antalya'daki 27 bin satırlık bir grupla Girne'deki birkaç yüz satırlık bir grup aynı ağırlıkta. Ayrıca yalnızca ortalamaya bakıldığı için grup içindeki fiyat dağılımı hakkında bir şey söylenmiyor.

## Çalıştırmak için

```bash
git clone https://github.com/Memreu/Rule-Based-Classification.git
cd Rule-Based-Classification
pip install -r requirements.txt
jupyter notebook Rule-Based-Classification.ipynb
```

Bir sonraki adım olarak segmentleri satış sayısıyla ağırlıklandırmayı ve az gözlemli grupları birleştirmeyi denemek isterim. `EB_Score` değişkenini satış tanımına eklemek de segmentleri daha anlamlı hale getirebilir.
