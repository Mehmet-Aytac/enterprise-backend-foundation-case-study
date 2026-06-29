# Authorization Model

Bu doküman, private enterprise backend foundation içinde ele alınan authorization model'i özetler.

Amaç, access decisions'ı centralize etmek ve permission logic'in controller/service içine dağılmasını engellemektir.

## Core Principle

Authorization şu şekilde tasarlandı:

- explicit
- centralized
- deterministic
- deny-by-default
- tenant-aware
- auditable
- client claims yerine server-derived facts temelli

Route hangi permission'ı gerektirdiğini declare eder. Permission engine, authenticated principal'ın target resource üzerinde bu action'ı yapıp yapamayacağına karar verir.

Gündelik benzetmeyle: controller kendi lokal tahminleriyle güvenlik görevlisi gibi davranmamalı; her route aynı ana güvenlik kapısından geçmelidir.

## Decision Inputs

Güvenli authorization decision trusted inputs gerektirir.

Tipik inputs:

- authenticated principal type: human user, service account veya system actor
- tenant context
- requested resource type ve action
- route permission key
- ownership önemliyse resource owner user ID
- branch, department, team, region, location veya custom dimension facts
- resource classification veya sensitivity
- ReBAC için relationship facts
- PBAC için tenant policy rules
- sensitive operations için session trust level

Önemli kural: resource facts server-side derived olmalıdır. Client-supplied ownership, tenant ID, branch code, classification veya relationship data authorization için güvenilir değildir.

## Access-Control Styles

Private prototype tek bir permission style yerine combined model araştırdı.

### RBAC

Role-based access control, role-to-permission assignment için kullanıldı.

Örnek konseptler:

- owner role tenant settings yönetebilir
- auditor role audit logs okuyabilir
- operator role operational records güncelleyebilir

RBAC base grant model sağlar.

### ABAC

Attribute-based access control, trusted subject/resource/request/scope attributes üzerinden ek koşullar için kullanılır.

Örnek konseptler:

- user clearance level resource classification ile uyumluysa allow
- tenant governance rule required dimension'a izin vermiyorsa deny
- sensitive action için stronger session trust gerektir

ABAC inputs server-derived veya approved attributes olmalıdır, arbitrary client-provided values olmamalıdır.

### ReBAC

Relationship-based access control resource-bound relationship checks için kullanılır.

Örnek konseptler:

- manager subordinate'a ait records okuyabilir
- user yalnızca exact relationship tuple varsa resource'a erişebilir

Safety rule: ReBAC checks exact resource type ve resource ID'ye bağlı olmalıdır. Broad relationship label yeterli değildir.

### PBAC

Policy-based access control tenant-level allow/deny policy rules için kullanılır.

Beklenen davranış deny-overrides'tır. Matching deny policy varsa allow'dan önce kazanır. Target resource/action için allow policies varsa allow gate geçmelidir.

## Tenant Boundary First

Tenant isolation first-order security boundary'dir.

Permission engine, explicitly authorized system-administration paths dışında cross-tenant access'i business-level permission rules öncesinde reddetmelidir.

Çünkü role, relationship veya policy grant target object başka tenant'a aitse güvenli değildir.

## Scoped Permissions

Prototype şu organization-style scope constraints'i destekleyecek şekilde düşünüldü:

- self-only
- branch-only
- department-only
- team-only
- region/location scopes
- custom tenant dimensions
- required relationships
- required owner relationships
- required session trust

Önemli hardening dersi: scoped permissions, required target-resource facts eksikse fail closed davranmalıdır.

Örneğin permission branch-scoped ise ve route target resource branch code'u trusted server-side data'dan sağlayamıyorsa güvenli karar deny'dır.

## Route-Level Pattern

Protected route kavramsal olarak şu sırayı izlemelidir:

```text
authenticate request
check tenant module / feature availability if relevant
reject machine principals when a human actor is required
resolve trusted resource facts from the server/database
evaluate centralized permission decision
execute controller/service logic
project response fields according to grants
write audit/security evidence when needed
```

Route permission logic'i service methods içine ikinci ve daha zayıf bir access sistemi olarak saklamamalıdır. Services tenant-owned existence ve consistency enforce edebilir; ama permission engine'in yerini almamalıdır.

## Service Account Boundaries

Service accounts machine principals olarak ele alındı, users olarak değil.

Design rules:

- service accounts explicit tenant-scoped permissions alır
- service accounts human user memberships inherit etmez
- human-only operations service-account principals'ı reddeder
- sensitive permissions explicit service-account-sensitive approval gerektirir
- audit/security events service account'ı user gibi göstermeden machine actor fields kullanır

Bu, API key'in sessizce human administrator'a dönüşmesini engeller.

## Field Projection Is Separate

Route authorization principal'ın resource'a erişip erişemeyeceğine karar verir.

Field projection response içinde hangi alanların dönebileceğine karar verir.

Bu ayrım önemlidir; çünkü bir user record listesi okuyabilir ama PII, confidential attributes, financial data, performance data veya security-sensitive metadata görme hakkına sahip olmayabilir.

## Lessons From Hardening

En önemli authorization dersi: missing server-derived facts zararsız kabul edilmemelidir.

Bir permission ownership, branch, department, team, relationship, classification veya başka resource fact'lere bağlıysa route bu fact'leri trusted server-side data'dan sağlamalıdır. Sağlayamıyorsa decision fail closed olmalıdır.

Bu kural, “permission check var” ile “permission check güvenilir” arasındaki farktır.
