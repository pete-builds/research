---
layout: default
title: Research
---

Long-form research reports. Every factual claim links to the source it came from, and each report ends with its confidence assessment, open questions and full source list.

{% assign reports = site.pages | where: "report", true | sort: "date" | reverse %}
{% for r in reports %}
### [{{ r.title }}]({{ r.url | relative_url }})
*{{ r.date }}.* {{ r.summary }}
{% endfor %}
