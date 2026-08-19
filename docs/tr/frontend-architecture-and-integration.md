# Frontend Mimari ve Entegrasyon

Bu doküman, private full-stack ERP/SaaS projesinin frontend tarafını açıklar ve **gerçekten uygulanmış davranışlarla** **seçilmiş ancak henüz tamamen bitmemiş mimari işleri** özellikle birbirinden ayırır.

Public repository frontend source code'u içermez. Amaç, private uygulamayı açmadan portfolyo incelemesi için yeterli implementation evidence ve mimari niyeti belgelemektir.

## Güncel Proje Konumu

Proje ilk olarak backend foundation şeklinde başladı. Daha sonra ayrı bir `frontend/` workspace eklendi ve private proje bugün aktif bir full-stack uygulamadır.

Frontend'in güncel hedefi mümkün olan bütün ERP modüllerini aynı anda tamamlamak değildir. Aktif yön, gelecekteki vertical/domain ürünlerin güvenli biçimde kullanabileceği ortak internal-product foundation'ları oluşturmaktır:

- authenticated application shell
- tenant/company ve branch context
- permission-aware navigation
- customer/account workflows
- catalog workflows
- sales quote/order foundations
- preferences/settings
- reusable API, localization, formatting ve UI boundaries

Daha geniş ERP modüllerinin bir bölümü, ortak foundation sağlamlaştırılırken bilinçli olarak deferred veya kısmi yüzey durumunda tutulmaktadır.

## Güncel Frontend Stack

Private implementation şu anda şunları kullanır:

- React
- TypeScript
- Vite
- TanStack Router
- TanStack Query
- Radix Primitives
- i18next / react-i18next
- küçük project-owned HTTP client üzerinden Fetch API
- design-token CSS
- Lucide icons

Seçilmiş mimari ayrıca şunları içerir:

- gerektiğinde local form coercion ve UX validation için Zod
- backend OpenAPI dokümanından türetilmiş TypeScript API types

Son iki madde mimari hedeflerdir; planlanan her integration adımının bugün tamamen uygulanmış olduğu iddiası değildir.

## Uygulama Yapısı

Private frontend tek büyük component tree yerine belirgin uygulama sınırları etrafında düzenlenmektedir.

Temsili alanlar:

```text
frontend/src/
  app/
  domain/
  features/
  i18n/
  shared/
  styles/
  vertical-modules/
```

Amaç; application bootstrap, domain contracts, reusable infrastructure, feature-specific UI, localization ve styling konularını yeterince ayırarak yeni ERP modüllerinin tüm uygulamada zincirleme yeniden yazım gerektirmesini önlemektir.

## Routing

Application route tree için TanStack Router kullanılmaktadır.

Güncel implementation evidence:

- lazy-loaded page components
- route-level loading fallback
- shared application shell
- permission-aware route shaping
- Dashboard, Business Partners, Customer Accounts, Catalog, Sales, Preferences/Settings ve admin foundation sayfaları için route'lar

Frontend UX amacıyla erişilemeyen alanları gizleyebilir veya bloke edebilir; ancak bu güvenlik sınırı olarak kabul edilmez. Backend authorization authoritative kalır.

## Server State

Server-state ve adapter-backed data flows için TanStack Query kullanılmaktadır.

Temsili pattern'ler:

- list/detail data için query hooks
- create/update/delete command'ları için mutation hooks
- query-key scoping
- başarılı mutation sonrasında cache invalidation
- gerektiğinde sensitive projection'lar için kısa ömürlü veya devre dışı caching

Bu ayrım ERP ekranlarında önemlidir; çünkü filtered lists, detail records, permissions, generated identifiers ve command results aynı ekranda birleşebilir. Bunların tamamını sıradan local component state olarak ele almak lifecycle ve cache davranışını zorlaştırır.

## API Client ve Browser Security

Frontend, browser-security davranışını büyük bir SDK arkasına gizlemek yerine küçük project-owned Fetch wrapper kullanır.

Güncel implementation şunları içerir:

- configurable API base URL
- browser session akışları için `credentials: 'include'`
- JSON request/response handling
- normalized API error objects
- cookie-authenticated mutating request'lerden önce CSRF token acquisition
- gerektiğinde mutation request'lerinde `x-csrf-token` gönderimi

Browser application ayrıca integration çalışmaları sırasında gerçek login, session cookie, CSRF ve bootstrap yolunu kullanan internal API-session setup/smoke flow içerir.

Ana kural: client-side state güvenlik otoritesi değildir. Tenant identity, permission checks, sensitive response projection ve business invariants backend-owned kalır.

## Bootstrap ve Context

Application shell; tenant, branch, module, permission ve preference state'ini ilgisiz endpoint'lerden tahmin etmek yerine authenticated bootstrap contract etrafında tasarlanmıştır.

Backend bootstrap response frontend'e şu tür bilgiler sağlar:

- authenticated user identity
- tenant context
- usable branches ve default branch
- enabled modules
- role/permission information
- session/trust timing
- preferences/localization facts

Frontend bu bilgileri navigation ve UX'i şekillendirmek için kullanır. Server-side kontrollerin yerine kullanmaz.

## Localization ve Presentation

User-facing system text, component'ler içine dağılmış hard-coded copy yerine i18next/react-i18next üzerinden yönetilir.

Frontend şu anda Türkçe ve İngilizce locale yapıları içerir.

Styling, design tokens ve project-owned visual rules etrafında kurulmaktadır. İlk MVP'de ağır component platform kullanılmaması bilinçli bir karardır; amaç büyük bir visual framework'e kilitlenmeden önce yoğun ve operasyonel ERP karakterini kendi kurallarıyla oluşturmaktır.

Radix Primitives, dialog gibi erişilebilir etkileşim davranışının düşük seviye implementation detaylarını tekrar üretmekten daha önemli olduğu yüzeylerde kullanılır.

## Temsili Feature Kanıtı

### Business Partners

Business Partners yüzeyi birden fazla frontend concern'ü birlikte gösterir:

- customer/supplier role switching
- list filtering ve sorting
- query-driven data loading
- permission-sensitive create behavior
- detail selection
- accessible create/detail dialogs
- mutation handling ve cache invalidation
- localized system text

Bu nedenle yalnızca statik bir ekran değildir; component state, server-state patterns, permission-aware UX, domain types ve API/adapter boundaries aynı akışta birleşir.

### Customer Accounts, Catalog ve Sales

Private repository içinde Customer Accounts, Catalog ve Sales için de ayrı feature boundaries bulunmaktadır. Aktif development plan bu alanları, ilk reusable commercial/vertical foundation için önemli olduklarından önceliklendirir.

Daha geniş ERP fonksiyonlarının tamamı bitmiş olarak sunulmaz. Purchasing, Inventory, Finance, HR ve diğer uzun vadeli yüzeyler ürün ihtiyacına göre aşamalı ele alınmaktadır.

## Implemented ve Planned Ayrımı

Bu case study için temel kurallardan biri bu iki grubu ayrı tutmaktır.

### Bugün uygulanmış olanlar

- Vite + React + TypeScript workspace
- TanStack Router route tree
- TanStack Query provider ve feature query/mutation patterns
- i18n structure
- design-token styling foundation
- Radix-based accessible interaction surfaces
- project-owned credentialed Fetch client
- CSRF-aware mutation behavior
- browser login/session/bootstrap integration smoke path
- birden fazla ERP alanı için gerçek feature/page boundaries

### Seçilmiş mimari / gelişmeye devam edenler

- complete OpenAPI-derived frontend type generation coverage
- daha geniş Zod-based local form validation coverage
- görünür/deferred bütün modüller için complete API-backed behavior
- tüm akışları kapsayan production-grade frontend test stack
- complete ERP frontend coverage
- external production validation

Bu ayrım bilinçlidir. Proje aktiftir; değerlendirme bitmemiş işleri tamamlanmış gibi göstermeye değil, güncel engineering evidence ve yönün kalitesine dayanmalıdır.

## Bu Frontend Backend Case Study İçin Neden Önemli?

Frontend, projenin niteliğini değiştirdi.

Bir backend mimarisi kendi başına doğru görünebilir; fakat gerçek browser application tarafından güvenli ve kullanışlı biçimde tüketilmesi zor olabilir. Frontend geliştirmek şu integration sorularını gerçek hale getirdi:

- session-cookie behavior
- CSRF
- bootstrap context
- permission-aware navigation
- sensitive projections ve cache behavior
- generated identifiers
- list/detail/query boundaries
- error mapping
- localization
- module enablement
- user ve tenant preferences

Bu, full-stack projeyi daha güçlü bir engineering case study yapar: backend contracts yalnızca teorik olarak belgelenmiyor, gerçek application surface tarafından kullanılmaya başlanıyor.

## Dürüst Sınırlar

Frontend aktif geliştirme aşamasındadır; bitmiş ticari ERP UI değildir.

Bu case study şunları iddia etmez:

- bütün backend modülleri için complete frontend coverage
- gerçek müşterilere production deployment
- external accessibility veya security certification
- complete end-to-end test coverage
- private implementation'ın public reproducibility'si

En doğru kısa tanım: **private multi-tenant ERP backend foundation ile entegre edilen, aktif geliştirilen operasyonel React frontend ve bunu belgeleyen public architecture/engineering case study.**
