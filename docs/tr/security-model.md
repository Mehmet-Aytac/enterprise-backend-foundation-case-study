# Security Model

Bu doküman, private enterprise backend foundation içinde ele alınan security model'i özetler.

Bu bir case-study summary'dir; third-party security audit, compliance certificate, penetration-test report veya live enterprise production usage iddiası değildir.

## Security Goals

Private proje şu hedefler etrafında tasarlandı:

- explicitly authorized system-administration path dışında cross-tenant data access'i engellemek
- authentication ve authorization ayrımını korumak
- authorization'ı deterministic, centralized ve deny-by-default yapmak
- browser cookie sessions'ı CSRF-style mutation attempts'e karşı korumak
- sessions'ı revocable ve application process dışında persisted tutmak
- token reuse, suspicious login, failed credentials ve security-sensitive lifecycle events'i görünür yapmak
- audit durability'yi outbox worker ile korumak
- audit history'yi application-level tamper-evident hale getirmek
- trusted proxy chain açıkça configure edilmedikçe client-supplied proxy/IP headers'a güvenmemek
- responses ve logs içinde sensitive data exposure'ı minimize etmek

## Protected Assets

Private design şu varlıkları protected assets olarak ele aldı:

- user credentials ve password hashes
- browser session tokens
- access ve refresh tokens
- token lifecycle state
- MFA secrets ve recovery codes
- password-reset material
- tenant data ve tenant governance settings
- organization hierarchy ve membership assignments
- role, permission, policy ve relationship grants
- service-account credentials ve permission assignments
- audit logs, security events ve outbox records
- PII, confidential business attributes, HR-like fields, performance data ve security-sensitive metadata

## Actor Types

Design şu actor türlerini dikkate aldı:

- anonymous internet client
- authenticated tenant user
- tenant administrator
- system administrator
- browser client using cookie transport
- API/mobile client using bearer transport
- machine credential kullanan service account
- outbox worker process
- database migration process

## Trust Boundaries

Önemli trust boundary'ler:

- internet/client boundary into Express API
- reverse proxy boundary into application process
- API request path into Prisma/PostgreSQL
- API request path into audit/security outbox
- outbox worker into durable audit/security tables
- browser cookie transport into CSRF-protected mutation routes
- bearer token transport into token/session verification
- service-account credential boundary into machine-principal handling

## Major Threats And Controls

### Cross-tenant access

Tenant boundaries first-order security boundary olarak ele alındı.

Tenant-owned records, request body içindeki tenant IDs yerine server-derived tenant context ile erişilmelidir. Business-level permissions, target object başka tenant'a aitse güvenli değildir.

### CSRF against browser sessions

Browser cookie flows mutating requests için CSRF protection gerektirir. Bearer-only API/mobile clients, ambient browser cookies kullanmadığı için browser CSRF checks'e zorlanmamalıdır.

### Token replay ve refresh-token reuse

Refresh tokens yalnızca hash olarak persisted edilmeli ve kullanımda rotate edilmelidir.

Reuse attempts sınıflandırılır; benign parallel races credential üretmeden reddedilebilir, suspicious veya repeated reuse affected session family'yi revoke edebilir.

### Weak or stale sessions

Sessions DB-backed ve revocable'dır. Sensitive operations, step-up authentication ile daha yüksek session trust gerektirebilir.

### Machine identity confusion

Service accounts machine principal'dır, human user değildir.

Human-only routes service-account principals'ı açıkça reddetmelidir. Sensitive service-account permissions ekstra guardrail gerektirir; çünkü machine credentials ve human sessions farklı risk taşır.

### Relationship overreach

Relationship-based access exact tenant, subject, relation type, resource type ve resource ID'ye bağlı olmalıdır.

“Manager” gibi genel bir ilişki etiketi tek başına yeterli değildir. Permission decision hangi resource'a erişildiğini bilmelidir.

### PII ve sensitive-field leakage

Route authorization tek başına yeterli değildir.

Bir user resource'a erişebilir ama PII, confidential attributes, internal metadata, performance metrics veya security details görme hakkına sahip olmayabilir. Bu alanlar explicit grants olmadan fail closed davranmalıdır.

### Audit loss veya audit inconsistency

Audit ve security writes durable outbox processing üzerinden akar.

Audit logs, application-level threat model içinde historical modification veya deletion tespit edilebilsin diye per-tenant hash-chain append strategy ile tasarlanır.

## Security Review Rule

Bir değişiklik şu durumlarda security-breaking gibi ele alınmalıdır:

- tenant isolation zayıflıyorsa
- default olarak daha fazla data expose ediyorsa
- daha fazla client input'a güveniyorsa
- credential handling zayıflıyorsa
- centralized authorization bypass ediliyorsa
- audit/security visibility kaldırılıyorsa
- failure modes daha belirsiz hale geliyorsa
- browser-cookie ve API-token davranışı karışıyorsa
- fail-closed path fail-open hale geliyorsa

Bu değişiklikler compensating control ve targeted regression tests gerektirir.

## What This Model Does Not Claim

Bu case study şunları iddia etmez:

- external security certification
- independent penetration testing
- compliance approval
- absolute audit immutability
- database, application code, backups ve logs tamamen ele geçirilmişse tam integrity guarantee
- complete production operational maturity

Daha doğru iddia şudur: private prototype gerçek backend security boundaries'i araştırdı ve bu boundary'lerin production öncesi nasıl validate edilmesi gerektiğini belgeledi.
