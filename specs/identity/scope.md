# Módulo Identity — Autenticação e Controle de Acessos (RBAC)

## Visão Geral

O Modulo Identity é responsável por garantir o acesso seguro a aplicação, controlando o que cada usuário pode visualizar e executar conforme seu nível de permissão.

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
📌 Cada role pode ter várias permissões. Um usuário pode possuir várias roles e recebe a união das
permissões atribuídas a elas.

Exemplo simples:

- Perfil **Administrador** → todas as permissões
- Perfil **Usuário** → apenas visualizar sua área autenticada

As permissões são definidas dentro do sistema e **não podem ser removidas**, apenas adicionadas em versões futuras.  
Isso garante **padronização e segurança** entre diferentes projetos.

---

## Funcionalidades da Versão Inicial

### Acesso ao Sistema

- Login com e-mail e senha
- Consulta e atualização do próprio perfil
- Troca da própria senha
- Troca obrigatória de senha temporária no primeiro acesso ou após redefinição administrativa

### Administração (apenas para usuários autorizados)

- Listagem e busca de usuários
- Criação, edição, ativação, desativação e exclusão de usuários
- Criação, edição e exclusão de perfis (roles)
- Visualização das permissões cadastradas no sistema
- Redefinição administrativa de senha por meio de senha temporária

---

## Regras de Negócio

- **RN001 — Email único**
  Cada usuário deve possuir um e-mail único para autenticação no sistema.

- **RN002 — Usuário inativo**
  Usuários inativos não podem acessar nenhuma área autenticada.

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

- **RN011 — Perfil do próprio usuário**
  O usuário pode consultar e alterar somente os próprios dados permitidos. Roles, permissions e
  estado da conta não podem ser modificados pelo fluxo de perfil.

- **RN012 — Troca da própria senha**
  A troca voluntária de senha deve exigir a senha atual e não pode expor ou registrar nenhuma das
  credenciais informadas.

- **RN013 — Redefinição administrativa de senha**
  Um administrador autorizado pode redefinir a senha de um usuário para uma senha temporária sem
  conhecer a senha anterior. A operação deve revogar as sessões existentes do usuário.

- **RN014 — Troca obrigatória de senha temporária**
  Usuários criados com senha temporária ou que tiveram a senha redefinida devem trocar a senha antes
  de acessar as demais funcionalidades. O backend deve aplicar essa restrição; o redirecionamento do
  frontend não é uma proteção suficiente.

- **RN015 — Proteção da conta Root**
  Administradores comuns não podem redefinir a senha, desativar ou alterar os acessos de uma conta
  Root.

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

[◀ Voltar para a especificação](./README.md) | [Ir para os fluxos ▶](./flows.md)
