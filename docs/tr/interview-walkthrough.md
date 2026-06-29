# Interview Walkthrough

Bu doküman, private backend foundation çalışmasını teknik görüşmelerde kod açmadan anlatmak için kısa bir rehberdir.

## 1. Honest Scope

Önce kapsamı dürüstçe söyle:

```text
Bu proje finished product değil; aktif geliştirme aşamasındaki private-source backend foundation çalışmasının public case study dokümantasyonudur.
```

## 2. Problem

Projenin cevap aradığı soru:

```text
Future modules aynı tenant isolation, authentication, authorization, response minimization, auditability, validation ve deployment-oriented runtime davranışlarını nasıl güvenli şekilde paylaşabilir?
```

## 3. Architecture

Kısa anlatım:

```text
Request API'ye gelir, identity resolve edilir, tenant context ve access scope oluşturulur, permission engine route'u değerlendirir, module service tenant-scoped iş yapar ve audit/security evidence outbox'a yazılır.
```

## 4. Permission Engine

Vurgulanacak ana fikir:

```text
Permission engine sadece role check değildir. Principal type, tenant boundary, route permission, scoped grants, relationship checks, policy rules, session trust ve server-derived resource facts birlikte değerlendirilir.
```

## 5. Tenant Isolation

Kısa anlatım:

```text
Tenant boundary normal bir filtre değil, security boundary olarak ele alınır. Tenant context request body'den değil authenticated request state'ten gelir.
```

## 6. Response Minimization

Kısa anlatım:

```text
Route'a erişebilmek, response içindeki tüm field'ları görebilmek anlamına gelmez. Field projection restricted alanları ayrıca kontrol eder.
```

## 7. Auditability

Kısa anlatım:

```text
Audit logs ve security events ayrılır. Önemli kayıtlar outbox worker üzerinden durable hale getirilir. Hash-chain tasarımı application-level tamper evidence sağlar.
```

## 8. AI Assistance

Dürüst açıklama:

```text
AI tools generation, review, hardening ve documentation aşamalarında kullanıldı. Benim rolüm requirements, architecture evaluation, validation review, edge-case thinking, documentation ve hardening direction üzerindeydi.
```

## 9. Closing Pitch

```text
Bu projenin güçlü yanı finished product olması değil; tenant boundaries, permission decisions, response minimization, auditability, validation ve production-readiness limits gibi gerçek backend concerns üzerinde düşünülmüş olmasıdır.
```
