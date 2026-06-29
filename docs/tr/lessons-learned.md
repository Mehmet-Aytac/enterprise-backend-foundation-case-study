# Lessons Learned

Bu doküman, private enterprise backend foundation case study sürecinden çıkarılan ana dersleri özetler.

## 1. Architecture üretmek kolay, güvenmek zordur

AI-assisted development çok hızlı şekilde structure üretebilir.

Ancak architecture anlamlı hale ancak assumptions test edildiğinde gelir:

- concurrent requests altında ne olur?
- route gerekli authorization fact'i sağlamazsa ne olur?
- response yanlışlıkla fazla field döndürürse ne olur?
- dirty local database integration result'ı etkilerse ne olur?

En değerli kısım file üretmek değil; behavior review etmek, targeted checks yazmak, edge case bulmak ve sistemi iteratively harden etmekti.

## 2. Missing data fail closed olmalıdır

Authorization için gerekli server-derived fact eksikse sistem allow varsaymamalıdır.

Permission branch, department, team, ownership, relationship veya classification'a bağlıysa bu facts trusted server-side data'dan gelmelidir.

Sistem izinli olduğunu kanıtlayamıyorsa deny etmelidir.

## 3. Permission engine basit role check değildir

Role grant yalnızca başlangıçtır.

Güvenli karar için tenant boundary, principal type, scoped grants, relationship checks, policy rules, session trust ve resource facts birlikte değerlendirilmelidir.

Bu yüzden permission engine bu case study'nin en önemli parçalarından biridir.

## 4. Browser ve API auth ayrı düşünülmelidir

Browser cookie authentication ve explicit API token authentication farklı risklere sahiptir.

Bu iki flow aynıymış gibi davranmak yerine ayrı davranış, ayrı validation ve ayrı dokümantasyon gerektirir.

## 5. Tamper-evident immutable demek değildir

Audit hash-chain faydalıdır ama magic değildir.

Application-level modification, deletion veya ordering problemlerini tespit etmeye yardım eder. Ancak database storage'ı tek başına immutable yapmaz.

Doğru ifade tamper-evident'tır, tamper-proof değil.

## 6. Documentation sadece feature anlatmamalıdır

İyi portfolio dokümantasyonu yalnızca “ne var?” sorusunu değil, “ne iddia edilmiyor?” sorusunu da cevaplamalıdır.

Bu repo bu yüzden limitations, validation scope ve source code policy gibi bölümleri açıkça içerir.

## 7. AI assistance ownership gerektirir

AI tools kullanmak ownership'i ortadan kaldırmaz.

Değerli kısım şunlardı:

- requirement belirlemek
- architecture kararlarını değerlendirmek
- validation output okumak
- edge case aramak
- broad claims'i concrete checks'e çevirmek
- sınırları dürüstçe belgelemek

## 8. Foundation product değildir

Backend foundation tek başına end-user product değildir.

Gerçek ürün için domain workflow, UI, onboarding, reporting, operations ve real user feedback gerekir.

Foundation ise future modules için riskleri azaltan shared platform layer sağlar.

## 9. Güçlü backend projeleri görünür olmayabilir

Frontend demo hızlı anlaşılır. Backend foundation'ın değeri daha görünmezdir:

- tenant boundaries
- predictable authorization
- safe failure behavior
- audit evidence
- validation checks
- deployment constraints

Bu yüzden public case study, görünmeyen backend engineering'i diyagramlar, tablolar ve net açıklamalarla görünür hale getirmelidir.
