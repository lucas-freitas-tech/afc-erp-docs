# Fluxo Git — AFC ERP

Este documento define o fluxo Git adotado durante o desenvolvimento do AFC ERP.

## Branch principal

A branch `main` é a única branch permanente do projeto. Alterações que não pertencem ao fluxo de
tasks com subissues podem ser feitas diretamente nela quando forem pequenas e seguras.

Não é necessário manter uma branch `develop` neste momento.

## Branches temporárias

Branches temporárias são obrigatórias para implementações rastreadas por subissues e opcionais nos
demais casos. Elas também podem ser usadas quando uma alteração for grande, experimental ou
precisar ser desenvolvida sem afetar a `main`.

Os nomes devem indicar o objetivo da alteração, por exemplo:

- `feat/customer-registration`
- `fix/budget-calculation`
- `refactor/authentication`

Para implementações criadas pelo handoff de planos, o nome também identifica a task e a subissue:

```text
<tipo>/<task-id>-issue-<número>-<resultado>
```

Exemplo:

```text
feat/admin-03-issue-8-role-management
```

A skill somente prepara essa branch quando a `main` local estiver limpa e apontar para o mesmo
commit de `origin/main`. Ela não atualiza, descarta nem integra automaticamente uma base divergente.

Após a conclusão da alteração, a branch temporária pode ser integrada à `main` e removida.

## Padrão de commits

Os commits seguem um formato básico inspirado em Conventional Commits:

```text
tipo: descrição curta da alteração
```

O tipo, o scope opcional e a descrição devem ser escritos em inglês, conforme a convenção de
linguagem técnica do workspace.

Tipos mais comuns:

- `feat`: nova funcionalidade
- `fix`: correção de erro
- `docs`: alteração na documentação
- `refactor`: mudança interna sem alterar o comportamento esperado
- `test`: criação ou alteração de testes
- `chore`: manutenção e tarefas auxiliares

Exemplos:

```text
feat: add customer registration
fix: correct budget total calculation
docs: document authentication flow
```

## Pull Requests

Pull Requests são obrigatórios para implementações originadas dos planos que criam subissues no
backend ou frontend. Cada PR deve resolver exatamente uma subissue do mesmo repositório e direcionar
a branch padrão.

O body do PR deve manter exatamente uma linha no formato:

```text
Closes #<número-da-subissue>
```

Ao integrar o PR na branch padrão, o GitHub fecha essa subissue como concluída. A automação então
recalcula todas as subissues da task pai:

- todas concluídas: move a task de `🚧 Em Desenvolvimento` para `Aguardando validação`;
- alguma pendente ou reaberta: mantém ou devolve a task para `🚧 Em Desenvolvimento`.

Fechar um PR sem merge não conclui a subissue. Fechar uma subissue como `not planned` também não
libera a task pai. A task pai nunca é fechada automaticamente.

`Aguardando validação` significa que a implementação técnica foi integrada, mas ainda não recebeu o
aceite funcional. O cliente, ou o desenvolvedor responsável pela validação nesta fase do produto,
verifica a história e seus critérios de aceitação. Depois do aceite, o desenvolvedor move
manualmente a task para `Concluído` e fecha sua issue principal no `afc-erp-docs`.

Enquanto os repositórios privados estiverem no GitHub Free, a validação do vínculo do PR é
informativa: o desenvolvedor deve corrigir um check com falha antes do merge, mesmo que o GitHub não
permita configurá-lo como obrigatório.

Pull Requests permanecem opcionais para mudanças sem subissue, embora sejam recomendados quando uma
revisão isolada for útil.

Este fluxo deverá ser revisto caso outros desenvolvedores passem a colaborar no projeto.

## Configuração da automação

Backend e frontend devem possuir o repository secret `AFC_PROJECT_AUTOMATION_TOKEN`. Ele deve conter
um fine-grained PAT exclusivo para CI, restrito aos repositórios envolvidos, com leitura de PRs e
issues, leitura das relações de subissues e acesso de leitura e escrita ao GitHub Project 3.

O repositório privado `afc-erp-ai-tooling` deve permitir que outros repositórios privados da
organização utilizem seus reusable workflows em **Settings > Actions > General > Access**.

O workflow nativo `Code review approved` do Project deve permanecer desabilitado. A conclusão da
entrega é determinada pelas subissues, não pela aprovação do próprio PR.

## Releases e tags

As versões entregues são identificadas por tags Git seguindo o Versionamento Semântico. Os detalhes estão descritos em [Versionamento e Histórico de Releases](../product/VERSIONING.md).

[Voltar para o início](../README.md)
