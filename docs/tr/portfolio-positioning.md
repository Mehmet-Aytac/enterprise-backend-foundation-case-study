# Portfolyo Konumlandırma

Bu doküman, alttaki private proje backend foundation'dan aktif full-stack ERP/SaaS platformuna genişledikten sonra projenin CV, LinkedIn, GitHub ve teknik görüşmelerde nasıl dürüst biçimde konumlandırılacağını açıklar.

## İsimlendirme Bağlamı

Public repository adı tarihsel bir isimdir: repo oluşturulduğunda portfolyo artifact yalnızca backend foundation'ı belgeliyordu.

Private proje artık gerçek bir React frontend içerdiği için "backend only" şeklinde anlatılmamalıdır. Ancak public repository hâlâ backend tarafında daha fazla deep-dive doküman içerir. En doğru ayrım:

- **alttaki proje**: aktif full-stack ERP/SaaS platformu
- **public repository**: güçlü backend odağı olan, frontend gelişimini de belgeleyen architecture/validation case study

## Önerilen Proje Başlıkları

CV veya proje bölümü için doğru seçenekler:

```text
Full-Stack Multi-Tenant ERP / SaaS Platform
```

```text
Enterprise ERP Platform — React + Node.js / TypeScript
```

```text
Full-Stack ERP Platform — Private Implementation / Public Architecture Case Study
```

Özellikle bu public repository'den söz ediliyorsa şu da doğrudur:

```text
Enterprise Backend Foundation Case Study — Full-Stack Project Evolution
```

Kaçınılması gereken örnekler:

```text
Production ERP System
```

```text
Production-Certified Enterprise SaaS
```

```text
Open-Source Full-Stack ERP Framework
```

Bunlar güncel durumu olduğundan ileri gösterir.

## En İyi Kısa Tanım

```text
React/TypeScript frontend ve security-focused Node.js/Express/PostgreSQL backend ile geliştirilen private-source, active-development full-stack ERP/SaaS platformu. Public case-study repository; architecture decisions, backend security/tenancy controls, frontend integration patterns, validation strategy, trade-off'lar ve güncel sınırları belgeler.
```

## CV İçin Önerilen Sürüm

```text
Full-Stack Multi-Tenant ERP / SaaS Platform
React, TypeScript, Vite, TanStack Router, TanStack Query, Node.js, Express, PostgreSQL, Prisma, Zod, OpenAPI, Docker

Private-source full-stack ERP/SaaS platformunu aktif olarak geliştiriyorum. Frontend; React, TypeScript, Vite, TanStack Router/Query, i18n, erişilebilir Radix primitives ve browser/API integration için project-owned Fetch client kullanıyor. Backend; multi-tenant isolation, session/JWT authentication, centralized authorization, validation, audit/security processing, OpenAPI contracts, integration/security testing, Docker deployment assets ve CI validation sağlıyor. Full implementation private kalırken public repository seçilmiş architecture ve engineering evidence'ı belgeliyor.
```

## CV Bullet Seçenekleri

İlana göre yalnızca uygun olanları kullan.

### Full-stack odaklı

```text
- React, TypeScript, Vite, TanStack Router/Query, Node.js, Express, Prisma ve PostgreSQL ile multi-tenant ERP/SaaS platformunu aktif olarak geliştiriyorum.
- Localized UI, accessible interaction primitives, server-state query/mutation patterns ve browser-safe API integration içeren permission-aware ERP frontend akışları geliştirdim.
- Frontend browser akışlarını session-cookie authentication, CSRF-aware mutating requests, backend bootstrap context ve normalized API errors ile entegre ettim.
- Business Partners, Customer Accounts, Catalog, Sales, Settings ve reusable application infrastructure etrafında frontend feature boundaries geliştirdim.
```

### Backend odaklı

```text
- ERP/internal business applications için tenant isolation, secure authentication flows ve service-account boundaries içeren multi-tenant backend foundation tasarladım.
- RBAC, ABAC, PBAC ve ReBAC kavramlarını deny-by-default decisions ve server-derived access facts ile merkezi authorization modelinde birleştirdim.
- Audit/security event separation, durable outbox processing ve tamper-evident hash-chain verification içeren auditability patterns geliştirdim.
- Integration, abuse-case, concurrency, response-leak, OpenAPI, CI ve deployment-readiness kontrollerini kapsayan validation evidence sürdürdüm.
```

### Public evidence / portfolyo odaklı

```text
- Full implementation'ı future commercial/domain-specific reuse için private tutarken public architecture ve engineering case study yayınladım.
- Bitmemiş frontend veya production işlerini tamamlanmış gibi göstermemek için implemented behavior ile planned work'ü ayrı belgeledim.
```

## LinkedIn / GitHub Pin Tanımı

```text
Private, active-development full-stack ERP/SaaS platformu için public architecture ve engineering case study: React/TypeScript frontend + security-focused multi-tenant Node.js/PostgreSQL backend.
```

## Teknik Görüşme Çerçevesi

Güçlü ve dürüst bir anlatım:

```text
Proje multi-tenant backend foundation olarak başladı ve daha sonra full-stack ERP uygulamasına dönüştü. Implementation private; bu public repository seçilmiş architecture, validation ve engineering evidence'ı belgeliyor. Backend tarafında tenant boundaries, authentication, authorization, sensitive responses, auditability ve deployment hardening üzerinde yoğunlaştım. Frontend tarafında gerçek ERP workflows, TanStack Router/Query, localization, accessible UI primitives ve browser-safe session/API integration etrafında operasyonel React uygulamasını geliştiriyorum. Proje active development; bitmiş production product değil.
```

## Backend Developer İlanlarında Vurgula

- Node.js / TypeScript / Express
- PostgreSQL / Prisma
- REST/OpenAPI contracts
- authentication/session/token flows
- tenant isolation
- centralized authorization
- validation ve error handling
- integration/security testing
- Docker/CI/deployment readiness

## Full Stack Developer İlanlarında Ek Olarak Vurgula

- React + TypeScript
- Vite
- TanStack Router
- TanStack Query / server-state management
- API integration
- cookie/session + CSRF browser flows
- i18n
- accessible UI primitives
- feature/module boundaries
- frontend/backend contract thinking

## Abartılmaması Gerekenler

Şunlar iddia edilmemelidir:

- live production customer usage
- external certification veya audit
- tamamen bitmiş ERP
- bütün backend modülleri için complete frontend coverage
- public runnable source
- planlanan her frontend architecture maddesinin bugün implemented olduğu

## Doğru Status İfadeleri

Kullanılabilecek ifadeler:

```text
active development
full-stack ERP/SaaS platform
production-oriented engineering goals
private-source implementation
public architecture and engineering case study
security-focused multi-tenant backend
operational React frontend under active development
```

Kaçınılacak ifadeler:

```text
production-ready
production-certified
fully complete ERP
used by enterprise customers
open-source framework
fully implemented frontend architecture
```

## Neden Güçlü Bir Portfolyo Projesi?

Değer artık yalnızca backend architecture veya yalnızca görünür frontend değildir. En güçlü sinyal iki tarafın birbirine bağlanmasıdır:

- browser session behavior backend auth rules ile uyumlu olmak zorunda
- CSRF API client design'ını etkiliyor
- permissions UI'ı şekillendiriyor ama security authority olmuyor
- tenant/branch context routing, queries ve server decisions'ı etkiliyor
- API contracts frontend types ve workflows'u etkiliyor
- sensitive response rules caching ve UI exposure kararlarını etkiliyor
- domain workflows frontend ve backend boundaries'i aynı gerçeklikte buluşturuyor

Bu, frontend ve backend'i birbirinden bağımsız demo projeleri gibi ele almaktan daha gerçekçi full-stack product engineering kanıtıdır.
