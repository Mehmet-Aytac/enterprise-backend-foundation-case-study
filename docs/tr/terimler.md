# Terimler ve Yazım Standardı

Bu dosya, Türkçe dokümanlarda kullanılan teknik terimlerin nasıl yazılacağını açıklar.

Amaç, iki dili kontrolsüz şekilde karıştırmak değil; Türkçe anlatımı ana dil olarak korurken sektörde bilinen İngilizce karşılıkları ilk kullanımda parantez içinde vermektir.

## Genel Kural

İlk kullanımda mümkünse:

```text
Türkçe terim (English term)
```

Sonraki kullanımlarda çoğunlukla yalnızca Türkçe karşılık kullanılmalıdır.

Kod adları, paket adları, komutlar, izin anahtarları, protokol/header adları ve teknoloji adları çevrilmez. Bir İngilizce terimin Türkçe karşılığı anlatımı yapay veya belirsiz hale getiriyorsa, sektördeki yerleşik teknik kullanım korunabilir.

## Terim Sözlüğü

| Türkçe kullanım | İngilizce karşılık |
|---|---|
| özel kaynak kodlu | private-source |
| herkese açık dokümantasyon | public documentation |
| full-stack uygulama / uçtan uca uygulama | full-stack application |
| backend altyapı temeli | backend foundation |
| frontend çalışma alanı | frontend workspace |
| aktif geliştirme | active development |
| üretim ortamına hazırlık hedefli | production-oriented |
| çalıştırılabilir açık kaynak şablon | runnable open-source starter template |
| kaynak kod | source code |
| mimari karar | architecture decision |
| doğrulama stratejisi | validation strategy |
| mühendislik kanıtı | engineering evidence |
| tasarım ödünü | trade-off |
| çok kiracılı yapı | multi-tenancy |
| kiracı | tenant |
| kiracı sınırı | tenant boundary |
| kimlik doğrulama | authentication |
| yetkilendirme | authorization |
| yetki karar motoru | permission engine |
| izin farkındalıklı kullanıcı deneyimi | permission-aware UX |
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
| tarayıcı oturumu | browser session |
| oturum çerezi | session cookie |
| sunucu durumu | server state |
| sorgu anahtarı | query key |
| önbellek geçersizleştirme | cache invalidation |
| bağdaştırıcı | adapter |
| API destekli akış | API-backed flow |
| başlangıç bağlamı / bootstrap bağlamı | bootstrap context |
| yanıt sadeleştirme | response minimization |
| alan filtreleme | field projection |
| hassas alan | sensitive field |
| kişisel veri | PII |
| gizli alan | confidential field |
| güvenliğe duyarlı alan | security-sensitive field |
| denetlenebilirlik | auditability |
| denetim kaydı | audit log |
| güvenlik olayı | security event |
| dayanıklı iş kuyruğu | durable outbox |
| kurcalamayı belli eden kayıt zinciri | tamper-evident hash chain |
| doğrulama matrisi | validation matrix |
| regresyon kontrolü | regression check |
| duman testi | smoke test |
| eşzamanlılık | concurrency |
| uçtan uca test | end-to-end test |
| erişilebilirlik | accessibility |
| dağıtım hazırlığı | deployment readiness |

## Çevrilmeyecek İfadeler

Aşağıdakiler teknik kimlik, paket veya kod karşılığı olduğu için genellikle çevrilmez:

- React, TypeScript, JavaScript, Node.js, Express, PostgreSQL, Prisma, Zod, OpenAPI, Docker
- Vite, TanStack Router, TanStack Query, Radix Primitives, i18next, Lucide
- Fetch API, HTTP, JWT, CSRF, CI/CD
- `credentials: 'include'`, `x-csrf-token`
- `users.read`, `sales.orders.create`, `audit-logs.read`
- dosya adları, komutlar, klasör yolları ve environment variable adları
- RBAC, ABAC, ReBAC, PBAC gibi yaygın kısaltmalar

## Örnekler

Daha zayıf:

```text
Response minimization ve field projection güvenlik için önemlidir.
```

Daha iyi:

```text
Yanıt sadeleştirme (response minimization) ve alan filtreleme (field projection), hassas alanların gereksiz yere dönmesini engeller.
```

Daha zayıf:

```text
Query mutation sonrası cache invalidation yapılıyor.
```

Daha iyi:

```text
Başarılı değişikliklerden sonra ilgili sunucu durumu önbelleği geçersizleştirilir (cache invalidation).
```
