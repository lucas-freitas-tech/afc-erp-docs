# 🧩 Lista de épicos do modulo Identity

Este documento detalha os épicos, seus objetivos e o valor que entregam ao produto.

## 📌 Épico 1 — Acesso seguro ao sistema (Autenticação)

🎯 Objetivo: Garantir acesso seguro a usuários autorizados, mantendo a sessão durante a navegação.

Abrange:

- Login com e-mail e senha
- Logout
- Renovação de sessão (refresh token)
- Redirecionamentos e erros de autenticação

Critérios de sucesso:

- Usuários apenas autenticados acessam áreas protegidas
- Sessão expira automaticamente
- Feedback claro em erros de login

---

## 🔐 Épico 2 — Controle de acesso baseado em permissões (RBAC)

🎯 Objetivo: Garantir que cada usuário acesse somente o que tem permissão.

Abrange:

- Proteção de rotas e funcionalidades
- Verificação de permissões (não de roles diretamente)
- Root com acesso total
- Regras de isolamento da Role Root

Critérios de sucesso:

- Permissões controlam tudo
- Roles apenas agrupam permissions
- Usuários fora do escopo seguro não acessam recursos

---

## 🛠️ Épico 3 — Gestão administrativa de acessos

🎯 Objetivo: Permitir que perfis autorizados administrem todo o sistema de identidade.

Abrange:

- CRUD de usuários com bloqueio
- CRUD de roles e permissions
- Visualização do catálogo de permissões
- Edição de vínculo entre usuários, perfis e permissões

Critérios de sucesso:

- Admin pode gerenciar todo o fluxo de acesso
- Permissões nunca são removidas, apenas adicionadas
- Usuário bloqueado não acessa mais o sistema

---

[◀ Voltar para a arquitetura](./architecture.md) | [⯅ Ir para a especificação](./README.md) | [Ir para as Histórias ▶](https://github.com/orgs/lucas-freitas-tech/projects/3/views/1)
