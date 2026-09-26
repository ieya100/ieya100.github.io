---
layout: default
title: Hardware Investigation Archive
description: Личный архив технических исследований, аппаратных неисправностей и практических решений.
lang: ru
translation_key: home
alternate_url: /en/
---

# Hardware Investigation Archive

Добро пожаловать в личный архив технических исследований и реальных случаев неисправностей оборудования.

## Разделы

### 🔧 Исследования и кейсы

{% for item in site.data.cases %}
{% assign localized = item.ru %}
{% if localized.status == "published" %}
- [{{ localized.title }}]({{ localized.url | relative_url }})
{% elsif localized.status == "draft" %}
- {{ localized.title }} _(в разработке)_
{% endif %}
{% endfor %}

### ℹ О сайте

- [О проекте]({{ '/about.html' | relative_url }})

---

Материалы обновляются по мере появления новых кейсов.

