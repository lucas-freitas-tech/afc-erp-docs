# 🧩 Versionamento e Histórico de Releases — AFC ERP

Este documento registra o padrão de versionamento utilizado e todas as versões liberadas do sistema.

---

## 🧠 Padrão de Versionamento

O projeto utiliza **Versionamento Semântico (SemVer)**:

`MAJOR.MINOR.PATCH`

| Parte | Muda quando… | Exemplo |
|------|---------------|---------|
| **MAJOR** | Quebra de compatibilidade | `1.0.0 → 2.0.0` |
| **MINOR** | Nova funcionalidade sem quebrar o que existe | `1.1.0 → 1.2.0` |
| **PATCH** | Correção sem adicionar funcionalidade | `1.1.0 → 1.1.1` |

---

## 🚧 Fase Pré-Produto (`0.x.x`)

Até que o ERP esteja funcional para uso real, as versões ficarão na série:

`0.MINOR.PATCH`

- Pode haver quebras
- Não há necessidade de compatibilidade entre versões

---

## 🏁 Promoção para 1.0.0

Acontecerá quando existir:

- ✔ Módulo funcional completo (ex.: orçamento utilizável)
- ✔ Fluxo mínimo já disponível em produção

Após isso:

- Compatibilidade deve ser preservada
- Evoluções → MINOR
- Correções → PATCH

---

## 🔖 Tags no Git

Toda release deve possuir uma tag no formato:

- `v0.1.0`
- `v0.2.0`
- `v1.0.0`

---

📌 Este documento será atualizado conforme novas versões forem liberadas.
