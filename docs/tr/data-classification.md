# Veri Sınıflandırma

Bu doküman, özel backend altyapı temelinde ele alınan yanıt sadeleştirme (response minimization) ve alan filtreleme (field projection) yaklaşımını özetler.

## Temel Fikir

Özel prototip, her yanıt alanını aynı güvenlik seviyesinde kabul etmez.

Rota yetkilendirmesi (route authorization) şu soruyu cevaplar:

> Bu aktör (principal) bu kaynağa erişebilir mi?

Alan filtreleme ise ikinci bir soruyu cevaplar:

> Bu aktör bu kaynak üzerindeki hangi alanları görebilir?

Bunlar farklı problemlerdir.

Bir kullanıcı bir kayda erişebilir; fakat kişisel veri (PII), gizli iş verisi (confidential business data), finansal ayrıntılar, performans ölçümleri, güvenliğe duyarlı üstveri veya iç politika bilgilerini görme hakkına sahip olmayabilir.

## Örnek Sınıflandırmalar

Özel tasarım şu sınıflandırmaları ele aldı:

| Sınıflandırma | Anlam | Varsayılan yaklaşım |
|---|---|---|
| Herkese açık | Geniş gösterim için güvenli alanlar | Genellikle görünür |
| İç kullanım | Kiracı içi operasyonel veri | Rota izni varsa görünebilir |
| Performans | Üretkenlik, değerlendirme veya yönetici görünürlüğündeki ölçümler | Açık izin olmadan gizli |
| Gizli | Hassas iç alanlar veya iş üstverisi | Açık izin olmadan gizli |
| Kişisel veri | Kişiyi tanımlayan veya İK benzeri bilgi | Açık izin olmadan gizli |
| Güvenliğe duyarlı | Denetim, olay, oturum veya politika üstverisi | Açık izin olmadan gizli |

Kısıtlı sınıflandırmalar, kimliği doğrulanmış aktör açık izne sahip değilse güvenli biçimde gizli kalmalıdır.

## Neden Önemli?

Yaygın backend hatalarından biri controller katmanından ham ORM nesneleri döndürmektir.

Bu, yanlışlıkla şu tür alanları açığa çıkarabilir:

- iç güvenlik üstverisi
- gizli alanlar
- çalışan tanımlayıcıları
- kişisel veri
- finansal ayrıntılar
- performans verisi
- kiracı yönetişim ayarları
- uygulama içi hata ayrıntıları

Özel prototip bu örüntüden kaçınmak için yanıt şekillendirme ve alan filtreleme yaklaşımını kullandı.

## Alan Filtreleme Stratejisi

Hedeflenen strateji:

1. Rota yetkilendirmesi, aktörün kaynağa erişip erişemeyeceğine karar verir.
2. Alan filtreleme, hangi alanların yanıta dönebileceğine karar verir.
3. Kısıtlı alanlar açık izin veya rol gerektirir.
4. Hassas veri grupları her istek için ayrıca yetkilendirilir.
5. OpenAPI yanıt şemaları, filtrelenmiş yanıt sözleşmesiyle uyumlu olmalıdır.
6. Testler, kısıtlı alanların izin yokken gizli ve izin varken görünür olduğunu kanıtlamalıdır.

## Örnek

Bir kiracı kullanıcısı kullanıcı listesini okuma iznine sahip olabilir:

```text
users.read
```

Bu izin; görünen ad, durum ve rol özeti gibi güvenli operasyonel alanları döndürebilir.

Aynı kullanıcı kişisel, gizli, güvenliğe duyarlı veya iç yönetişim alanlarını otomatik olarak almamalıdır.

Bu alanlar daha dar izinler gerektirmelidir:

```text
users.read.pii
users.read.attributes
security-events.read.details
```

Tam izin adları uygulama detayıdır. Önemli örüntü şudur: geniş rota erişimi, geniş hassas alan erişimine dönüşmemelidir.

## İnceleme Kontrol Listesi

Bir yanıt alanı açığa çıkarılmadan önce şu sorular cevaplanmalıdır:

- Bu alan hangi sınıflandırmaya sahip?
- Bu alan varsayılan olarak güvenli mi?
- Hangi izin veya rol bu alanı okuyabilir?
- Kiracı verisi, kişisel veri, İK verisi, finansal veri, güvenlik ayrıntısı veya iç yönetişim bilgisi sızdırabilir mi?
- OpenAPI şeması olası yanıt şeklini açıklıyor mu?
- İzin olmadan gizli kaldığını kanıtlayan test var mı?
- Hata yönetimi uygulama içi ayrıntıları açığa çıkarmıyor mu?

## Portfolyo Çıkarımı

Ana ders: yetkilendirme tek başına yeterli değildir.

İş uygulamalarında, özellikle iç araçlarda ve çok kiracılı sistemlerde backend ayrıca yanıt sadeleştirmeye ihtiyaç duyar. Böylece geniş operasyonel erişim, otomatik olarak geniş hassas veri erişimine dönüşmez.
