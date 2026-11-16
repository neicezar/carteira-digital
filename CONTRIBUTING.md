# Como contribuir

Este documento descreve o fluxo de trabalho do carteira-digital: como nomear branches, como escrever commits, como abrir PRs e quando uma história pode ser considerada pronta.

Toda mudança nasce de uma história no Jira, e a chave dela (`CART-<n>`) aparece na branch, nos commits e no PR.

## Branches

| Branch | Origem | Destino | Para que serve |
|---|---|---|---|
| `main` | — | — | Código que já foi liberado. Cada versão publicada tem uma tag. |
| `develop` | — | — | Integração da sprint em andamento. |
| `feature/CART-<n>-descricao` | `develop` | `develop` | Uma história por branch. |
| `release/x.y.z` | `develop` | `main` (e volta para `develop`) | Fechamento da sprint. |

A descrição da branch fica em minúsculas, com palavras separadas por hífen:

```
feature/CART-12-debito-em-conta
feature/CART-6-docker-compose-postgres
```

Nada é commitado direto em `main` ou `develop`. Tudo entra por PR.

## Fluxo de uma história

1. Atualize a `develop` e crie a branch da história a partir dela:
   ```bash
   git checkout develop
   git pull
   git checkout -b feature/CART-12-debito-em-conta
   ```
2. Trabalhe em commits pequenos, seguindo o padrão da próxima seção.
3. Abra o PR para a `develop` e preencha o template (`.github/pull_request_template.md`).
4. Com a [Definition of Done](#definition-of-done) cumprida, faça o merge e apague a branch.

## Padrão de commit

A mensagem começa com a chave da história, dois-pontos e uma frase curta dizendo o que o commit faz. A frase fica em minúsculas, sem ponto final, e o verbo vai no presente.

```
CART-12: adiciona débito em conta
CART-12: valida saldo insuficiente no caso de uso
CART-6: configura conexão com o PostgreSQL
```

Quando o commit não pertencer a nenhuma história (ajuste de build, por exemplo), combine antes com o time e, se for o caso, abra uma história para ele.

## Release e versionamento

No fim da sprint:

1. Crie `release/x.y.z` a partir da `develop`. A versão segue o [versionamento semântico](https://semver.org/lang/pt-BR/).
2. Na release entram só o ajuste de versão e as correções do que será entregue, sem funcionalidade nova. O ajuste de versão troca o `0.0.1-SNAPSHOT` do `pom.xml` (raiz e módulos) pelo `x.y.z`:
   ```bash
   ./mvnw versions:set -DnewVersion=x.y.z -DgenerateBackupPoms=false
   ```
3. Abra o PR da release para a `main`.
4. Depois do merge, crie a tag da versão na `main`:
   ```bash
   git checkout main
   git pull
   git tag -a vx.y.z -m "Versão x.y.z"
   git push origin vx.y.z
   ```
5. Leve a `main` de volta para a `develop` (por PR), para que as duas branches não divirjam. Isso inclui as correções feitas na release e o commit de versão.

## Pull requests

- O título repete o padrão de commit: `CART-12: adiciona débito em conta`.
- O PR vai sempre para `develop`, com exceção do PR de `release/x.y.z`, que vai para `main`.
- O template cobre a história do Jira, o que mudou, como testar e o checklist da DoD. Preencha tudo, inclusive o que não se aplica (escreva "não se aplica").
- Mantenha o PR pequeno e focado em uma história.
- O merge é feito com **merge commit** (o padrão do GitHub), para preservar os commits da branch com a chave da história.

## Definition of Done

Uma história só é considerada pronta quando todos os itens abaixo estão cumpridos:

- [ ] Critérios de aceite atendidos.
- [ ] Testes unitários do domínio e do caso de uso escritos e passando.
- [ ] CI verde.
- [ ] Code review aprovado pelo agente Tech Lead.
- [ ] Validação do agente QA.
- [ ] Documentação da API atualizada, quando houver endpoint novo.

A aprovação do Tech Lead é registrada como review aprovado no PR. A validação do QA é registrada como comentário no PR, dizendo o que foi verificado.

O mesmo checklist aparece no template de PR, então confira lá antes de pedir a revisão.
