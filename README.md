# kalphastali-itahmini
KALP HASTALIĞI TAHMİNİ
Derin öğrenme tabanlı ikili sınıflandırma ile klinik karar desteği için bir ön çalışma

Veri	918 hasta kaydı · 11 girdi · 1 hedef
Model	İki gizli katmanlı MLP · sigmoid çıktı
Test başarımı	Doğruluk %88,41 · Recall %92,11 · ROC-AUC 0,9291

Özet
Bu çalışmada, demografik ve klinik ölçümlerden yararlanarak kalp hastalığı durumunu tahmin eden bir çok katmanlı algılayıcı (MLP) modeli geliştirilmiştir. Kaggle’daki Heart Failure Prediction Dataset üzerinde veri kalitesi incelenmiş; sıfır kodlanmış geçersiz ölçümler eksik değer adayı olarak ele alınmış, kategorik değişkenler one-hot kodlama ile, sayısal değişkenler standartlaştırma ile dönüştürülmüştür. Eğitim, doğrulama ve test kümeleri birbirinden ayrılmış; karar eşiği doğrulama kümesinde hastaları kaçırmama önceliğine göre seçilmiştir. Ayrı tutulan test kümesinde model
%88,41 doğruluk, %92,11 duyarlılık ve 0,9291 ROC-AUC elde etmiştir. Sonuçlar umut verici olmakla birlikte, açık bir veri lisansı ve harici klinik doğrulama olmadan model gerçek hasta bakımında kullanılmamalıdır.
1.	Giriş ve amaç
Kalp ve damar hastalıklarının erken değerlendirilmesi, klinik risk taşıyan kişilerin ileri incelemeye yönlendirilmesi açısından önemlidir. Makine öğrenmesi, rutin hasta ölçümlerinden örüntüler öğrenerek bu değerlendirmeye yardımcı olabilecek araçlar sunar. Bu çalışmanın amacı, HeartDisease hedefini 0 (hastalık yok) ve 1 (hastalık var) olarak sınıflandıran bir model kurmak ve özellikle pozitif vakaları yakalama oranını (Recall/duyarlılık) incelemektir.
Modelin çıktısı klinik tanı olarak değil, yalnızca araştırma amaçlı bir karar destek göstergesi olarak yorumlanmalıdır. Veri seti ve örnekleme yöntemi, gerçek klinik popülasyondaki hastalık sıklığını veya kullanım ortamını temsil etmeyebilir.
 
2.	Veri seti ve yöntem
Veri kaynağı ve değişkenler
Çalışmada Kaggle’da fedesoriano tarafından yayımlanan Heart Failure Prediction Dataset kullanılmıştır. Veri kümesi 918 satır ve 12 sütun içerir: yaş, cinsiyet, göğüs ağrısı tipi, dinlenme kan basıncı, kolesterol, açlık kan şekeri, dinlenme EKG’si, maksimum kalp hızı, egzersiz anjinası, Oldpeak, ST eğimi ve HeartDisease hedefi. 11 özellik model girdisi, HeartDisease (0/1) hedef değişkendir. Not defterindeki kaynağın bağlantısı kaynakçada verilmiştir.
Veri kalitesi ve ön işleme
Ham tabloda boş hücre görülmemiştir; ancak klinik olarak geçersiz olabilecek sıfırlar ayrıca incelenmiştir. Kolesterolde 172 sıfır (%18,74), dinlenme kan basıncında 1 sıfır (%0,11) bulunmuştur. Kolesterol sıfırlarının 152’si hastalık sınıfı 1, 20’si sınıf 0’dır. Bu sıfırlar eksik ölçüm adayı olarak NaN’a çevrilmiştir; eğitim verisinin medyanı ile tamamlanmıştır. Sıfır kolesterol dışındaki dağılım sağa çarpık olduğundan (çarpıklık 1,24; medyan 237), medyan seçimi uç değerlere karşı daha dayanıklıdır. Önemli yöntem notu: not defterinde “fit yalnızca eğitimde” açıklaması yer alsa da kodda bu imputasyon medyanının gerçekten eğitim bölümündeki pipeline içinde öğrenildiği doğrulanmalıdır; raporlanan performans bu nedenle yeniden üretilebilir kod incelemesine tabidir.
Veri 918 gözlemle sınırlıdır ve diğer değişkenlerdeki uç/uygunsuz değerlerin kapsamlı klinik temizliği raporlanmamıştır. Beş kategorik sütun one-hot kodlanmış, sayısal sütunlar StandardScaler ile ölçeklenmiştir. Ön işleme nesnesi eğitim kümesine fit edilerek doğrulama ve teste aynı dönüşüm uygulanmıştır.
Veri bölme ve model
Hedef sınıf oranlarını korumak için stratified bölme ve random_state=42 kullanılmıştır. Toplam verinin %70’i eğitim (642), %15’i doğrulama (138), %15’i test (138) olarak ayrılmıştır. Model; 64 ve 32 nöronlu, ReLU etkinleştirmeli iki gizli yoğun katman ile sigmoid çıkış katmanından oluşur (3.457 eğitilebilir parametre). Adam (öğrenme oranı 0,001), ikili çapraz entropi kaybı ve 32’lik batch kullanılmış; eğitim en çok 100 epoch sürdürülmüş ve doğrulama kaybı için sabır değeri 15 olan Early Stopping uygulanmıştır. En düşük doğrulama kaybı 23. epoch’ta 0,3537 olarak kaydedilmiştir.
Karar eşiği
Olasılık eşiği 0,30–0,70 arasında doğrulama kümesinde karşılaştırılmıştır. 0,45 eşiği, 0,50 eşiğine göre pozitif sınıf recall değerini %88,16’dan %89,47’ye çıkarırken doğruluğu %84,78 düzeyinde tutmuştur. 0,70 eşiği daha yüksek doğruluk ve kesinlik verse de recall %82,89’a gerilemiştir. Çalışmanın duyarlılık önceliğine uygun olarak 0,45 seçilmiş; bu eşik test verisine yalnızca son değerlendirmede uygulanmıştır.
 
3.	Bulgular
Keşifsel bulgular
Hastalık sınıfındaki hastaların ortalama yaşı 55,90; hastalık olmayanların 50,55’tir. Hastalık sınıfında maksimum kalp hızı daha düşük (127,66’ya karşı 148,15), Oldpeak daha yüksek (1,27’ye karşı 0,41) bulunmuştur. Göğüs ağrısı gruplarında gözlenen hastalık oranı ASY’de %79,03, TA’da %43,48, NAP’ta %35,47 ve ATA’da %13,87’dir. Egzersiz anjinası “Y” olanlarda oran %85,18; “N” olanlarda %35,10’dur. Bunlar bu örneklem içindeki betimsel ilişkilerdir; nedensellik veya bireysel risk yorumu olarak değerlendirilmemelidir.
Bağımsız test kümesi performansı

Ölçüt	Sonuç	Yorum
Doğruluk (Accuracy)	%88,41	138 gözlemin 122’si doğru sınıflandırıldı.
Kesinlik (Precision, sınıf 1)	%87,50	Hasta tahminlerinin %87,5’i test etiketinde hastaydı.
Duyarlılık (Recall, sınıf 1)	%92,11	76 pozitif vakanın 70’i yakalandı; 6’sı kaçırıldı.
F1-skoru (sınıf 1)	%89,74	Kesinlik ve duyarlılığın harmonik ortalaması.
ROC-AUC	0,9291	Eşikler genelinde ayırt etme başarısı.

Şekil 1. Test kümesinde eşik 0,45 için karışıklık matrisi. Satırlar gerçek, sütunlar tahmin edilen sınıftır. TN=52, FP=10, FN=6, TP=70.

Şekil 2. Notebook çıktısından alınan test ROC eğrisi; AUC=0,9291.
 
4.	Tartışma, sınırlılıklar ve sonuç
Test verisindeki %92,11 recall, pozitif olarak etiketlenmiş 76 vakanın 70’inin yakalandığını gösterir. Bunun karşılığında hastalık olmayan 62 kişinin 10’u yanlış pozitif sınıflandırılmıştır. Duyarlılık önceliği olan tarama senaryolarında bu tercih anlamlı olabilir; ancak yanlış pozitiflerin ek tetkik, kaygı ve kaynak kullanımı doğurabileceği klinik bağlamda ayrıca değerlendirilmelidir. ROC-AUC’nin 0,9291 olması farklı eşiklerde iyi ayırt etme performansına işaret eder, fakat tek başına kalibrasyonu, klinik yararı veya genellenebilirliği kanıtlamaz.
Sınırlılıklar
•	Veri kümesi küçüktür (n=918) ve kaynağında bir araya getirilmiş klinik veri örneklerinden oluşur; örneklem temsiliyeti ve farklı kurumlara taşınabilirlik belirsizdir.
•	Sonuç tek bir stratified bölme ve sabit rastgelelik tohumu üzerinden raporlanmıştır. Güven aralıkları, çapraz doğrulama ve bağımsız dış doğrulama verilmemiştir.
•	Karar eşiği doğrulama kümesinde seçilmiştir; bu doğru ayrımdır, ancak bir kez seçilen eşik için klinik maliyet/yarar analizi yapılmamıştır.
•	Not defterinde eğitim-doğrulama kaybı ilerleyen epoch’larda ayrışarak aşırı öğrenme olasılığı göstermektedir. Early Stopping uygulanmıştır; yine de dış doğrulama ve düzenlileştirme değerlendirmesi gerekir.
•	Sıfır değerlerin eksik ölçüm sayılması klinik açıdan makul olsa da varsayımdır. Özellikle eksiklik örüntüsünün hedef sınıfla ilişkisi ve eğitim kümesindeki gerçek imputasyon adımı doğrulanmalıdır.
•	Yalnızca ayırt etme ölçütleri raporlanmıştır. Kalibrasyon, alt gruplarda adalet, klinik fayda ve veri lisansı/atıf koşulları ayrıca incelenmelidir.
Sonuç
Kaggle Heart Failure Prediction Dataset üzerinde geliştirilen MLP, ayrılmış test kümesinde %88,41 doğruluk, %87,50 kesinlik, %92,11 recall, %89,74 F1 ve 0,9291 ROC-AUC üretmiştir. Bu değerler, çalışmanın veri kümesi içinde umut verici bir sınıflandırma performansına işaret eder. Sonuç, hekim değerlendirmesinin yerine geçmez ve klinik kullanım için doğrulanmış bir model anlamına gelmez. İleride daha güvenilir bir değerlendirme için ön işleme adımlarının eğitim pipeline’ında yeniden doğrulanması, tekrarlı/çapraz doğrulama, güven aralıkları, dış merkez testleri, kalibrasyon ve klinik eşik analizi önerilir.
Kaynakça
[1]	fedesoriano. Heart Failure Prediction Dataset. Kaggle. https://www.kaggle.com/datasets/fedesoriano/heart-failure-prediction (Erişim: 14 Kasım 2026).
VERİ SETİ = https://www.kaggle.com/datasets/fedesoriano/heart-failure-prediction 
COLAB LİNKİ = https://colab.research.google.com/drive/1t5nYGa4xJPr3XMwuVXioMdkwFqRc5Zue?usp=sharing 
