Bu araştırma makalesi "Dönüşüm Oranı Tahmini için Parametrik Olmayan Gecikmeli Geri Bildirim Modeli" (A Nonparametric Delayed Feedback Model for Conversion Rate Prediction) başlığını taşıyor ve Yuya Yoshikawa ile Yusaku Imai tarafından yazılmış[cite: 1]. Makale, gösterimli reklamlarda dönüşüm oranlarını (CVR) tahmin etmek için NoDeF (Nonparametric Delayed Feedback model) adında bir model sunuyor[cite: 6].

Anahtar Kavramlar:

* **Dönüşüm Oranı Tahmini (CVR)**: Bu, bir reklama tıklandıktan sonra kullanıcıların istenen bir eylemi (örneğin, bir ürün satın alma) tamamlama oranını tahmin etmek anlamına gelir[cite: 1, 10]. Bu, reklamların etkinliğini ölçmek ve kullanıcıların ilgi alanlarını anlamak için önemlidir[cite: 1, 10].
* **Gecikmeli Geri Bildirim (Delayed Feedback)**: Gösterimli reklamcılıkta, reklam tıklaması ile gerçek dönüşüm arasında genellikle bir zaman gecikmesi (gecikmeli geri bildirim olarak adlandırılır) bulunur[cite: 2, 15]. Bu gecikme nedeniyle, gözlem süresinden sonra dönüşen örnekler yanlışlıkla negatif (dönüşmeyen) olarak değerlendirilebilir[cite: 3, 19]. Bu durum, ele alınmadığı takdirde hatalı CVR tahmin modellerine yol açabilir[cite: 19, 20].
* **Parametrik Olmayan Gecikmeli Geri Bildirim Modeli (NoDeF)**: Bu, makalenin temel katkısıdır[cite: 6, 25]. Önceki modellerin zaman gecikmesi için belirli bir parametrik dağılım (üstel veya Weibull gibi) varsaymasının aksine[cite: 4, 5, 22, 35], NoDeF böyle varsayımlarda bulunmaz[cite: 6, 25]. Bunun yerine, zaman gecikmesinin dağılımını parametrik olmayan bir şekilde temsil eder[cite: 6, 25], bu da gecikme dağılımlarının çeşitli şekillerine uyum sağlamasına olanak tanır[cite: 7, 24].
* **Tehlike Fonksiyonu (Hazard Function)**: Hayatta kalma analizinde, tehlike fonksiyonu $h(t)$, bir olayın (bu durumda dönüşümün) $t$ anında, $t$ anına kadar gerçekleşmediği varsayıldığında gerçekleşme oranını tanımlar[cite: 51]. NoDeF, tehlike fonksiyonunu, dönüşüm zamanının çekirdek değerlerinin ve zaman ekseni üzerindeki sözde noktaların ağırlıklı bir toplamı olarak tanımlar[cite: 27, 62].
* **Hayatta Kalma Fonksiyonu (Survival Function)**: Hayatta kalma fonksiyonu $s(t)$, bir olayın (dönüşüm) $t$ zamanına kadar gerçekleşmeme olasılığını temsil eder[cite: 51]. NoDeF'te hayatta kalma fonksiyonu, tehlike fonksiyonundan türetilir[cite: 68].
* **Gizli Değişken ($c_i$)**: NoDeF, her örnek için, dönüşümün gerçekleşmesi için geçen zamandan bağımsız olarak, örneğin sonunda dönüşüp dönüşmeyeceğini gösteren $c_i \in \{0,1\}$ gizli bir ikili değişken kullanır[cite: 28, 69]. Eğer bir örnek zaten dönüştüyse ($y_i=1$), $c_i=1$ olur[cite: 70]. Eğer gözlemlenen zamana kadar dönüşmediyse ($y_i=0$), $c_i$'nin öğrenme sürecinde tahmin edilmesi gerekir[cite: 71, 72].
* **EM Algoritması (Expectation-Maximization Algorithm)**: NoDeF, parametre tahmini için Beklenti-Maksimizasyon (EM) algoritmasını kullanır[cite: 29]. EM algoritması, gizli değişken atamalarını (E-adımı) yinelemeli olarak tahmin eder ve model parametrelerini (M-adımı) günceller.
* **Çekirdek Yoğunluk Tahmini (KDE) Fikirleri**: NoDeF'in tehlike fonksiyonu formülasyonu KDE'den fikirler ödünç alır[cite: 65], ancak iki önemli fark vardır: Birincisi, tehlike fonksiyonu yoğunluğun büyüklüğünü, girdi olarak bir özellik vektörü alan yoğunluk fonksiyonu $\alpha_l$ ile kontrol ederken, KDE yoğunluğun büyüklüğünü veri noktalarının sayısıyla temsil eder[cite: 66]. İkincisi, tehlike fonksiyonu, yoğunluğu hesaplamak için bir giriş zaman noktası $d$ ve sadece $L$ sözde nokta $\{t_l\}_{l=1}^{L}$'nin çekirdek değerlerini hesaplarken, KDE, giriş zaman noktasının ve boyutu genellikle $L$'den daha büyük olan tüm gözlemlenen zaman noktalarının çekirdek değerlerini hesaplamaya ihtiyaç duyar[cite: 67]. Bu nedenle tehlike fonksiyonu, yeni bir özellik vektörü için hızlı bir şekilde hesaplanabilir[cite: 67].
* **Lojistik Regresyon**: NoDeF içindeki dönüşüm modeli, dönüşüm olasılığını ($p(c_i=1|x_i;w)$) tahmin etmek için lojistik regresyon kullanır[cite: 76].

**Node.js Kavramları Açıklayan Kod Örnekleri (Kavramsal)**

Makale doğrudan Node.js'i içermese de, bazı kavramların Node.js ortamında nasıl temsil edilebileceğini, özellikle veri işleme ve model etkileşimi için, kavrayabiliriz.

**1. Dönüşüm Günlüğü Verilerini Temsil Etme:**

Dönüşüm günlüğü verilerini (Şekil 1'deki gibi) Node.js'de JavaScript nesneleri kullanarak temsil edebilirsiniz[cite: 12, 13, 14, 15, 16].

```javascript
// Dönüşüm günlüğü girişine örnek
const conversionLogEntry = {
    id: 1,
    clickDate: '2018/01/04 10:12:15',
    conversionDate: '2018/01/12 16:30:02', // Veya 'unobserved'
    adFeature1: 0.1,
    userFeature1: 10.3
};

// Dönüşüm günlüğü verilerinin bir koleksiyonu (örneğin, bir veritabanından)
const conversionLogs = [
    {
        id: 1,
        clickDate: new Date('2018-01-04T10:12:15'),
        conversionDate: new Date('2018-01-12T16:30:02'),
        adFeature1: 0.1,
        userFeature1: 10.3,
        y: 1, // Gözlemlenmiş dönüşüm
        d: (new Date('2018-01-12T16:30:02').getTime() - new Date('2018-01-04T10:12:15').getTime()) / (1000 * 60 * 60 * 24), // Gecikme gün cinsinden
        e: null // Geçen süre işleme sırasında ayarlanacak
    },
    {
        id: 2,
        clickDate: new Date('2018-01-05T08:42:33'),
        conversionDate: 'unobserved',
        adFeature1: 0.3,
        userFeature1: 8.4,
        y: 0, // Gözlemlenmemiş dönüşüm
        d: Infinity, // Gözlemlenmezse gecikme sonsuzdur
        e: null // Geçen süre işleme sırasında ayarlanacak
    },
    // ... daha fazla giriş
];

// Gerçek bir uygulamada, bunları büyük olasılıkla bir veritabanından çekeriz.
// Örneğin, bir veritabanı istemcisi kullanarak:
/*
async function getConversionLogsFromDB() {
    // 'db'nin veritabanı bağlantı nesneniz olduğunu varsayalım
    const result = await db.query('SELECT * FROM conversion_logs');
    return result.rows.map(row => ({
        id: row.id,
        clickDate: new Date(row.click_date),
        conversionDate: row.conversion_date === 'unobserved' ? 'unobserved' : new Date(row.conversion_date),
        adFeature1: row.ad_feature_1,
        userFeature1: row.user_feature_1
    }));
}
*/
```

**2. Özellikleri ve Parametreleri Kavramsal Olarak Tanımlama:**

Özellik vektörleri (`x` [cite: 48]), ağırlık vektörleri (`w` [cite: 48]) ve parametre matrisleri (`V` [cite: 48]), NoDeF modelini Node.js'de sıfırdan uygulayacaksanız diziler veya özel sayısal yapılar olarak temsil edilir. Karmaşık matematiksel işlemler için `math.js` veya `ndarray` gibi kütüphaneleri kullanabilirsiniz.

```javascript
// Bir reklam ve kullanıcı için örnek özellik vektörü (x_i)
const featureVectorX = [0.1, 10.3, /* ... diğer özellikler ... */]; // M boyutlu vektör

// Dönüşüm sınıflandırıcısı için örnek ağırlık vektörü (w)
const weightVectorW = [0.5, -0.2, /* ... diğer ağırlıklar ... */]; // M boyutlu vektör

// Zaman gecikmesi modeli için örnek parametre matrisi (V) (L x M)
const parameterMatrixV = [
    [0.1, 0.2, /* ... */], // Sözde nokta t_1 için satır
    [-0.3, 0.4, /* ... */], // Sözde nokta t_2 için satır
    // ... L satır
];

// Gerçek bir senaryoda, bunlar öğrenilir/yüklenir.
```

**3. Lojistik Regresyonu Simüle Etme (Dönüşüm Modeli):**

`p(c_i=1|x_i;w)` fonksiyonu (Denklem 10 [cite: 76]) Node.js'de uygulanabilir.

```javascript
// Basit bir nokta çarpımı fonksiyonu
function dotProduct(vec1, vec2) {
    if (vec1.length !== vec2.length) {
        throw new Error("Vektörler nokta çarpımı için aynı uzunlukta olmalıdır.");
    }
    let sum = 0;
    for (let i = 0; i < vec1.length; i++) {
        sum += vec1[i] * vec2[i];
    }
    return sum;
}

// Lojistik sigmoid fonksiyonu
function sigmoid(z) {
    return 1 / (1 + Math.exp(-z));
}

// p(c_i=1 | x_i; w) - Nihai dönüşüm olasılığı
function predictConversionProbability(featureVector, weightVector) {
    const z = dotProduct(weightVector, featureVector);
    return sigmoid(z);
}

// Örnek kullanım
const userFeatures = [1.0, 5.0, 0.7]; // Örnek özellikler
const conversionWeights = [0.2, -0.1, 0.3]; // Örnek öğrenilmiş ağırlıklar
const probConversion = predictConversionProbability(userFeatures, conversionWeights);
console.log(`Nihai dönüşüm olasılığı: ${probConversion.toFixed(4)}`);
```

**4. Tehlike Fonksiyonu Bileşenlerini Kavramsal Olarak Tanımlama (Zaman Gecikmesi Modeli):**

Tehlike fonksiyonu çekirdek fonksiyonları ve yoğunluk fonksiyonlarını içerir[cite: 62].

```javascript
// Örnek Gauss Çekirdeği (k(t_l, d)) - Denklem 4 [cite: 63]
function gaussianKernel(tl, d, bandwidth) {
    return Math.exp(-Math.pow(tl - d, 2) / (2 * Math.pow(bandwidth, 2)));
}

// Örnek Yoğunluk Fonksiyonu (alpha_l(x_i; V)) - Denklem 7 [cite: 63]
function intensityFunction(featureVector, V_l_row) {
    const V_l_dot_x = dotProduct(V_l_row, featureVector);
    return 1 / (1 + Math.exp(-V_l_dot_x));
}

// Kavramsal Tehlike Fonksiyonu (h(d_i; x_i, V)) - Denklem 3 [cite: 62]
function calculateHazardFunction(d_i, x_i, V_matrix, pseudoPoints, bandwidth) {
    let sum = 0;
    for (let l = 0; l < pseudoPoints.length; l++) {
        const t_l = pseudoPoints[l];
        const V_l_row = V_matrix[l];
        const alpha_l = intensityFunction(x_i, V_l_row);
        const kernel_val = gaussianKernel(t_l, d_i, bandwidth);
        sum += alpha_l * kernel_val;
    }
    return sum;
}

// Örnek kullanım (sahte verilerle)
const pseudoPoints = [1, 2, 3, 4, 5]; // t_l değerleri [cite: 61]
const exampleBandwidth = 0.5;
const exampleUserFeatures = [0.5, 12.0];
const exampleVMatrix = [
    [0.1, 0.05],
    [0.2, -0.1],
    [0.0, 0.15],
    [-0.1, 0.2],
    [0.3, -0.05]
];
const conversionDelay = 2.5; // d_i

const hazard = calculateHazardFunction(
    conversionDelay,
    exampleUserFeatures,
    exampleVMatrix,
    pseudoPoints,
    exampleBandwidth
);
console.log(`2.5 gün gecikmedeki hesaplanmış tehlike fonksiyonu: ${hazard.toFixed(4)}`);
```

**5. EM Algoritması (Üst Düzey Kavram):**

Tam EM algoritmasının Node.js'de uygulanması, M-adımı için optimizasyon kütüphaneleri (örneğin, makalede bahsedilen L-BFGS [cite: 85, 165]) dahil olmak üzere oldukça kapsamlı olacaktır. Ancak, yinelemeli yapısını temsil edebilirsiniz[cite: 91].

```javascript
async function trainNoDeFModel(trainingData, initialWeightsW, initialMatrixV, maxIterations, tolerance) {
    let currentW = initialWeightsW;
    let currentV = initialMatrixV;
    let previousQ = -Infinity; // Q(Theta; Theta_hat)'ı temsil eder

    for (let j = 0; j < maxIterations; j++) {
        console.log(`İterasyon ${j + 1}`);

        // E-Adımı: Gizli değişken c_i'nin posterior olasılıklarını tahmin et [cite: 92]
        const estimatedHiddenProbabilities = estimateHiddenVariables(trainingData, currentW, currentV);
        // Bu fonksiyon 23, 24, 25 numaralı denklemleri kullanarak q_ic'yi hesaplar [cite: 88, 24, 25]

        // M-Adımı: Parametreleri (w ve V) optimizasyon kullanarak güncelle [cite: 92]
        // (Bu, sayısal bir optimizasyon kütüphanesi gerektirir)
        const newW = await lbfgsUpdateW(currentW, estimatedHiddenProbabilities, trainingData); // Kavramsal
        const newV = await lbfgsUpdateV(currentV, estimatedHiddenProbabilities, trainingData); // Kavramsal

        currentW = newW;
        currentV = newV;

        // Mevcut amaç fonksiyonu Q(Theta; Theta_hat)'ı hesapla [cite: 22]
        const currentQ = calculateObjectiveFunction(trainingData, currentW, currentV, estimatedHiddenProbabilities);

        // Durma koşulunu kontrol et [cite: 92]
        if (currentQ - previousQ < tolerance) {
            console.log("Yakınsama sağlandı.");
            break;
        }
        previousQ = currentQ;
    }
    return { finalW: currentW, finalV: currentV };
}

// Gösterim için sahte fonksiyonlar (gerçek uygulama karmaşık olacaktır)
function estimateHiddenVariables(data, w, V) {
    // Mevcut w ve V verildiğinde, her örnek için q_ic'yi hesaplama mantığı
    // Burası (24) ve (25) numaralı denklemlerin uygulanacağı yerdir [cite: 24, 25]
    return data.map(sample => {
        // Mevcut w kullanarak p(c=0|x) ve p(c=1|x)'i hesapla
        // Mevcut V kullanarak s(e_i; x_i, V)'yi hesapla
        // Ardından q_i0 ve q_i1'i hesapla
        return {
            id: sample.id,
            q_i0: Math.random(), // Yer tutucu
            q_i1: Math.random() // Yer tutucu
        };
    });
}

function calculateObjectiveFunction(data, w, V, hiddenProbs) {
    // (22) numaralı denklemi kullanarak Q(Theta; Theta_hat)'ı hesaplama mantığı [cite: 22]
    // Bu, gözlemlenen ve gözlemlenmeyen örnekler için günlük-olasılık terimlerinin toplanmasını içerir
    return Math.random() * 1000; // Yer tutucu
}

// L-BFGS'nin gerçek bir uygulaması için, büyük olasılıkla harici bir kütüphane
// veya yüksek düzeyde optimize edilmiş bir sayısal rutin kullanırsınız.
async function lbfgsUpdateW(currentW, hiddenProbs, trainingData) {
    console.log("W optimize ediliyor...");
    // Asenkron optimizasyonu simüle et
    return new Promise(resolve => setTimeout(() => resolve(currentW.map(val => val + (Math.random() - 0.5) * 0.01)), 50));
}

async function lbfgsUpdateV(currentV, hiddenProbs, trainingData) {
    console.log("V optimize ediliyor...");
    // Asenkron optimizasyonu simüle et
    return new Promise(resolve => setTimeout(() => resolve(currentV.map(row => row.map(val => val + (Math.random() - 0.5) * 0.01)))), 50);
}

// Eğitimi nasıl başlatabileceğinize dair bir örnek (kavramsal olarak)
/*
(async () => {
    const trainingData = conversionLogs.filter(log => log.y !== null); // Gerekirse başlangıç kurulumu için eksik girişleri filtrele
    const initialW = Array(featureVectorX.length).fill(0.1);
    const initialV = Array(parameterMatrixV.length).fill(null).map(() => Array(featureVectorX.length).fill(0.1));
    const trainedModel = await trainNoDeFModel(trainingData, initialW, initialV, 5, 0.001);
    console.log("Model eğitimi tamamlandı.");
    console.log("Son W:", trainedModel.finalW);
    console.log("Son V (ilk satır):", trainedModel.finalV[0]);
})();
*/
```

Bu Node.js örnekleri basitleştirilmiş ve kavramsaldır. NoDeF'in tam bir uygulaması, genellikle özel makine öğrenimi kütüphaneleri veya çerçeveleri tarafından daha iyi ele alınan önemli sayısal hesaplama ve optimizasyon gerektirecektir. Ancak, modelin farklı bileşenlerinin bir JavaScript ortamında nasıl yapılandırılabileceğini göstermektedirler.
