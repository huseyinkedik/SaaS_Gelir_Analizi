# SaaS Gelir Analizi Dashboard
![alt text](https://github.com/huseyinkedik/SaaS-Gelir-Analizi/blob/main/Dashboard%202.png)
Detaylı dashboard içeriğine ve diğer projelere [buradan](https://public.tableau.com/views/4_Konu4_Tableaudaetkileimlipanolaroluturma/Dashboard2?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link) ulaşabilirsiniz.

## Proje Özeti:

Bir SaaS (Hizmet Olarak Yazılım) işletmesinin gelir akışını analiz etmek ve görselleştirmek için geliştirilmiştir. Tableau kullanılarak oluşturulan bu analiz, işletmenin aylık tekrar eden geliri (MRR), toplam geliri ve müşteri kohortları üzerinden yapılan incelemeleri içerir. Dashboard, iş kararlarını yönlendirmek için kritik gelir metrikleri sunar ve müşteri bağlılığını anlamak için veri odaklı bir bakış açısı sağlar.


## Dataset:

**user_id**
Bir kullanıcıyı veya müşteriyi benzersiz şekilde tanımlayan alfanümerik kod. Genellikle veri güvenliği ve gizlilik nedeniyle şifrelenir. Veritabanlarında ve müşteri yönetim sistemlerinde yaygın olarak kullanılır.

**payment_date**
Finansal işlemin gerçekleştiği tarihi gösteren zaman damgası. Genellikle gün.ay.yıl formatında kaydedilir. Nakit akışı analizleri, gelir raporlaması ve muhasebe kayıtları için kritik öneme sahiptir.

**location**
Müşterinin veya satışın gerçekleştiği coğrafi bölgeyi belirten kategori. Çoğunlukla iş operasyonlarını ve pazar segmentlerini yönetmek için kullanılır. Yaygın bölge kodları arasında APAC (Asya Pasifik), EMEA (Avrupa, Orta Doğu ve Afrika) ve USA (Amerika Birleşik Devletleri) bulunur.

**software_name**
Satın alınan veya kullanılan yazılım ürününün adı. Ürün portföyü yönetimi, satış analizi ve müşteri segmentasyonu için kullanılır. Yazılım şirketlerinin farklı ürün hatlarını veya modüllerini temsil eder.

**is_enterprise_customer**
Müşterinin kurumsal seviyede olup olmadığını belirten boolean (mantıksal) değer. TRUE veya FALSE olarak kaydedilir. Müşteri sınıflandırması, fiyatlandırma stratejileri ve hizmet seviyesi anlaşmaları için önemlidir.

**revenue_amount**
Bir işlemden elde edilen gelir miktarı. Genellikle belirli bir para biriminde ifade edilir. Finansal raporlama, gelir tahmini ve iş performansı analizlerinde kullanılır.

---

## Analiz Aşamaları:

### 1. **New MRR (New Monthly Recurring Revenue)**:

**New MRR**, her ay ödeme yapan yeni müşterilerden elde edilen gelir miktarını gösterir. Bu, işletmenin büyümesini anlamak için kritik bir metriktir.

- **Veri Hazırlığı**:
  - Ödeme yapılan ayın müşterinin ilk ödeme yaptığı ay olup olmadığı belirlendi.
  - Yeni MRR'yi her ay için topladık.

- **Hesaplamalar**:
```tableu
  -- New customer hesaplanması
  IF DATETRUNC('month', [payment_date]) = DATETRUNC('month', [First Payment Date]) THEN TRUE ELSE FALSE END
  ```
  Ödeme yapılan ayın müşterinin ilk ödeme yaptığı ay olup olmadığını kontrol eder. DATETRUNC('month', [payment_date]) ile ödeme tarihi ay seviyesine indirilir ve DATETRUNC('month', [First Payment Date]) ile karşılaştırılır. Eğer iki tarih aynıysa, müşteri yeni kabul edilir ve formül TRUE döner, aksi halde FALSE döner. Bu, yeni müşterileri tespit etmek ve sadece ilk kez ödeme yapan müşterilerden gelen gelirleri analiz etmek için kullanılır.

  ```tableu
  -- Yeni MRR'nin hesaplanması
  IF [New Customer] THEN [revenue_amount] END
  ```
  Bu formül, sadece yeni müşterilerden gelen geliri toplar. **[New Customer]** sütunu, yeni müşterileri belirlemek için kullanılırken **[revenue_amount]** her müşteriden elde edilen aylık ücreti temsil eder.

- **Grafik Türü**:
  Bu veriler bir **çubuk grafik (bar chart)** ile görselleştirildi. Her bir ayda elde edilen Yeni MRR değerleri barlar halinde sunuluyor. Örneğin, **Haziran 2022**'de $44,085 gelir elde edilmiştir.

### 2. **Toplam Gelir ve Yüzdesel Değişim**:

**Toplam Gelir**, işletmenin her ay elde ettiği toplam gelirdir ve bir önceki ay ile kıyaslanarak yüzde değişim hesaplanır.

- **Veri Hazırlığı**:
  - Her ay için toplam gelir belirlendi.
  - Bir önceki ayın geliri ile kıyaslanarak yüzdesel değişim hesaplandı.

- **Hesaplamalar**:
  
  **table calculation** kullanılarak bir önceki ayın gelirini kullanarak yüzdesel değişimi hesaplandı.  **percent difference from** , bir değerin önceki bir değere göre yüzde olarak ne kadar arttığını veya azaldığını hesaplamak için kullanılır.

- **Grafik Türü**:
  **Dual-axis chart (Çift eksenli grafik)** kullanılarak hem toplam gelir hem de yüzdesel değişim aynı anda gösterildi. Toplam gelir çizgi grafikte, yüzdesel değişim ise ikincil bir eksenle görselleştirildi.

  Örneğin, **Ağustos 2022**'de %30.50'lik bir artış görülürken, **Mart 2023**'te %6.89'luk bir düşüş yaşanmıştır.

### 3. **Kohort Analizi**:

Kohort analizi, belirli bir ayda ödeme yapan kullanıcıların ilerleyen aylardaki gelir katkılarını inceler. Amaç, uzun vadeli müşteri sadakatini ve gelir tutma oranını anlamaktır.

- **Veri Hazırlığı**:
  - Her müşteri için ilk ödeme yaptıkları ay (kohort ayı) belirlendi.
  - Müşterilerin her ay işletmeye getirdiği gelir, ilk ay ile kıyaslanarak hesaplandı.

- **Hesaplamalar**:
  ```sql
  -- İlk ay geliri ve takip eden ayların geliri oranı
  DATEDIFF('month',[First Payment Month],[payment_date])
  ```
  Bu formül, [First Payment Month] ile [payment_date] arasındaki ay farkını hesaplar. DATEDIFF('month', ...) fonksiyonu, iki tarih arasındaki belirli bir zaman birimi (bu örnekte ay) farkını döndürür. 

- **Grafik Türü**:
  **Isı haritası (heatmap)** kullanılarak bu veriler görselleştirildi. Hücrelerin rengi, ilgili ayda elde edilen gelirin ilk aya göre oranını gösterir. Daha koyu renkler, daha yüksek bir oranı, dolayısıyla daha yüksek gelir tutma oranını ifade eder. Bu sayede, hangi kohortların daha uzun süre gelir sağladığı anlaşılır.

---

## Dashboard Yapısı ve Filtreler

Bu analizde üç ayrı sayfa birleştirildi ve kullanıcıların **lokasyon (APAC, EMEA, USA)** ve **tarihe** göre filtreler uygulayarak analiz yapabilmesine olanak tanındı.

- **Filtreler**:
  ```sql
  -- Konuma göre filtreleme
  IF [region] = 'APAC' THEN 'APAC'
  ELSEIF [region] = 'EMEA' THEN 'EMEA'
  ELSE 'USA'
  END
  ```
  Bu filtre, kullanıcının belirli bir coğrafi bölgeyi seçmesine olanak tanır. Benzer şekilde tarih filtreleri de kullanılarak belirli dönemler için veri analizi yapılabilir.

---

## Sonuçlar ve Öneriler:

1. **Büyüme Fırsatları**: Yeni MRR verileri, işletmenin yeni müşteri kazanımındaki performansını ortaya koyar. Özellikle Haziran 2022 ve Mart 2023'teki zirveler, bu dönemlerde yapılan pazarlama veya ürün iyileştirme çalışmalarının başarılı olduğunu gösterir.
  
2. **Müşteri Sadakati**: Kohort analizi, müşteri sadakatini değerlendirme imkanı sunar. İlk üç aydan sonra gelirde düşüş görülen kohortlar, müşteri bağlılığını artırmaya yönelik stratejiler geliştirilmesi gerektiğini gösterir.
  
3. **Gelir Trendleri**: Yüzdesel değişim ile birlikte toplam gelir grafiği, işletmenin gelirdeki iniş çıkışlarını izlemeyi sağlar. Ağustos 2022'deki büyüme, belirgin bir stratejik başarının sonucudur.

---

Proje dashboard içeriğine ve diğer projelere [buradan](https://public.tableau.com/views/4_Konu4_Tableaudaetkileimlipanolaroluturma/Dashboard2?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link) ulaşabilirsiniz.   
Kullanılan veri dosyasını [buradan](https://drive.google.com/file/d/1_SzRROlaaSBTamkrOtgVUlR1kqPXthkg/view?usp=drive_link) indirebilirsin.
