# K-Nearest-Neighbors

## Dataset'i indirin

Dataset [burada](https://d32aokrjazspmn.cloudfront.net/materials/ML_Houses_clean.csv) mevcuttur. Aşağıdaki komutlarla indirip `01-KNN` dizinindeki `data` klasörüne kaydedelim:

``` bash
curl https://d32aokrjazspmn.cloudfront.net/materials/ML_Houses_clean.csv > data/houses_clean.csv
```

## Dataset

- Dataset, Houses dataset'inin özelliklerinden bir seçimdir
- Çoğu özellik zaten önceden işlenmiştir
- Hedef, evlerin satış fiyatıdır

## Alıştırma

Bu alıştırma KNN algoritmasını ve mekaniklerini parçalara ayırır. Şunları yapacaksınız:

- Ölçeğe duyarlılığını keşfedin
- Kasıtlı olarak overfitting yaptırın
- K parametresini ince ayarlayın

Son olarak, performansını Linear Regression ile karşılaştıracak ve göreve en uygun modeli seçeceksiniz.

Alıştırmaya başlamak için `jupyter notebook`'ta `KNN.ipynb` dosyasını açın ve talimatları takip edin.

🚀 Sıra sizde!

## Sonuçlar ve Yorum

Dataset: 1460 satır, 5 feature (`GrLivArea`, `BedroomAbvGr`, `KitchenAbvGr`,
`OverallCond`, `CentralAir`), hedef `SalePrice`. Tüm skorlar 5-fold
cross-validation ortalamasıdır.

### 1. Ölçeğe duyarlılık

| Model | R² |
|---|---|
| Varsayılan KNN (k=5), ölçeklenmemiş | 0.6084 |
| Varsayılan KNN (k=5), MinMaxScaler | 0.6499 |
| Ayarlanmış KNN (k=11), MinMaxScaler | 0.6572 |

Dört feature zaten [0, 1] aralığındaydı; `GrLivArea` ise RobustScaler
çıktısı olarak [-2.26, 6.46] aralığındaydı — yaklaşık 8.7 birimlik bir
genişlik. Öklid mesafesi hesaplanırken bu tek feature diğer dördünü
bastırıyor, model fiilen tek boyutlu bir KNN'e dönüşüyordu.

MinMaxScaler ile tüm feature'lar ortak [0, 1] aralığına çekildiğinde R²
**+0.0415** arttı. Karşılaştırma için: k'yı 5'ten 11'e ayarlamanın katkısı
sadece **+0.0072**. Yani bu veri setinde ölçekleme, hiperparametre
ayarından yaklaşık altı kat daha belirleyici.

### 2. Overfitting ve k seçimi

k = 1..25 aralığında taranan skor eğrisinde maksimum **k = 11**'de bulundu
(R² = 0.6572).

k=2 ile çizilen learning curve'de train skoru yüksek, test skoru belirgin
şekilde düşük ve aradaki boşluk örneklem büyüdükçe kapanmıyor — klasik
overfitting. Çok küçük k'da model her tahminini birkaç komşuya dayandırıyor,
bu da eğitim verisindeki gürültüyü ezberlemek anlamına geliyor. k=11'in
learning curve'ünde iki eğri belirgin şekilde yakınsıyor.

### 3. KNN vs Linear Regression

| Model | R² | MAE ($) |
|---|---|---|
| KNN (k=11) | 0.6572 | 30.824 |
| Linear Regression | 0.5945 | 33.586 |

KNN her iki metrikte de kazanıyor, dolayısıyla sonuç metrik seçimine bağlı
değil. Muhtemel neden: KNN, `SalePrice`'ın feature'larla ilişkisindeki
doğrusal olmayan yapıyı (örneğin yaşam alanı arttıkça fiyatın hızlanan
artışı) yakalayabiliyor, Linear Regression ise tek bir katsayıya sıkışıyor.

**Seçilen model: KNN.**

### 4. Sınırlar

MAE ≈ 30.800 $, medyan satış fiyatının (163.000 $) yaklaşık **%19'u**.
Beş feature'lık bir alıştırma modeli için beklenen bir seviye, ancak gerçek
bir değerleme aracı olarak kullanılamaz. R² = 0.66, fiyat varyansının
üçte birinden fazlasının bu feature'larla açıklanamadığı anlamına geliyor.
