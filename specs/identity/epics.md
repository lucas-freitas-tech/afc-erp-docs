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

- CRUD de usuários com ativação e desativação
- CRUD de roles
- Visualização do catálogo de permissões
- Edição de vínculos entre usuários e roles e entre roles e permissões
- Redefinição administrativa de senha temporária

Critérios de sucesso:

- Admin pode gerenciar todo o fluxo de acesso
- Permissões nunca são removidas, apenas adicionadas
- Usuário inativo não acessa mais o sistema

---

## 👤 Épico 4 — Perfil e credenciais do usuário

🎯 Objetivo: Permitir que o usuário consulte sua conta e mantenha sua própria senha com segurança.

Abrange:

- Consulta do próprio perfil
- Atualização dos dados pessoais permitidos
- Troca voluntária de senha mediante confirmação da senha atual
- Troca obrigatória no primeiro acesso ou após redefinição administrativa
- Restrição de acesso enquanto a troca obrigatória estiver pendente

Critérios de sucesso:

- Usuário não acessa nem modifica o perfil de outra pessoa
- Perfil não permite alterar roles, permissions ou estado da conta
- Senha temporária não concede acesso normal antes de ser substituída
- Credenciais e hashes nunca são expostos

---

[◀ Voltar para a arquitetura](./architecture.md) | [⯅ Ir para a especificação](./README.md) | [Ir para as Histórias ▶](https://github.com/orgs/lucas-freitas-tech/projects/3/views/1)
