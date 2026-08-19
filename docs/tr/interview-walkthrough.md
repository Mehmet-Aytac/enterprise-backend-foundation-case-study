# Görüşme Anlatım Rehberi

Bu doküman, private full-stack ERP/SaaS projesini teknik görüşmelerde kaynak kod açmadan anlatmak için güvenli ve dürüst bir rehberdir.

Amaç; private implementation'ı açmadan mühendislik çalışmasını anlaşılır kılmak ve bitmemiş işleri tamamlanmış gibi göstermemektir.

## 1. Önce Dürüst Kapsamı Söyle

İlk çerçeve:

```text
Proje multi-tenant backend foundation olarak başladı ve aktif full-stack ERP/SaaS uygulamasına dönüştü. Implementation private. Bu public repository seçilmiş architecture, validation evidence, frontend integration patterns, trade-off'lar ve limitations'ı belgeliyor. Proje active development; bitmiş production product değil.
```

Bu anlatım, projenin artık backend-only olmadığını baştan doğru şekilde açıklar.

## 2. Full-Stack Problemi Açıkla

Projenin cevap aradığı soruyu şöyle anlatabilirsin:

```text
Browser UI, API contracts, tenant context, permissions, sensitive data, domain workflows, auditability ve deployment rules sistem büyürken nasıl tutarlı tutulabilir?
```

Basit anlatımı: frontend ve backend zamanla birbirinden kopmuş, farklı kurallara sahip iki ayrı uygulamaya dönüşmemeli.

## 3. Runtime Yapısını Açıkla

README'deki architecture diagram'a referans ver.

Kısa anlatım:

```text
React frontend küçük bir Fetch client üzerinden credentialed request gönderiyor. Express backend session veya token'ı çözüyor, trusted tenant/request context oluşturuyor, authorization değerlendiriyor, tenant-scoped domain logic çalıştırıyor, Prisma/PostgreSQL üzerinden veriyi işliyor ve gerektiğinde audit/security evidence'ı outbox yoluna yazıyor.
```

Ana fikir:

```text
Frontend permissions ve capabilities ile UX'i şekillendirebilir; ancak tenant boundaries, access decisions, sensitive response fields, validation ve business invariants için backend authoritative kalır.
```

## 4. Frontend Mimarisini Açıkla

Kısa özet:

```text
Frontend ayrı Vite + React + TypeScript workspace. TanStack Router application routing'i, TanStack Query server-state ve mutation'ları, i18next system localization'ı yönetiyor. Radix Primitives accessible behavior-heavy controls sağlıyor. Browser/API integration küçük project-owned Fetch client üzerinden yapılıyor.
```

Söylenebilecek gerçek implementation örnekleri:

- lazy-loaded ERP routes
- permission-aware route/navigation shaping
- query/mutation hooks ve cache invalidation
- Business Partners list/detail/create flows
- Customer Accounts, Catalog, Sales, Dashboard ve Settings feature boundaries
- Turkish/English localized system text
- session-cookie + CSRF-aware API requests
- gerçek login/session/bootstrap integration smoke path

Frontend'in hâlâ aktif geliştirme aşamasında olduğunu ve bütün planlanan ERP yüzeylerinin tamamlanmadığını açıkça belirt.

## 5. Browser Authentication ve CSRF'yi Açıkla

Kısa anlatım:

```text
Browser authentication session-cookie tabanlı. Cookie-authenticated mutating request'lerde CSRF protection gerektiği için frontend HTTP client gerektiğinde CSRF token alıyor ve mutation request'ine ekliyor. Browser auth modelinde localStorage token'ları default yaklaşım olarak kullanmıyorum.
```

Neden önemli:

```text
Bu, backend security kararının frontend implementation'ı doğrudan değiştirdiği iyi bir örnek.
```

## 6. Tenant Isolation'ı Açıkla

Kısa anlatım:

```text
Multi-tenant sistemde tenant boundary normal filtre değil, security boundary. Tenant context authenticated server-side request state'ten gelir. Client başka tenant'a ait bir object ID gönderse bile tenant-owned read/write işlemleri scope dışına çıkmamalıdır.
```

Günlük benzetme:

```text
Apartman numarasını bilmek başka dairenin kapısını açmaya yetmemeli.
```

## 7. Authorization'ı Açıkla

Kısa anlatım:

```text
Backend centralized deny-by-default authorization yaklaşımında RBAC, ABAC, ReBAC ve PBAC kavramlarını kullanıyor. Route gerekli access'i bildiriyor ve permission engine trusted server-derived facts ile karar veriyor.
```

Frontend bağlantısı:

```text
Frontend kullanılmayan action'ları UX için gizleyebilir veya disable edebilir; ama bu authorization değildir. Server tekrar kontrol eder.
```

Günlük benzetme:

```text
Client izin kağıdını gösterebilir ama kendi izin kağıdına not veremez.
```

## 8. Server State ve Feature Boundaries'i Açıkla

Kısa anlatım:

```text
ERP ekranları listeler, filters, detail records, permissions, generated identifiers ve commands'i aynı anda taşır. TanStack Query server-state'i açık ve izlenebilir tutuyor; feature folders ise domain-specific UI ve adapter'ların tek global component katmanına dönüşmesini önlüyor.
```

Somut örnek olarak Business Partners kullanılabilir; çünkü role-aware lists, filtering, permission-sensitive create behavior, dialogs, mutations ve cache invalidation aynı feature içinde birleşiyor.

## 9. Response Minimization'ı Açıkla

Kısa anlatım:

```text
Bir route'a erişebilmek response içindeki bütün alanları görebilmek anlamına gelmez. Sensitive fields ayrı projection kurallarıyla kontrol edilir.
```

Frontend etkisi:

```text
Sensitive projection rules cache ve UI design'ını da etkiler. Daha yüksek permission ile alınabilen restricted data, sıradan base record gibi geniş cache'e yayılmamalıdır.
```

Günlük benzetme:

```text
Binaya girebilmek içerideki bütün dosya dolaplarını açabilmek anlamına gelmez.
```

## 10. Auditability'yi Açıkla

Kısa anlatım:

```text
Audit logs ve security events ayrı kavramlar. Önemli evidence durable outbox worker üzerinden işlenebiliyor. Audit entries per-tenant hash-chain ile application-level tamper evidence sağlıyor.
```

Mutlaka sınırı ekle:

```text
Tamper-evident, immutable demek değildir. Daha güçlü guarantees için protected backups veya external log anchoring gibi operational controls gerekir.
```

## 11. Validation Çalışmasını Açıkla

Kısa anlatım:

```text
Private proje code-contract validation, fresh-database runs, integration behavior, tenant-boundary attempts, response-leak checks, concurrency-sensitive flows, audit hash-chain verification, CI/platform checks ve deployment-oriented review ile kontrol ediliyor. Frontend tarafında da gerçek browser/session/bootstrap integration smoke path bulunuyor; daha geniş frontend test coverage gelişmeye devam ediyor.
```

Dürüst sınır:

```text
Bunlar private/internal engineering checks; external audit veya certification değil.
```

## 12. AI Desteğini Profesyonel Açıkla

Örnek ifade:

```text
AI araçları implementation support, generation, review, hardening ve documentation aşamalarında kullanıldı. Benim rolüm requirements tanımlamak, architecture değerlendirmek, implementation behavior incelemek, validation komutlarını çalıştırmak, sonuçları yorumlamak, edge case'leri bulmak, kararları belgelemek ve hardening yönünü belirlemekti.
```

Önemli olan kullanılan araçları saklamak değil, kararları anlayıp sahiplenebilmektir.

## 13. Sorulmasını İsteyebileceğin Konular

Bu proje şu konuları konuşmak için uygundur:

- internal ERP için React/frontend architecture
- TanStack Query ve server-state boundaries
- browser session cookies ve CSRF
- frontend/backend API contracts
- tenant isolation failures
- centralized vs local authorization
- permission-aware UX ile gerçek authorization farkı
- sensitive field projection ve frontend caching
- audit outbox design
- concurrency-sensitive auth flows
- validation strategy
- production-readiness boundaries
- AI-assisted engineering workflow ve review discipline

## 14. Dikkatli Cevaplanması Gereken Sorular

### Production'da çalışıyor mu?

```text
Hayır. Active development ve engineering goals açısından production-oriented; ancak live production system olarak sunulmuyor.
```

### Frontend tamamlandı mı?

```text
Hayır. Routing, server-state patterns, API/session integration, localization ve birden fazla ERP feature boundary içeren gerçek React frontend var; ancak broader workflows ve production-grade frontend validation hâlâ geliştiriliyor.
```

### Public repository çalıştırılabilir mi?

```text
Hayır. Public repository case study. Full backend ve frontend implementation private.
```

### External audit yapıldı mı?

```text
Hayır. Validation private proje içinde yapıldı ve burada belgelendi. Daha güçlü production veya certification claim'leri için external review gerekir.
```

## 15. Kapanış Cümlesi

```text
Bu projenin en güçlü yanı iki tarafın birbirine bağlanması. Backend beni tenant boundaries, auth, authorization, sensitive responses, audit evidence, concurrency ve deployment constraints üzerine düşünmeye zorladı. React frontend geliştirmek ise bu contract'ların gerçek browser flows, server-state management, permission-aware UX, localization ve ERP workflows içinde çalışmasını gerektirdi. Proje hâlâ active development ama frontend ve backend'i ayrı demolar olarak değil, sistemi uçtan uca düşünme konusunda gerçek pratik sağlıyor.
```
