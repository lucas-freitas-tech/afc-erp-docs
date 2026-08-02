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

## 🛠️ Épico 3 — Gestão de usuários e acessos

🎯 Objetivo: Permitir que perfis autorizados administrem usuários e acessos, e que cada usuário
gerencie com segurança os próprios dados e credenciais permitidos.

Abrange:

- CRUD de usuários com ativação e desativação
- CRUD de roles
- Visualização do catálogo de permissions
- Vínculos entre usuários e roles
- Vínculos entre roles e permissions
- Criação e redefinição administrativa com senha temporária
- Consulta e atualização do próprio perfil
- Troca voluntária de senha
- Troca obrigatória no primeiro acesso ou após redefinição administrativa

Critérios de sucesso:

- Administradores autorizados gerenciam usuários e acessos
- Usuário gerencia somente os próprios dados permitidos
- Permissions continuam sendo catálogo técnico imutável
- Usuário inativo não acessa o sistema
- Senha temporária não concede acesso normal antes da troca
- Credenciais e hashes nunca são expostos
- Contas Root permanecem isoladas das operações administrativas comuns

---

[◀ Voltar para a arquitetura](./architecture.md) | [⯅ Ir para a especificação](./README.md) | [Ir para as Histórias ▶](https://github.com/orgs/lucas-freitas-tech/projects/3/views/1)
