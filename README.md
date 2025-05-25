# mertkayahan-repo
 Akbank Makine Öğrenmesine Giriş Bootcamp Final Projesi
 
# Heart Disease Prediction

## İçindekiler
- [Kaggle Linki](#kaggle-linki)  
- [Proje Özeti](#proje-özeti)  
- [Veri Seti](#veri-seti)  
- [Veri Ön İşleme](#veri-ön-işleme)  
- [Keşifsel Veri Analizi (EDA)](#keşifsel-veri-analizi-eda)  
- [Dengesiz Veriyle Mücadele](#dengesiz-veriyle-mücadele)  
- [Model Eğitimi ve Karşılaştırma](#model-eğitimi-ve-karşılaştırma)  
- [Seçilen Model ve Sonuçlar](#seçilen-model-ve-sonuçlar)  
- [Gerçek Dünyada Kullanım Senaryoları](#gerçek-dünyada-kullanım-senaryoları)  
- [Gelecek Geliştirmeler](#gelecek-geliştirmeler)  
 

---

## Kaggle Linki
https://www.kaggle.com/code/mertkayahan/supervised

## Proje Özeti
Bu projede, **“Heart Disease”** veri setini kullanarak bireylerin kalp hastalığı geçirme riskini tahmin ediyorum. Amacım:

1. Verideki kategorik ve sayısal özellikleri doğru şekilde işleyip analiz etmek.  
2. Sınıflandırma modelleriyle “hasta”/“sağlıklı” ayrımını yapacak en başarılı yöntemi bulmak.  
3. Dengesiz veri (hasta vakaları azınlıkta) sorununu giderip, nadir vakaları kaçırmayan bir model kurmak.  
4. Gerçek hayatta erken uyarı sistemi olarak kullanılabilecek, yorumlanabilir ve güvenilir bir model oluşturmak.

---

## Veri Seti
- **Kaynak**: “Heart Disease.csv” (BRFSS 2020 verisi)  
- **Özellikler**:  
  - Sayısal: `BMI`, `PhysicalHealth`, `MentalHealth`, `SleepTime`  
  - Kategorik: `Smoking`, `AlcoholDrinking`, `Stroke`, `DiffWalking`, `Sex`, `AgeCategory`, `Race`, `Diabetic`, `PhysicalActivity`, `GenHealth`, `Asthma`, `KidneyDisease`, `SkinCancer`  
- **Boyut**: 319.795 örnek × 18 sütun  
- **Eksik veri**: Yok  

---

## Veri Ön İşleme
1. **Uç değer temizliği** (IQR yöntemi):  
   - `BMI`, `PhysicalHealth`, `MentalHealth`, `SleepTime`’ın uç değerlerini kaldırdım.  
2. **Kategorik dönüşümler**:  
   - İkili “Yes/No” sütunlarına One-Hot Encoding.  
   - `AgeCategory`’yi sayısal yaş değerine map ederek tek bir sütun haline getirdim.  
   - `PhysicalHealth`, `MentalHealth`, `GenHealth`, `SleepTime` için anlamlı sıralar oluşturarak Ordinal Encoding uyguladım.  
   - `Race` için One-Hot Encoding (sırasız, eşit ağırlıklı işlemek için).  
3. **Ölçekleme**:  
   - `BMI` ve `AgeCategory`’yi `StandardScaler` ile ortalaması 0, standart sapması 1 olacak şekilde standartlaştırdım.

---

## Keşifsel Veri Analizi (EDA)
- **BMI**: Çoğunluk 20–35 aralığında; uç değer (≥55) kayıtları temizlendi.  
- **PhysicalHealth & MentalHealth**:  
  - Çoğu katılımcı 0 gün şikayet bildirmiş.  
  - “8+ days” ve “25–30 days” grupları risk artışının en belirgin olduğu aşamalar.  
- **SleepTime**:  
  - Ortalama ~7 saat; “5 saat altı” ve “9+ saat” grupları daha yüksek risk gösterdi.  
- **GenHealth**:  
  - “Poor” grubu %35 riskle en yüksek, “Excellent” %2 riskle en düşük seviyede.  

Grafikler ve barplot’larla her kategoride kalp hastalığı oranını analiz ederek uygun encoding stratejileri belirledim.

---

## Dengesiz Veriyle Mücadele
- Eğitim seti: `train_test_split(stratify=y)` ile %70–30 oranında bölündü.  
- Doğrudan sınıf oranı:
False (sağlıklı): ~198.246
True (hasta) : ~ 18.333
- **SMOTE** ile eğitim setini %50–50’e dengeledim:  
False: 198.246 → 198.246
True : 18.333 → 198.246

---

## Model Eğitimi ve Karşılaştırma
Aşağıdaki Dört algoritmayı aynı veri seti üzerinde deneyip karşılaştırdım

1. **Decision Tree**  
   - Basit ve yorumlanabilir bir “temel” (baseline) model sunuyor.  
   - Özelliklerin karar mekanizmasını görselleştirerek veri hikâyesini hızlıca kavramamı sağlıyor.

2. **Random Forest**  
   - Birden fazla karar ağacını bir araya getirerek aşırı öğrenmeyi (overfitting) azaltıyor.  
   - Karar ağacının gücünü ansambl hâline getirip genelleme performansını artırıyor.

3. **XGBoost**  
   - Gradyan artırma (gradient boosting) tabanlı, modern ve genellikle tabular veride en iyi sonuç veren yöntemlerden biri.  
   - Ağaç tabanlı modelleri ardışık öğrenme ile optimize ederek doğruluğu daha da yükseltebiliyor.

4. **Balanced Random Forest**  
   - Sınıflar arası dengesizlik problemine özel tasarlanmış bir rastgele orman çeşidi.  
   - Dengesiz veride, azınlık sınıfı (hasta vakaları) görmezden gelmeyip alt-örnekleme yaparak recall’u koruyor.

Bu dört model; **yorumlanabilirlik**, **ensemble gücü**, **boosting başarısı** ve **dengesiz veri stratejisi** açılarından birbirini tamamlıyor. Böylece her birinin güçlü ve zayıf yönlerini görüp, en uygun yaklaşımı keşfedecek zengin bir karşılaştırma yapabildim.  



| Model               | Precision (True) | Recall (True) | F1-Score (True) | Accuracy |
|---------------------|------------------|---------------|-----------------|----------|
| Decision Tree       | 0.548            | 0.047         | 0.086           | 0.916    |
| Random Forest       | 0.628            | 0.038         | 0.072           | 0.917    |
| XGBoost             | 0.522            | 0.107         | 0.177           | 0.916    |
| **Balanced RF**     | **0.226**        | **0.678**     | **0.339**       | 0.776    |

- Diğer algoritmalar, azınlık sınıf (“hasta”) vakalarını neredeyse gözardı ederken  
- **BalancedRandomForestClassifier** en iyi F1-score’u (0.339) ve yüksek recall’u (%67) sağladı.

--- 


## Seçilen Model ve Sonuçlar
- **Model**: Balanced Random Forest  
| n_estimators | max_depth | precision | recall |  f1   |
|-------------:|:----------|----------:|-------:|------:|
|           50 | None      |    0.258  |  0.343 | 0.295 |
|           50 | 10        |    0.227  |  0.772 | 0.350 |
|          100 | None      |    0.260  |  0.350 | 0.298 |
|          100 | 10        |    0.229  |  0.771 | 0.353 |
- En iyi F1 sonucunu n_estimators=100, max_depth=10 ile lede ettim
- **Hiperparametreler**:  
n_estimators=100  
max_depth=10  
random_state=42  
n_jobs=-1 


## Threshold Optimizasyonu
| Thr | Precision | Recall |   F1   |
|-----|-----------|--------|--------|
| 0.1 | 0.096     | 0.994  | 0.174  |
| 0.2 | 0.125     | 0.972  | 0.222  |
| 0.3 | 0.153     | 0.938  | 0.263  |
| 0.4 | 0.181     | 0.888  | 0.300  |
| 0.5 | 0.216     | 0.808  | 0.340  |
| 0.6 | 0.266     | 0.663  | 0.379  |
| 0.7 | 0.337     | 0.481  | 0.397  |
| 0.8 | 0.458     | 0.220  | 0.297  |
| 0.9 | 0.584     | 0.027  | 0.053  |

- Varsayılan eşik (0.5) ile F1 = 0.340  
- Eşik taraması → en iyi F1 = 0.397 @ threshold = 0.7  
- **Nihai Performans (threshold=0.7):**  
  - Precision: **0.337**  
  - Recall:    **0.481**  
  - F1-Score:  **0.397**    

### Seçim Gerekçesi
- Yüksek recall (%50 üzeri) ile **hastaları kaçırmama** önceliği  
- **En yüksek F1** dengesi  

---

## Gerçek Dünyada Kullanım Senaryoları
- **Hastane/klinik ön taraması:**  
  Risk altındaki bireyleri erken tespit edip ileri tetkikleri planlamak.  
- **Sağlık sigortası:**  
  Maliyet optimizasyonu için yüksek riskli müşterilere yönelik önleyici programlar geliştirmek.  
- **Kişisel sağlık uygulamaları:**  
  Kullanıcılara yaşam tarzı önerileri sunarak kalp sağlığı takibi yapmak.  

---

## Gelecek Geliştirmeler
- **Farklı dengeleme yöntemleri**  
  (ör. SMOTE+ENN, Borderline-SMOTE)  
- **Derin öğrenme yaklaşımları**  
  (TabNet, TabTransformer vb.)  
- **Canlı sistem entegrasyonu**  
  - REST API ile gerçek zamanlı tahmin servisleri  


```python