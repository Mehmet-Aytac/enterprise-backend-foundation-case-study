# Görüşme Anlatım Rehberi

Bu doküman, özel backend altyapı temeli çalışmasını teknik görüşmelerde kod açmadan anlatmak için kısa bir rehberdir.

## 1. Dürüst Kapsam

Önce kapsamı dürüstçe söyle:

```text
Bu proje bitmiş bir ürün değil; aktif geliştirme aşamasındaki özel kaynak kodlu backend altyapı temeli çalışmasının public case study dokümantasyonudur.
```

## 2. Problem

Projenin cevap aradığı soru:

```text
Gelecekteki modüller aynı kiracı yalıtımı, kimlik doğrulama, yetkilendirme, yanıt sadeleştirme, denetlenebilirlik, doğrulama ve dağıtım hazırlığı davranışlarını nasıl güvenli şekilde paylaşabilir?
```

## 3. Mimari

Kısa anlatım:

```text
İstek API'ye gelir, kimlik çözülür, kiracı bağlamı ve erişim kapsamı oluşturulur, yetki karar motoru rotayı değerlendirir, modül servisi kiracı kapsamlı iş yapar ve denetim/güvenlik kaydı iş kuyruğuna yazılır.
```

## 4. Yetki Karar Motoru

Vurgulanacak ana fikir:

```text
Yetki karar motoru sadece rol kontrolü değildir. Aktör tipi, kiracı sınırı, rota izni, kapsam kısıtları, ilişki kontrolleri, politika kuralları, oturum güven düzeyi ve sunucu tarafından doğrulanan kaynak bilgileri birlikte değerlendirilir.
```

## 5. Kiracı Yalıtımı

Kısa anlatım:

```text
Kiracı sınırı normal bir filtre değil, güvenlik sınırı olarak ele alınır. Kiracı bağlamı istek gövdesinden değil kimliği doğrulanmış istek durumundan gelir.
```

## 6. Yanıt Sadeleştirme

Kısa anlatım:

```text
Rotaya erişebilmek, yanıt içindeki tüm alanları görebilmek anlamına gelmez. Alan filtreleme, kısıtlı alanları ayrıca kontrol eder.
```

## 7. Denetlenebilirlik

Kısa anlatım:

```text
Denetim kayıtları ve güvenlik olayları ayrılır. Önemli kayıtlar iş kuyruğu üzerinden kalıcı hale getirilir. Kayıt zinciri tasarımı uygulama seviyesinde kurcalamayı belli eder.
```

## 8. AI Desteği

Dürüst açıklama:

```text
AI araçları üretim, inceleme, sertleştirme ve dokümantasyon aşamalarında kullanıldı. Benim rolüm gereksinimler, mimari değerlendirme, doğrulama incelemesi, uç durum düşüncesi, dokümantasyon ve sertleştirme yönü üzerindeydi.
```

## 9. Kapanış Cümlesi

```text
Bu projenin güçlü yanı bitmiş ürün olması değil; kiracı sınırları, yetki kararları, yanıt sadeleştirme, denetlenebilirlik, doğrulama ve üretim hazırlığı sınırları gibi gerçek backend konuları üzerinde düşünülmüş olmasıdır.
```
