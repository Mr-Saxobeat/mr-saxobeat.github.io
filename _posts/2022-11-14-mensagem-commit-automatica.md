---
layout: post
title:  "Automatizando o código da tarefa na mensagem de commit"
date:   2022-11-14 00:06:03 -0300
---
Padrões de commits variam de empresa para empresa, mas todos têm o mesmo objetivo: organizar o histórico de modificações do repositório para que numa auditoria futura seja possível entender de onde veio uma modificação e a qual tarefa ela estava relacionada.

Em um padrão de empresa, por exemplo, pode ser necessário inserir o código da tarefa executada tanto no nome da branch, quanto nas mensagens de commit. Pois bem, imagina você ter que digitar uma mensagem e o código da tarefa toda vez... Ok, é possível copiar e colar este código, mas ainda sim eu pensei se não seria possível melhorar ainda mais. Então conheci uma funcionalidade do git que era justamente o que eu precisava para resolver o problema.

## Git hooks

Git hooks são scripts executados quando certos eventos ocorrem. Existem vários:

- ``pre-commit``: é acionado antes mesmo de você digitar uma mensagem;
- ``prepare-commit-msg``: é acionado antes do editor de mensagem ser iniciado, mas depois da mensagem padrão ser criada;
- ``commit-msg``: recebe um parâmetro, que é o caminho para um arquivo temporário que contém a mensagem de commit já escrita.

Você pode ver mais sobre git hooks na [documentação oficial](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks).

### Para nosso problema nós precisamos:

1. Extrair o código da tarefa a partir do nome da branch, no modelo: ``feature/COD-123-Uma-tarefa-da-pesada``. Onde vamos extrair ``COD-123``;
2. Digitar a mensagem de commit: ``Adiciona botao furta cor``;
3. O git hook então fará o trabalho de juntar a mensagem com o código, tendo como resultado a seguinte mensagem:
``COD-123 - Adiciona botao furta cor``.

Para isso vamos usar o ``prepare-commit-msg`` hook.

Então vamos lá:

1. Na sua pasta ``~/.git/hooks`` crie um arquivo com nome ``prepare-commit-msg`` sem extensão
2. Configure o git para usar essa pasta para procurar os scripts a serem executados: ```
git config --global core.hooksPath ~/.git/hooks
```
3. Abra o arquivo ``~/.git/hooks/prepare-commit-msg`` e adicione o seguinte script bash:

```
#!/bin/bash
#
# Inspeciona o nome da branch e verifica se contém um código da tarefa, (i.e COD-123).
# Se sim, a mensagem de commit será concatenada com "COD-123 - " no início.
#
# Útil para organizar o histórico git em grupos de commits junto a uma história do usuário.
#

BRANCH_NAME=$(git rev-parse --abbrev-ref HEAD 2>/dev/null)

# Remove o prefixo da branch (featute/, hotfix/, release/, etc)
BASE_BRANCH_NAME=$(echo $BRANCH_NAME | sed 's/[^\/]*\///')

# Certifica que BASE_BRANCH_NAME começa com um código de tarefa.
# SKIP_PREPARE_COMMIT_MSG pode ser usado para pular este hook, enquanto permite que outros continuem sendo acionados.
if [[ "$BASE_BRANCH_NAME" =~ (^[[:upper:]]{2,}-[[:digit:]]{1,}) ]] && [ "$SKIP_PREPARE_COMMIT_MSG" != 1 ]; then

  # Divide o resto do nome da branch pelo hífen '-'
  NAME_PARTS=($(echo "$BASE_BRANCH_NAME" | tr '-' '\n'))

  # Pega apenas as duas primeiras partes, que são exatamente "COD" e "123" e enfim os concatenam como "COD-123"
  COMMIT_PREFIX=$(echo "${NAME_PARTS[0]}-${NAME_PARTS[1]}")

  IS_PREFIX_IN_COMMIT=$(grep -c "$COMMIT_PREFIX" $1)

  # Certifica que COMMIT_PREFIX existe em BRANCH_NAME e não está já inserido na mensagem de commit
  if [[ -n "$COMMIT_PREFIX" ]] && ! [[ $IS_PREFIX_IN_COMMIT -ge 1 ]]; then
    sed -i.bak -e "1s~^~$COMMIT_PREFIX - ~" $1
  fi
fi
```
Dessa forma, ao fazer commit temos:
```
feature/COD-123-Uma-tarefa-da-pesada                                             
~\git\meu-projeto
❯ git commit -m "Adiciona botao furta cor"
[feature/COD-123-Uma-tarefa-da-pesada ffc707da] COD-123 - Adiciona botao furta cor
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Este artigo foi adaptado do gist: https://gist.github.com/johncmunson/ca02a8027a923a7f4b2f662c67d6528c