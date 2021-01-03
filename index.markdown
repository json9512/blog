---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home

# Testing timeago plugin
{% assign date = '2021-01-03T01:20:00z' %}

- Original date - {{ date }}
- With timeago filter - {{ date | timeago }}
---
