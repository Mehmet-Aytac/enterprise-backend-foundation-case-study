# Öğrenilen Dersler

Bu doküman, private full-stack ERP/SaaS projesinin backend foundation'dan gerçek operasyonel frontend içeren bir uygulamaya dönüşürken ortaya çıkan ana dersleri özetler.

## 1. Mimari Üretmek Kolay, Güvenmek Zordur

AI-assisted development kısa sürede çok fazla yapı üretebilir.

Ancak architecture yalnızca varsayımlar test edildiğinde anlamlı hale gelir:

- Concurrent request altında ne olur?
- Authorization için gerekli facts eksikse ne olur?
- Token yeniden kullanılırsa ne olur?
- Response yanlışlıkla sensitive data döndürürse ne olur?
- Browser flow backend auth modeline uymazsa ne olur?
- Frontend cache tenant veya permission boundaries'i dikkate almazsa ne olur?

Değerli iş dosya üretmek değil; behavior incelemek, failure mode'ları yeniden üretmek, evidence yorumlamak ve sistemi tekrar tekrar harden etmektir.

## 2. Security-Sensitive Kod Regression Evidence Gerektirir

Önemli sorunlar çoğu zaman syntax değil behavior problemidir.

Backend review sırasında karşılaşılan örnekler:

- gerekli resource facts eksikken scoped authorization'ın fail open davranabilmesi
- refresh-token rotation'ın concurrency-safe olması gerekliliği
- MFA recovery code'ların atomic single-use enforcement gerektirmesi
- TOTP enrollment verification için concurrency hardening ihtiyacı
- browser cookie flow'larında token material exposure'ın önlenmesi

Bu tür fix'ler, failure mode yeniden üretilebildiğinde ve tekrar kontrol edildiğinde daha güvenilir hale gelir.

## 3. Eksik Security Facts Fail Closed Olmalıdır

Access kararı branch, department, team, ownership, relationship, classification, session trust veya başka server-derived fact'e bağlıysa bu facts trusted data'dan gelmelidir.

Allow kararı için gerekli facts kurulamıyorsa sistem tahmin etmek yerine reddetmelidir.

## 4. Browser Auth ve API Auth Stack'in İki Tarafını da Etkiler

Cookie-based browser authentication ile explicit bearer-token API authentication farklı riskler ve client behavior taşır.

Bunları ayırmak backend ve frontend design'ı netleştirdi:

- browser flows HTTP-only cookies kullanır
- cookie-authenticated mutation'larda CSRF protection gerekir
- API/mobile flows explicit token endpoint kullanabilir
- browser UI default auth modeli olarak localStorage token'a dayanmak zorunda değildir
- frontend HTTP client backend transport/security modelini doğru yansıtmalıdır

Bu, full-stack architecture'ın en açık örneklerinden biri oldu: backend security kararı frontend implementation'ı doğrudan değiştiriyor.

## 5. Frontend Visibility Authorization Değildir

Permission-aware navigation usability sağlar; ancak route veya button gizlemek data'yı korumaz.

Frontend bootstrap permissions/capabilities ile UI'ı şekillendirebilir. Gerçek access decision yine backend tarafından verilir.

Frontend ve backend ayrı düşünülürse bu ayrım kolayca ihlal edilebilir.

## 6. Server State Açık Sınırlar Gerektirir

ERP arayüzleri filters, lists, detail records, permissions, generated identifiers, mutations ve sensitive projections'ı aynı anda taşır.

TanStack Query bunların sıradan local component variables değil, cache lifecycle'ı olan server-owned facts olduğunu daha açık hale getirdi.

Önemli dersler:

- query keys gerçekten data'yı değiştiren context ile scope edilmeli
- başarılı command sonrasında ilgili data invalidate edilmeli
- sensitive projection geniş cache'e yayılmamalı
- mutation results ve lifecycle rules backend authority ile uyumlu kalmalı

## 7. Gerçek Frontend Backend Design İçin Contract Test Gibi Çalışır

Backend yalnızca route definitions ve tests üzerinden bakıldığında oldukça düzgün görünebilir.

React frontend geliştirmek şu pratik contract sorularını ortaya çıkardı:

- bootstrap data
- session-cookie behavior
- CSRF
- normalized errors
- permission-aware navigation
- tenant ve branch context
- generated identifiers
- list/detail query shapes
- localization ve preferences
- sensitive-field caching

Bu yüzden frontend sadece ekran eklemedi. Backend'in gerçek application platform olarak tüketilebilir olup olmadığını da test etti.

## 8. Tamper-Evident Immutable Demek Değildir

Audit hash-chain application-level modification, deletion veya ordering problem'lerini fark etmeye yardımcı olabilir. Alttaki database'i değiştirilemez yapmaz.

Doğru ifade tamper-evident'tır; tamper-proof değildir.

Daha güçlü guarantees için protected backups, external log export, object-lock storage, SIEM integration veya external anchoring gibi operational controls gerekir.

## 9. Dokümantasyon Implemented ile Planned'ı Ayırmalıdır

Architecture kararlarını tamamlanmış implementation gibi yazmak portfolyo güvenilirliğini düşürür.

Frontend geliştikçe bu ayrım daha önemli hale geldi; çünkü seçilmiş stack ve target architecture'ın bazı parçaları bilinçli olarak staged durumdadır.

Public case study artık şunları ayrı tutar:

- bugün gerçekten implemented olanlar
- seçilmiş ama tamamlanmaya devam eden architecture
- bilinçli olarak deferred olanlar
- external production evidence gerektiren iddialar

Bu, bütün planları bitmiş feature gibi gösteren listeden daha değerlidir.

## 10. AI Desteği Sahiplik Gerektirir

AI kullanmak requirements'ı anlama, behavior inceleme, assumptions sorgulama ve validation yapma gereğini ortadan kaldırmaz.

Gerçek engineering learning sağlayan işler:

- neyin yapılacağına karar vermek
- design'ın neden safe/unsafe olduğunu sorgulamak
- suspected bug'ları reproducible check'e çevirmek
- generated implementation'ı otomatik kabul etmek yerine review etmek
- validation output okumak
- frontend behavior ile backend contracts'i karşılaştırmak
- trade-off ve limits belgelemek

AI için en faydalı rol, sorgusuz code generator olmaktan çok implementation partner, reviewer, test designer, tutor ve debugging assistant olmaktır.

## 11. Foundation Gerçek Product Surface Tarafından Kullanıldığında Daha Değerli Hale Gelir

Backend foundation tek başına da değerliydi; ancak gerçek frontend onu kullanmaya başladığında projenin niteliği değişti.

Operational UI soyut platform kararlarını somutlaştırdı:

- auth browser içinde gerçekten çalışmalıydı
- permissions UX'i şekillendirmeli ama authorization'ın yerini almamalıydı
- tenant/branch context queries ve routes boyunca taşınmalıydı
- API errors kullanılabilir UI states'e dönüşmeliydi
- domain contracts gerçek forms ve lists'i desteklemeliydi

Güncel en güçlü portfolyo sinyali, reusable backend foundation ile gelişen user-facing application arasındaki bağlantıdır.

## 12. Proje Değiştikçe Portfolyo Anlatımı da Değişmelidir

Eski anlatım olan "private backend architecture ve security-hardening case study", proje backend-only iken doğruydu.

Güncel ve daha doğru anlatım:

> Private, AI-assisted, active-development full-stack ERP/SaaS engineering case study; security-focused multi-tenant backend foundation ve gerçek backend contracts'i giderek daha geniş kullanan operational React frontend.

Bu çerçeve backend derinliğini korur; gerçek frontend çalışmasını saklamaz ve overall product'ı bitmiş gibi göstermez.
