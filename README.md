# mertkayahan-repo
 Akbank Makine Öğrenmesine Giriş Bootcamp Final Projesi
 deneme 
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
- [Kurulum ve Çalıştırma](#kurulum-ve-çalıştırma)  

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
