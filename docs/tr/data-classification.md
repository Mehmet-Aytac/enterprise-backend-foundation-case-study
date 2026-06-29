# Data Classification

Bu doküman, private enterprise backend foundation içinde ele alınan response-minimization ve field-projection strategy'yi özetler.

## Core Idea

Private prototype her response field'ı aynı güvenlik seviyesinde kabul etmez.

Route authorization şu soruyu cevaplar:

> Bu principal bu resource'a erişebilir mi?

Response projection ikinci bir soruyu cevaplar:

> Bu principal bu resource üzerindeki hangi field'ları görebilir?

Bunlar farklı problemlerdir.

Bir user bir record'a erişebilir; fakat PII, confidential business data, financial details, performance metrics, security metadata veya internal policy state görme hakkına sahip olmayabilir.

## Example Classifications

Private design şu classification'ları ele aldı:

| Classification | Anlam | Default direction |
|---|---|---|
| Public | Broad authenticated veya public display için güvenli alanlar | Genellikle visible |
| Internal | Tenant-internal operational data | Route allow ise visible olabilir |
| Performance | Productivity, review veya manager-visible metrics | Explicit grant olmadan hidden |
| Confidential | Sensitive internal attributes veya business metadata | Explicit grant olmadan hidden |
| PII | Personally identifying veya HR-like information | Explicit grant olmadan hidden |
| Security-sensitive | Audit, incident, session veya policy metadata | Explicit grant olmadan hidden |

Restricted classifications, authenticated principal explicit grants'e sahip değilse fail closed davranmalıdır.

## Why This Matters

Yaygın backend hatalarından biri controller'lardan raw ORM objects döndürmektir.

Bu, yanlışlıkla şu tür alanları expose edebilir:

- internal security metadata
- confidential attributes
- employee identifiers
- PII
- financial details
- performance data
- tenant governance settings
- implementation-specific error details

Private prototype bu pattern'den kaçınmak için response-shaping ve field-projection concepts kullandı.

## Projection Strategy

Intended strategy:

1. Route authorization principal'ın resource'a erişip erişemeyeceğine karar verir.
2. Response projection hangi fields'ın dönebileceğine karar verir.
3. Restricted fields explicit permissions veya grants gerektirir.
4. Sensitive data için include groups request-by-request authorize edilir.
5. OpenAPI response schemas projected response contract ile eşleşmelidir.
6. Testler restricted fields'ın grant olmadan hidden, grant ile visible olduğunu kanıtlamalıdır.

## Example

Bir tenant user user listesi okuma permission'ına sahip olabilir:

```text
users.read
```

Bu permission display name, status ve role summary gibi safe operational fields döndürebilir.

Aynı user personal, confidential, security-sensitive veya internal governance fields'ı otomatik olarak almamalıdır.

Bu alanlar daha dar permission'lar gerektirmelidir:

```text
users.read.pii
users.read.attributes
security-events.read.details
```

Exact permission names implementation detail'dır. Önemli pattern şudur: broad route access, broad sensitive-field access'e dönüşmemelidir.

## Review Checklist

Bir response field expose edilmeden önce şu sorular cevaplanmalıdır:

- Bu field hangi classification'a sahip?
- Bu field default olarak safe mi?
- Hangi permission veya role bu field'ı okuyabilir?
- Tenant data, PII, HR data, financial data, security details veya internal governance state leak edebilir mi?
- OpenAPI schema possible response shape'i açıklıyor mu?
- Grant olmadan hidden olduğunu kanıtlayan test var mı?
- Error handling internal implementation details expose etmiyor mu?

## Portfolio Takeaway

Ana ders: authorization tek başına yeterli değildir.

Business applications, özellikle internal tools ve multi-tenant systems içinde backend response minimization'a da ihtiyaç duyar. Böylece broad operational access, otomatik olarak broad sensitive-data access'e dönüşmez.
