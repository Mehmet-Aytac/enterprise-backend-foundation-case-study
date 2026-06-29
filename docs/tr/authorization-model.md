# Yetkilendirme Modeli

Bu doküman, özel backend altyapı temelinde ele alınan yetkilendirme (authorization) modelini özetler.

Amaç, erişim kararlarını merkezi hale getirmek ve izin mantığının controller veya servis katmanlarına dağılmasını engellemektir.

## Temel İlke

Yetkilendirme şu ilkelerle tasarlandı:

- açık
- merkezi
- belirleyici
- varsayılan olarak reddeden
- kiracı farkındalığı olan
- denetlenebilir
- istemci iddiaları yerine sunucu tarafından doğrulanan bilgilere dayanan

Rota hangi izni istediğini bildirir. Yetki karar motoru (permission engine), kimliği doğrulanmış aktörün hedef kaynak üzerinde bu işlemi yapıp yapamayacağına karar verir.

Gündelik benzetmeyle: controller kendi yerel tahminleriyle güvenlik görevlisi gibi davranmamalı; her rota aynı ana güvenlik kapısından geçmelidir.

## Karar Girdileri

Güvenli yetkilendirme kararı güvenilir girdiler gerektirir.

Tipik girdiler:

- aktör türü: insan kullanıcı, servis hesabı veya sistem aktörü
- kiracı bağlamı
- istenen kaynak türü ve eylem
- rota izin anahtarı
- sahiplik önemliyse kaynak sahibi kullanıcı kimliği
- şube, departman, ekip, bölge, konum veya özel boyut bilgileri
- kaynak sınıflandırması veya hassasiyet seviyesi
- ilişki tabanlı erişim için ilişki bilgileri
- politika tabanlı erişim için kiracı politikaları
- hassas işlemler için oturum güven düzeyi

Önemli kural: kaynak bilgileri sunucu tarafında doğrulanmalıdır. İstemciden gelen sahiplik, kiracı kimliği, şube kodu, sınıflandırma veya ilişki bilgisi yetkilendirme için güvenilir kabul edilmez.

## Erişim Kontrol Yaklaşımları

Özel prototip tek bir izin tarzı yerine birleşik bir model araştırdı.

### Rol Tabanlı Erişim Kontrolü (RBAC)

Rol tabanlı erişim kontrolü, rollerin izinlerle eşleştirilmesi için kullanılır.

Örnek fikirler:

- sahip rolü kiracı ayarlarını yönetebilir
- denetçi rolü denetim kayıtlarını okuyabilir
- operatör rolü operasyonel kayıtları güncelleyebilir

RBAC temel izin modelini sağlar.

### Öznitelik Tabanlı Erişim Kontrolü (ABAC)

Öznitelik tabanlı erişim kontrolü, güvenilir özne/kaynak/istek/kapsam bilgilerine dayalı ek koşullar için kullanılır.

Örnek fikirler:

- kullanıcının yetki seviyesi kaynak sınıflandırmasıyla uyumluysa izin ver
- kiracı yönetişim kuralı gerekli boyuta izin vermiyorsa reddet
- hassas işlem için daha güçlü oturum güveni iste

ABAC girdileri sunucu tarafından doğrulanmış veya onaylanmış özellikler olmalıdır.

### İlişki Tabanlı Erişim Kontrolü (ReBAC)

İlişki tabanlı erişim kontrolü, kaynağa bağlı ilişki kontrolleri için kullanılır.

Örnek fikirler:

- yönetici, astına ait kayıtları okuyabilir
- kullanıcı yalnızca kesin ilişki kaydı varsa kaynağa erişebilir

Güvenlik kuralı: ReBAC kontrolleri kesin kaynak türüne ve kaynak kimliğine bağlı olmalıdır. Geniş ilişki etiketi tek başına yeterli değildir.

### Politika Tabanlı Erişim Kontrolü (PBAC)

Politika tabanlı erişim kontrolü, kiracı seviyesindeki izin/ret kuralları için kullanılır.

Beklenen davranışta ret kuralı izin kuralından önce gelir. Hedef kaynak/eylem için izin politikaları varsa en az bir izin kuralı geçmelidir.

## Kiracı Sınırı Önce Gelir

Kiracı yalıtımı birinci sınıf güvenlik sınırıdır.

Yetki karar motoru, açıkça yetkilendirilmiş sistem yönetimi yolları dışında kiracılar arası erişimi iş seviyesi izin kurallarından önce reddetmelidir.

Çünkü hedef nesne başka kiracıya aitse rol, ilişki veya politika izni güvenli değildir.

## Kapsamlı İzinler

Prototip şu kapsam kısıtlarını destekleyecek şekilde düşünülmüştür:

- yalnızca kendi kayıtları
- yalnızca aynı şube
- yalnızca aynı departman
- yalnızca aynı ekip
- bölge/konum kapsamları
- özel kiracı boyutları
- gerekli ilişkiler
- gerekli sahip ilişkileri
- gerekli oturum güven düzeyi

Önemli ders: kapsamlı izinler, gerekli hedef kaynak bilgisi eksikse güvenli biçimde reddetmelidir.

## Rota Seviyesi Örüntü

Korumalı rota kavramsal olarak şu sırayı izlemelidir:

```text
isteği kimlik doğrulamadan geçir
gerekirse kiracı modülü / özellik durumunu kontrol et
insan aktör gerekiyorsa makine aktörünü reddet
güvenilir kaynak bilgilerini sunucu/veritabanından çöz
merkezi yetki kararını değerlendir
controller/servis mantığını çalıştır
yanıt alanlarını izinlere göre filtrele
gerekiyorsa denetim/güvenlik kaydı oluştur
```

Rota izin mantığını servis metotları içine ikinci ve daha zayıf bir erişim sistemi olarak saklamamalıdır. Servisler kiracıya ait varlık ve tutarlılık kontrolleri yapabilir; ancak yetki karar motorunun yerini almamalıdır.

## Servis Hesabı Sınırları

Servis hesapları kullanıcı değil, makine aktörleri olarak ele alındı.

Tasarım kuralları:

- servis hesapları açık kiracı kapsamlı izinler alır
- servis hesapları insan kullanıcı üyeliklerini devralmaz
- insan aktör gerektiren işlemler servis hesabını reddeder
- hassas izinler servis hesabı için ayrıca açık onay gerektirir
- denetim/güvenlik kayıtları servis hesabını insan kullanıcı gibi göstermez

Bu, API anahtarının sessizce insan yöneticisine dönüşmesini engeller.

## Alan Filtreleme Ayrıdır

Rota yetkilendirmesi, aktörün kaynağa erişip erişemeyeceğine karar verir.

Alan filtreleme (field projection), yanıt içinde hangi alanların dönebileceğine karar verir.

Bu ayrım önemlidir; çünkü bir kullanıcı kayıt listesi okuyabilir ama kişisel veri, gizli alan, finansal veri, performans verisi veya güvenliğe duyarlı üstveri görme hakkına sahip olmayabilir.

## Sertleştirme Dersi

En önemli yetkilendirme dersi şudur: eksik sunucu doğrulamalı bilgiler zararsız kabul edilmemelidir.

Bir izin sahiplik, şube, departman, ekip, ilişki veya sınıflandırmaya bağlıysa rota bu bilgileri güvenilir sunucu verisinden sağlamalıdır. Sağlayamıyorsa karar güvenli biçimde reddetmelidir.

Bu kural, “izin kontrolü var” ile “izin kontrolü güvenilir” arasındaki farktır.
