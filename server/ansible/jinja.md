jinja
=====

### loop index and inventory hostname
```
| slave node | filename | line in file |
{% for slave in groups['slaves'] %}
| {{slave}} | test | slave index is {{loop.index}} |
{% endfor %}
```
