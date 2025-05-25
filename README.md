# mertkayahan-repo
 Akbank Makine Öğrenmesine Giriş Bootcamp Final Projesi
 
# Heart Disease Prediction

## İçindekiler
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
Dört algoritmayı aynı veri seti üzerinde deneyip karşılaştırdım:

| Model               | Precision (True) | Recall (True) | F1-Score (True) | Accuracy |
|---------------------|------------------|---------------|-----------------|----------|
| Decision Tree       | 0.533            | 0.062         | 0.111           | 0.916    |
| Random Forest       | 0.593            | 0.047         | 0.088           | 0.917    |
| XGBoost             | 0.518            | 0.096         | 0.162           | 0.916    |
| **Balanced RF**     | **0.226**        | **0.770**     | **0.350**       | 0.757    |

- Diğer algoritmalar, azınlık sınıf (“hasta”) vakalarını neredeyse gözardı ederken  
- **BalancedRandomForestClassifier** en iyi F1-score’u (0.350) ve yüksek recall’u (%77) sağladı.

---

## Seçilen Model ve Sonuçlar
- **Model**: Balanced Random Forest  
- **Hiperparametreler**:  
```python
n_estimators=100  
max_depth=10  
random_state=42  
n_jobs=-1  

## Threshold Optimizasyonu:
- Varsayılan eşik (0.5) ile F1 = 0.286
- Eşik taraması → en iyi F1 = 0.397 @ threshold = 0.7
- Nihai Performans (threshold=0.7):
Precision: 0.328  
Recall:    0.503  
F1-Score:  0.397  
Accuracy:  0.857  
Seçim Gerekçesi:
- Yüksek recall (%50 üzeri) ile hastaları kaçırmama önceliği
- En yüksek F1 dengesi

## Gerçek Dünyada Kullanım Senaryoları
- Hastane/klinik ön taraması: Risk altındaki bireyleri erken tespit edip ileri tetkikleri planlama.
- Sağlık sigortası: Maliyet optimizasyonu için yüksek riskli müşterilere yönelik önleyici programlar.
- Kişisel sağlık uygulamaları: Kullanıcılara yaşam tarzı önerileri sunarak kalp sağlığı takibi.

## Gelecek Geliştirmeler
- Farklı dengeleme yöntemleri
- Derin öğrenme
- Canlı sistem entegrasyonu:
- REST API ile gerçek zamanlı tahmin servisleri