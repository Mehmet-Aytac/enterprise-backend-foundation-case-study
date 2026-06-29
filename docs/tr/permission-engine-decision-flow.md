# Yetki Karar Motoru Akışı

Bu doküman, özel backend altyapı temelindeki yetki karar motorunun (permission engine) kavramsal çalışma şeklini açıklar.

Yetki karar motoru, yetkilendirme (authorization) modelinin merkezidir. Bir isteğin kabul edilip edilmeyeceği yalnızca role bakılarak belirlenmez. Kiracı sınırı, rota izni, kapsam kısıtları, ilişki bilgileri, kiracı politikaları, oturum güven düzeyi ve kaynak bilgileri birlikte değerlendirilir.

Bu public doküman özel kaynak kod paylaşmaz; yalnızca mimari seviyede açıklama yapar.

## Neden Önemli?

Çok kiracılı bir iş uygulamasında her controller kendi yetki kararını vermeye çalışırsa sistem tutarsız hale gelir.

Yetki karar motoru bu yüzden merkezi bir karar noktasıdır. Amaç, aynı tür isteklerin aynı kurallarla değerlendirilmesini sağlamaktır.

## Kararda Kullanılan Bilgiler

| Türkçe terim | İngilizce karşılık | Açıklama |
|---|---|---|
| Aktör | principal | İsteği yapan kullanıcı, servis hesabı veya sistem süreci. |
| Kiracı bağlamı | tenant context | İsteğin hangi kiracı sınırı içinde değerlendirildiği. |
| İzin anahtarı | permission key | Rotanın istediği adlandırılmış izin. |
| Kaynak | resource | Erişilmek istenen kayıt veya iş nesnesi. |
| Kapsam kısıtı | scope constraint | İznin yalnızca belirli şube, ekip, sahiplik veya ilişki için geçerli olması. |
| Sunucu tarafından doğrulanan bilgi | server-derived fact | Yetkilendirme için istemciden değil sunucudan alınan güvenilir bilgi. |
| Kiracı politikası | tenant policy | Kiracı seviyesinde ek izin veya ret kuralı. |
| Oturum güven düzeyi | session trust | İşlem için oturumun yeterince güvenilir olup olmadığı. |

## Karar Sırası

Kavramsal karar sırası şöyledir:

1. İsteği yapan aktör belirlenir.
2. Kiracı sınırı kontrol edilir.
3. Rotanın istediği temel izin kontrol edilir.
4. Kapsam kısıtları değerlendirilir.
5. İlişki ve politika kuralları değerlendirilir.
6. Gerekliyse oturum güven düzeyi kontrol edilir.
7. Sonuç açık bir kabul veya gerekçeli ret olarak üretilir.

## Temel Güvenlik İlkesi

Yetkilendirme için kullanılan sahiplik, şube, ekip, ilişki veya sınıflandırma gibi bilgiler istemcinin gönderdiği değerlere dayanmaz.

Bu bilgiler sunucu tarafından doğrulanmalıdır. Gerekli bilgi yoksa en güvenli davranış isteği kabul etmemektir.

## Alan Filtreleme Ayrıdır

Yetki karar motoru rotaya erişim kararını verir.

Yanıt içinde hangi alanların döneceği ise alan filtreleme (field projection) adımıyla ayrıca belirlenir. Bu konu [Veri Sınıflandırma](./data-classification.md) dokümanında anlatılır.

## Portfolyo Çıkarımı

Profesyonel mesaj yalnızca “role ekledim” değildir.

Daha güçlü mesaj şudur:

> Yetkilendirme modelini kiracı sınırları, sunucu tarafından doğrulanan bilgiler, kapsam kısıtları, ilişki kuralları, kiracı politikaları, oturum güven düzeyi ve ayrı yanıt alanı filtreleme etrafında tasarladım.
