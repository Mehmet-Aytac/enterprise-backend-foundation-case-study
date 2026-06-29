# Öğrenilen Dersler

Bu doküman, özel backend altyapı temeli case study sürecinden çıkarılan ana dersleri özetler.

## 1. Mimari Üretmek Kolay, Güvenmek Zordur

AI destekli geliştirme kısa sürede çok fazla yapı üretebilir.

Ancak mimari yalnızca varsayımlar test edildiğinde anlamlı hale gelir:

- Eşzamanlı istekler altında ne olur?
- Rota gerekli yetkilendirme bilgisini sağlamazsa ne olur?
- Yanıt yanlışlıkla fazla alan döndürürse ne olur?
- Kirli yerel veritabanı entegrasyon sonucunu etkilerse ne olur?

En değerli kısım dosya üretmek değil; davranışı incelemek, hedefli kontroller yazmak, uç durum bulmak ve sistemi tekrar tekrar sertleştirmekti.

## 2. Eksik Bilgi Güvenli Ret Üretmelidir

Yetkilendirme için gerekli sunucu doğrulamalı bilgi eksikse sistem izin varmış gibi davranmamalıdır.

İzin; şube, departman, ekip, sahiplik, ilişki veya sınıflandırmaya bağlıysa bu bilgiler güvenilir sunucu verisinden gelmelidir.

Sistem izinli olduğunu kanıtlayamıyorsa reddetmelidir.

## 3. Yetki Karar Motoru Basit Rol Kontrolü Değildir

Rol izni yalnızca başlangıçtır.

Güvenli karar için kiracı sınırı, aktör tipi, kapsam kısıtları, ilişki kontrolleri, politika kuralları, oturum güven düzeyi ve kaynak bilgileri birlikte değerlendirilmelidir.

Bu yüzden yetki karar motoru bu case study'nin en önemli parçalarından biridir.

## 4. Tarayıcı ve API Kimlik Akışları Ayrı Düşünülmelidir

Tarayıcı çerez kimlik doğrulaması ve açık API erişim akışları farklı risklere sahiptir.

Bu iki akış aynıymış gibi davranmak yerine ayrı davranış, ayrı doğrulama ve ayrı dokümantasyon gerektirir.

## 5. Kurcalamayı Belli Etmek Değişmezlik Demek Değildir

Denetim kayıt zincirleri faydalıdır ama sihirli değildir.

Uygulama seviyesinde değiştirme, silme veya sıralama sorunlarını fark etmeye yardım eder. Ancak veritabanını tek başına değiştirilemez hale getirmez.

Doğru ifade kurcalamayı belli eden yapıdır; kurcalanamaz yapı değildir.

## 6. Dokümantasyon Sadece Özellik Anlatmamalıdır

İyi portfolyo dokümantasyonu yalnızca “ne var?” sorusunu değil, “ne iddia edilmiyor?” sorusunu da cevaplamalıdır.

Bu repo bu yüzden sınırlar, doğrulama kapsamı ve kaynak kod politikası gibi bölümleri açıkça içerir.

## 7. AI Desteği Sahiplik Gerektirir

AI araçları kullanmak sahipliği ortadan kaldırmaz.

Değerli kısım şunlardı:

- gereksinimleri belirlemek
- mimari kararları değerlendirmek
- doğrulama çıktısını okumak
- uç durum aramak
- geniş iddiaları somut kontrollere çevirmek
- sınırları dürüstçe belgelemek

## 8. Altyapı Temeli Ürün Değildir

Backend altyapı temeli tek başına son kullanıcı ürünü değildir.

Gerçek ürün için iş akışı, kullanıcı arayüzü, onboarding, raporlama, operasyon ve gerçek kullanıcı geri bildirimi gerekir.

Altyapı temeli ise gelecekteki modüller için riskleri azaltan ortak platform katmanı sağlar.

## 9. Güçlü Backend Projeleri Görünür Olmayabilir

Frontend demosu hızlı anlaşılır. Backend altyapı temelinin değeri daha görünmezdir:

- kiracı sınırları
- öngörülebilir yetkilendirme
- güvenli hata davranışı
- denetim kanıtı
- doğrulama kontrolleri
- dağıtım sınırları

Bu yüzden public case study, görünmeyen backend mühendisliğini diyagramlar, tablolar ve net açıklamalarla görünür hale getirmelidir.
