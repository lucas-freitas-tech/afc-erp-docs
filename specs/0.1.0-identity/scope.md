# Resumo Executivo — Sistema de Autenticação e Controle de Acessos (RBAC)

## Visão Geral

O Sistema de Autenticação RBAC é um módulo genérico e reutilizável, responsável por garantir o acesso seguro a aplicações, controlando o que cada usuário pode visualizar e executar conforme seu nível de permissão.  
Pode ser integrado a diferentes projetos da organização, padronizando segurança e facilitando a administração de acessos.

---

## Objetivos Principais

- Garantir acesso seguro ao sistema
- Controlar permissões conforme o perfil do usuário
- Facilitar o gerenciamento administrativo de contas e acessos

---

## Funcionamento das Permissões (RBAC)

O controle de acessos é feito em **3 níveis relacionados**:

| Elemento      | O que é | Exemplo |
|---------------|--------|---------|
| **Usuário**   | Pessoa que acessa o sistema | João, Maria |
| **Role (Perfil)** | Um conjunto de permissões atribuídas ao usuário | Administrador, Usuário |
| **Permission (Permissão)** | Autorizações individuais para executar ações específicas | Gerenciar usuários, Visualizar lista, Excluir registros |

📌 O sistema **não** verifica acessos diretamente por perfis, mas sim por permissões.  
📌 Cada perfil pode ter várias permissões, e um usuário recebe as permissões do seu perfil.

Exemplo simples:

- Perfil **Administrador** → todas as permissões
- Perfil **Usuário** → apenas visualizar sua área autenticada

As permissões são definidas dentro do sistema e **não podem ser removidas**, apenas adicionadas em versões futuras.  
Isso garante **padronização e segurança** entre diferentes projetos.

---

## Funcionalidades da Versão Inicial

### Acesso ao Sistema

- Login com e-mail e senha

### Administração (apenas para usuários autorizados)

- Listagem e busca de usuários
- Criação, edição, bloqueio e exclusão de usuários
- Criação, edição, bloqueio e exclusão de perfils (roles)
- Visualização das permissões cadastradas no sistema

---

## Regras de Negócio

- **RN001 — Email único**
  Cada usuário deve possuir um e-mail único para autenticação no sistema.

- **RN002 — Acesso bloqueado**
  Usuários bloqueados não podem acessar nenhuma área autenticada.

- **RN003 — RBAC baseado em permissões**
  O acesso às funcionalidades do sistema deve ser autorizado exclusivamente por permissões registradas no sistema, não por roles diretamente.

- **RN004 — Permissões independentes**
  A exclusão de uma role não deve remover as permissões vinculadas a ela.
  As permissões permanecem cadastradas no sistema para uso futuro.

- **RN005 — Sem permissões diretas**
  Usuários não recebem permissões individuais. Toda permissão deve ser concedida exclusivamente através de roles.

- **RN006 — Sessão expirada**
  Usuários com token expirado devem ser automaticamente redirecionados para autenticação.

- **RN007 — Catálogo de permissões imutável**
  Permissões cadastradas no sistema não podem ser removidas, apenas adicionadas em novas versões, garantindo padronização e segurança.

- **RN008 — Conta Root**
  O sistema deve nascer com uma conta root capaz de acessar e modificar qualquer recurso, independente das permissões atribuídas.

- **RN009 — Role Root**
  A role root deve conceder acesso total ao sistema, sem necessidade de vincular permissões. Ela é exclusiva para a conta root.

- **RN010 — Isolamento da Role Root**
  A role root não deve ser exibida ou manipulada por nenhum usuário que não seja root. Apenas o root pode criar ou modificar outro root.

---

## Segurança Aplicada

- Senhas armazenadas com criptografia forte
- Tokens de acesso com tempo de expiração
- Controle de acesso baseado em permissões registradas no sistema

---

## Fora do Escopo Inicial

(Recursos previstos apenas para versões futuras)

- Login Social ex. (Google, Facebook, etc)
- Recuperação de senha por e-mail
- Confirmação de e-mail no cadastro
- Autenticação em duas etapas (2FA)
- Auditoria detalhada de ações (logs avançados)
- Bloqueio imediato de tokens após logout
