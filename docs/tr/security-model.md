# Güvenlik Modeli

Bu doküman, özel backend altyapı temelinde ele alınan güvenlik modelini özetler.

Bu bir case-study özetidir; dış denetim raporu, uyumluluk sertifikası veya canlı üretim kullanımı iddiası değildir.

## Güvenlik Hedefleri

Özel proje şu hedefler etrafında tasarlandı:

- kiracılar arası veri erişimini engellemek
- kimlik doğrulama (authentication) ile yetkilendirmeyi (authorization) ayrı tutmak
- yetkilendirmeyi merkezi, belirleyici ve varsayılan olarak reddeden şekilde tasarlamak
- tarayıcı çerez oturumlarını güvenli akışlarla korumak
- oturumları uygulama süreci dışında saklanabilir ve geri alınabilir tutmak
- önemli güvenlik olaylarını görünür hale getirmek
- denetim kayıtlarını dayanıklı iş kuyruğu üzerinden işlemek
- denetim geçmişini uygulama seviyesinde kurcalamayı belli eder hale getirmek
- güvenilir proxy zinciri yapılandırılmadıkça istemci başlıklarına güvenmemek
- yanıtlar ve loglar içinde hassas veri görünürlüğünü azaltmak

## Korunan Varlıklar

Özel tasarım şu varlıkları korunan varlıklar olarak ele aldı:

- kullanıcı hesapları ve oturum bilgileri
- kiracı verisi ve kiracı yönetişim ayarları
- organizasyon hiyerarşisi ve üyelik atamaları
- rol, izin, politika ve ilişki kayıtları
- servis hesabı bilgileri ve izin atamaları
- denetim kayıtları, güvenlik olayları ve iş kuyruğu kayıtları
- kişisel veri, gizli iş alanları, performans verisi ve güvenliğe duyarlı üstveri

## Aktör Türleri

Tasarım şu aktör türlerini dikkate aldı:

- anonim istemci
- kimliği doğrulanmış kiracı kullanıcısı
- kiracı yöneticisi
- sistem yöneticisi
- tarayıcı istemcisi
- API/mobil istemci
- servis hesabı
- worker süreci
- veritabanı migration süreci

## Güven Sınırları

Önemli güven sınırları:

- istemciden Express API'ye geçiş
- reverse proxy'den uygulama sürecine geçiş
- API istek yolundan PostgreSQL'e geçiş
- API istek yolundan denetim/güvenlik iş kuyruğuna geçiş
- worker sürecinden kalıcı denetim/güvenlik tablolarına geçiş
- tarayıcı akışından değişiklik rotalarına geçiş
- servis hesabı kimliğinden makine aktörü davranışına geçiş

## Ana Riskler ve Kontroller

### Kiracılar arası erişim

Kiracı sınırları birinci sınıf güvenlik sınırı olarak ele alındı. Kiracıya ait kayıtlar, istemciden gelen iddialar yerine sunucu tarafından doğrulanan kiracı bağlamıyla erişilmelidir.

### Tarayıcı ve API akışlarının karışması

Tarayıcı çerez akışları ve API/mobil erişim akışları farklı risklere sahiptir. Bu nedenle aynı davranış gibi ele alınmamalıdır.

### Makine kimliği ile insan kimliğinin karışması

Servis hesapları insan kullanıcısı değil, makine aktörüdür. İnsan aktör gerektiren rotalar servis hesaplarını açıkça reddetmelidir.

### İlişki taşması

İlişki tabanlı erişim; kiracı, özne, ilişki türü, kaynak türü ve kaynak kimliği bağlamına bağlı olmalıdır. Genel ilişki etiketleri tek başına yeterli değildir.

### Hassas alan görünürlüğü

Rota yetkilendirmesi tek başına yeterli değildir. Bir kullanıcı kaynağa erişebilir ama kişisel, gizli veya güvenliğe duyarlı alanları görme hakkına sahip olmayabilir.

### Denetim tutarlılığı

Denetim ve güvenlik kayıtları dayanıklı iş kuyruğu üzerinden işlenir. Denetim geçmişi, uygulama seviyesinde kurcalamayı belli edecek şekilde tasarlanır.

## Güvenlik İnceleme Kuralı

Bir değişiklik şu durumlarda daha dikkatli incelenmelidir:

- kiracı yalıtımını zayıflatıyorsa
- varsayılan olarak daha fazla veri gösteriyorsa
- istemci girdisine daha fazla güveniyorsa
- merkezi yetkilendirmeyi atlıyorsa
- denetim görünürlüğünü azaltıyorsa
- hata durumlarını daha belirsiz hale getiriyorsa

## Bu Model Ne İddia Etmez?

Bu case study; dış güvenlik sertifikası, canlı üretim kullanımı, mutlak denetim değişmezliği veya eksiksiz operasyon olgunluğu iddia etmez.

Daha doğru iddia şudur: özel prototip gerçek backend güvenlik sınırlarını araştırdı ve bu sınırların üretim öncesi nasıl doğrulanması gerektiğini belgeledi.
