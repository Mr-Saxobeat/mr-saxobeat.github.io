---
layout: post
title: "Tabela não encontrada no Django"
date: 2025-03-24 23:30:00 -0300
---

### Problema

Hoje eu me deparei com um bug no trabalho que quando resolvi pareceu besta. Sempre parece besta depois de resolvido.

O erro era esse:
```bash
django.db.utils.ProgrammingError: relation "template" does not exist
```

Basicamente minha tabela `template` não estava sendo encontrada. O que era estranho pois a tabela com certeza existia no banco, eu conseguia consultá-la com SQL e também a outra tabela `plan` estava sendo recuperado corretamente e referenciava essa tabela `template`.

E mais, quando eu executava um script para recuperar algum objeto dessa tabela, dava certo.

Esse era o script:
```python
# test_template.py
from models import Template

template = Template.objects.get(pk=1)
print(template.name)
```

E o comando pra testar:
```bash
python manage.py tenant_command shell --schema=schema1 < test_template.py
```

### Solução

Depois de muito bater a cabeça consegui encontrar onde estava o erro.
Bom, eu já sabia que a tabela não estava sendo encontrada e isso já aconteceu comigo antes pois o object manager não estava configurando corretamente o tenant em que devia procurar a tabela.

Olhando melhor no código pude encontrar o momento em que o `plan` era recuperado:
```python
with schema_context(tenant_id):
    plan = Plan.objects.get(pk=plan_id)
```

Vê só, a consulta tá sendo executada dentro de um `schema_context`. Enquanto que no momento de consultar o template o schema_context não estava declarado:
```python
template = plan.template
```

Enfim, a solução foi simples, movi essa linha em que declaro a variável template para logo abaixo da linha em que o plan é recuperado. Daí a tabela `template` é encontrada no tenant correto.

Problema resolvido!