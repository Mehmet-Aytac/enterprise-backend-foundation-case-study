# Terimler ve Yazım Standardı

Bu dosya, Türkçe dokümanlarda kullanılan teknik terimlerin nasıl yazılacağını açıklar.

Amaç, iki dili kontrolsüz şekilde karıştırmak değil; Türkçe anlatımı ana dil olarak korurken sektörde bilinen İngilizce karşılıkları ilk kullanımda parantez içinde vermektir.

## Genel Kural

İlk kullanımda:

```text
Türkçe terim (English term)
```

Sonraki kullanımlarda çoğunlukla yalnızca Türkçe terim kullanılır.

Kod adları, paket adları, komutlar, izin anahtarları ve teknoloji adları çevrilmez.

## Terim Sözlüğü

| Türkçe kullanım | İngilizce karşılık |
|---|---|
| özel kaynak kodlu | private-source |
| herkese açık dokümantasyon | public documentation |
| backend altyapı temeli | backend foundation |
| aktif geliştirme | active development |
| üretim ortamına hazırlık hedefli | production-oriented |
| çalıştırılabilir açık kaynak şablon | runnable open-source starter template |
| kaynak kod | source code |
| mimari karar | architecture decision |
| doğrulama stratejisi | validation strategy |
| tasarım ödünü | trade-off |
| çok kiracılı yapı | multi-tenancy |
| kiracı | tenant |
| kiracı sınırı | tenant boundary |
| kimlik doğrulama | authentication |
| yetkilendirme | authorization |
| yetki karar motoru | permission engine |
| rol tabanlı erişim kontrolü | RBAC |
| öznitelik tabanlı erişim kontrolü | ABAC |
| ilişki tabanlı erişim kontrolü | ReBAC |
| politika tabanlı erişim kontrolü | PBAC |
| servis hesabı | service account |
| makine aktörü | machine principal |
| aktör | principal |
| sunucu tarafından doğrulanan bilgi | server-derived fact |
| kapsam kısıtı | scope constraint |
| ilişki kontrolü | relationship check |
| kiracı politikası | tenant policy |
| oturum güven düzeyi | session trust |
| yanıt sadeleştirme | response minimization |
| alan filtreleme | field projection |
| kişisel veri | PII |
| gizli alan | confidential field |
| güvenliğe duyarlı alan | security-sensitive field |
| denetlenebilirlik | auditability |
| denetim kaydı | audit log |
| güvenlik olayı | security event |
| dayanıklı iş kuyruğu | durable outbox |
| kurcalamayı belli eden kayıt zinciri | tamper-evident hash chain |
| doğrulama matrisi | validation matrix |
| eşzamanlılık | concurrency |
| dağıtım hazırlığı | deployment readiness |

## Çevrilmeyecek İfadeler

Aşağıdakiler teknik kimlik veya kod karşılığı olduğu için genellikle çevrilmez:

- TypeScript, Node.js, Express, PostgreSQL, Prisma, Zod, OpenAPI, Docker
- `users.read`, `sales.orders.create`, `audit-logs.read`
- dosya adları, komutlar ve klasör yolları
- RBAC, ABAC, ReBAC, PBAC gibi yaygın kısaltmalar

## Örnek

Kötü:

```text
Response minimization ve field projection güvenlik için önemlidir.
```

Daha iyi:

```text
Yanıt sadeleştirme (response minimization) ve alan filtreleme (field projection), hassas alanların gereksiz yere dönmesini engeller.
```
