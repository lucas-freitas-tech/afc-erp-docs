# Fluxo Git — AFC ERP

Este documento define o fluxo Git adotado durante o desenvolvimento do AFC ERP.

## Branch principal

A branch `main` é a única branch permanente do projeto. Como o projeto possui apenas um desenvolvedor, alterações pequenas e seguras podem ser feitas diretamente nela.

Não é necessário manter uma branch `develop` neste momento.

## Branches temporárias

Branches temporárias são opcionais e podem ser usadas quando uma alteração for grande, experimental ou precisar ser desenvolvida sem afetar a `main`.

Os nomes devem indicar o objetivo da alteração, por exemplo:

- `feat/cadastro-clientes`
- `fix/calculo-orcamento`
- `refactor/autenticacao`

Após a conclusão da alteração, a branch temporária pode ser integrada à `main` e removida.

## Padrão de commits

Os commits seguem um formato básico inspirado em Conventional Commits:

```text
tipo: descrição curta da alteração
```

Tipos mais comuns:

- `feat`: nova funcionalidade
- `fix`: correção de erro
- `docs`: alteração na documentação
- `refactor`: mudança interna sem alterar o comportamento esperado
- `test`: criação ou alteração de testes
- `chore`: manutenção e tarefas auxiliares

Exemplos:

```text
feat: adiciona cadastro de clientes
fix: corrige cálculo do total do orçamento
docs: documenta fluxo de autenticação
```

## Pull Requests

Pull Requests não são obrigatórios enquanto o projeto possuir apenas um desenvolvedor. Eles podem ser utilizados em alterações grandes quando uma revisão isolada for útil.

Este fluxo deverá ser revisto caso outros desenvolvedores passem a colaborar no projeto.

## Releases e tags

As versões entregues são identificadas por tags Git seguindo o Versionamento Semântico. Os detalhes estão descritos em [Versionamento e Histórico de Releases](../product/VERSIONING.md).

[Voltar para o início](../README.md)
