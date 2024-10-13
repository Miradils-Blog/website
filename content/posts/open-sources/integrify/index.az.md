---
title: Integrify
date: 2024-10-14T10:00:00+02:00
description: "İlk açıq-qaynaqlı proyektim: Integrify"
menu:
  sidebar:
    name: Integrify
    identifier: integrify
    parent: open-sources
    weight: 10

tags: ["Programming", "Open Source"]
categories: ["open-source", "story"]

---

## Giriş

Bu proyektdən öncə, başqa bir proyekt üçün freelance işləyirdim. Proyekt tam bitmək üzrə idi, ancaq ödəniş sistemi qoşmaq qalmışdı. İlk öncə qərar verdik ki, ABB üzərindən Azericard-a qoşulaq. İndiyə qədər çox developerdən eşitsəm də ki "Azericard-dan uzaq dur, dokumentasiyaları pis gündədir, supportu da yoxdur", sınamağa qərar verdim. Başqa bir səbəb də, birbaşa Azericard-la etdikdə komissiya faizlərin çox yaxşı olması idi. ABB tərəfdən baş verən texniki problemlərdən dolayı, Azericard-a qoşula bilmədik.

## Necə oldu ki, bu proyektə başladım?

Azericard alınmadıqdan sonra, başqa ödəniş sistemləri axtarmağa başladım. Tanış developerlər EPoint-i məsləhət gördü. Faizləri başqa ödəniş sistemləri ilə eyni olduğu üçün, EPoint-i seçdim. Qeydiyyatdan keçib, kontrakt imzaladıqdan sonra, dokumentasiyalarını əldə etdim. Dokumentasiya çox detallı və aydın yazılmışdı. Bu zaman ağlımda bir fikir yarandı: niyə də dokumentasiyanı kitabxanaya çevirməyim? Niyə bütün əlimə keçə biləcək dokumentasiyaları da əlavə etməyim? Komanda yoldaşım [Vahidlə](https://www.linkedin.com/in/vahidzhe/) planı müzakirə etdikdən sonra, proyekti elə EPoint-lə başlamaq qərarı verdik.

## Integrify

İlk problem, istənilən developerin qarşılaşdığı problem idi: adlandırma. Uzun müzakirələrdən (və ChatGPTnin köməyi) sonra, əsas məqsəd inteqrasiyaları asanlaşdırmaq olduğu üçün `integrify` adında dayandıq. Yeri gəlmişkən, logonu da ChatGPT ərsəyə gətirib. Bundan sonra ciddi development başladı.

## Development

### Kod strukturu

İlk öncə prioritetləri incələdik:

1. Hərtəfəli dokumentasiyanın mövcudluğu
2. Type-hintinglərdən davamlı istifadə. Həm istifadəçi həm də biz developerlər üçün işi asanlaşdırır.

Sonra isə struktur seçməli idik. Uzun araşdırmalardan sonra, [Adyen](https://github.com/Adyen/adyen-python-api-library/blob/main/Adyen/services/payments/payments_api.py)-ə bənzər etməyə qərarlaşdıq: hər sorğu üçün bir klass. İşin ortasında anladıq ki, nəticə çox da user-friendly çıxmır. Ona görə, strukturu dəyişib, hər klient üçün bir class və hər sorğu bir funksiya formatına keçdik. Növbəti mərhələ dokumentasiya idi.

### Dokumentasiya

Birinci iş dokumentasiyanı harada host edəcəyimizi seçmək idi. [SQLAdmin](https://aminalaee.dev/sqladmin/)-dən ilhamlanaraq öz şəxsi bloqumun subdomain-ində host etdik. Dokumentasiyanın dizaynı üçün iki ən məhşur seçim var idi: `readthedocs` və `mkdocs`. Birinci seçim klassik olsa da, ikinci seçim daha müasir və gözoxşayan olduğundan onunla davam etdik. Üstəlik `FastAPI`, `pydantic` və başqa kitabxanalar da bu statik sayt generatorlarından istifadə edir. Bununla da ilk versiyamızı PyPI-da paylaşdıq.

## İlk versiya və maraq

Proyektin ilk versiyasını çıxarmaq üzrə olarkən başqa developerlərin ödəniş sistemləri ilə problem çəkdiklərini gördüm. Onlarla bildiklərimi paylaşdım və belə bir kitabxana üzərində işlədiyimi bildirdim. Proyekt gözlədiyimdən daha yaxşı başladı: elə ilk həftədə 500-ə yaxın yüklənmə müşahidə olundu.

İlk versiyamızından sonra [Şəhriyar](https://www.linkedin.com/in/shahriyar-rzayev) proyektə yeni issue və PR-lər əlavə edərək dəstək olmağa başladı. Növbəti versiyalarda onun qeyd etdiyi (və özümüzün tapdığımız) problemləri düzəltdik. Amma kod strukturunda Şəhriyarla razılığa gələmmədik (bu postun yazılma tarixində, [müzakirə](https://github.com/mmzeynalli/integrify/pull/8) hələ də davam edir).

## Linklər

- [Dokumentasiya](https://integrify.mmzeynalli.dev)
- [Kod](https://github.com/mmzeynalli/integrify)
