---
layout: default
title: Hardware Investigation Archive
description: A personal archive of real-world hardware investigations, failures and practical fixes.
lang: en
translation_key: home
alternate_url: /
---

# Hardware Investigation Archive

Welcome to a personal archive of hardware research and real-world failure cases: SSD issues, NVMe controllers, laptop behavior after sleep, thermal problems, electrical faults and other rare incidents.

This site preserves technical material that may be useful to engineers and enthusiasts.

## Sections

### 🔧 Investigations & case studies

{% for item in site.data.cases %}
{% assign localized = item.en %}
{% if localized.status == "published" %}
- [{{ localized.title }}]({{ localized.url | relative_url }})
{% elsif localized.status == "draft" %}
- {{ localized.title }} _(in progress)_
{% endif %}
{% endfor %}

### ℹ About

- [About the project]({{ '/en/about.html' | relative_url }})

---

Materials are updated as new cases appear.

