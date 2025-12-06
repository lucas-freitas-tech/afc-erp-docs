# Fluxos do Usuário

## 1. Fluxo de Login (credenciais)

1. Usuário acessa uma página protegida ou a página de login.
2. Caso não esteja autenticado, é redirecionado para a página de login.
3. Usuário informa email e senha.
4. O sistema envia `POST /auth/signin`.
5. Se as credenciais forem válidas e o usuário estiver ativo:
   - Backend retorna access token e refresh token.
   - Frontend armazena os tokens (ex.: localStorage).
   - Usuário é redirecionado para o área autenticada
6. Se as credenciais forem inválidas ou o usuário estiver bloqueado:
   - É exibida uma mensagem de erro amigável.
   - Nenhum token é armazenado.

## 2. Fluxo de Acesso a Rota Protegida

1. Usuário tenta acessar uma rota protegida
2. O guard de rota verifica se existe um access token válido:
   - Se **não** houver token → redireciona para página de login.
   - Se houver token:
     - Frontend decodifica o token para obter usuário e suas permissões.
     - Verifica se o usuário possui a **permissão** necessária para aquela rota.
3. Se o usuário tiver permissão:
   - A rota é carregada normalmente.
4. Se o usuário **não** tiver permissão:
   - Acesso é negado (ex.: redireciona para o área autenticada ou para o login).

---

## 3. Fluxo de Renovação de Token (Refresh)

1. Usuário navega normalmente até o access token expirar.
2. Ao fazer uma requisição com token expirado:
   - Backend responde com erro indicando expiração do token (ex.: 401 com código específico).
3. Frontend:
   - Detecta o erro de expiração.
   - Envia `POST /auth/refresh` com o refresh token.
4. Backend:
   - Valida o refresh token.
   - Se válido, retorna um novo access token (e opcionalmente novo refresh token).
   - Se inválido/expirado, retorna erro e invalida sessão.
5. Frontend:
   - Em caso de sucesso, repete a requisição original com o novo token.
   - Em caso de erro, remove tokens do storage e redireciona usuário para página de login.

---

## 4. Fluxo de Logout

1. Usuário clica em "Sair" na interface.
2. Frontend:
   - Remove tokens do storage (access e refresh).
3. Usuário é redirecionado para a página de login.
4. Tentativas de acessar rotas protegidas após logout:
   - Devem redirecionar para página de login.

---

## 5. Fluxo de Gestão de Usuários (Admin)

1. Usuário com permission apropriada (ex.: `USER_MANAGE`) acessa o painel admin.
2. Frontend chama endpoint de listagem de usuários (ex.: `GET /admin/users`).
3. Admin pode:
   - Criar novo usuário:
     - Preenche dados + roles → `POST /admin/users`.
   - Editar usuário:
     - Altera dados/roles → `PUT /admin/users/{id}`.
   - Bloquear usuário:
     - Marca usuário como bloqueado → `PATCH /admin/users/{id}/block`.
   - Deletar usuário:
     - `DELETE /admin/users/{id}` (lógico ou físico, conforme modelagem).
4. páginas e ações só são exibidas se o usuário tiver as **permissions** necessárias.

---

## 6. Fluxo de Gestão de Roles e Permissions (Admin)

1. Usuário admin com permission apropriada (ex.: `ROLE_MANAGE`) acessa a área de roles.
2. Frontend consulta:
   - Lista de roles (`GET /admin/roles`).
   - Lista de permissions (`GET /admin/permissions`).
3. Admin pode:
   - Criar nova role, escolhendo um conjunto de permissions.
   - Atualizar uma role existente (adicionar/remover permissions).
   - Deletar role (respeitando regras, ex.: não deletar role em uso por usuários).
4. Alterações em roles **não exigem mudanças em código**:
   - A UI se baseia em permissions para mostrar/ocultar ações.
   - O backend protege endpoints por permissions, não por roles.

## 7. Fluxo de Acesso a Rotas Inexistentes (404)

1. Usuário acessa uma URL que não existe na aplicação.
2. Frontend detecta rota inexistente (erro 404 no client-side).
3. O sistema redireciona o usuário:
   - Para a **página de login** se o usuário não estiver autenticado.
   - Para o **área autenticada** se estiver autenticado.
4. Opcional:
   - Exibição de mensagem de informação (ex.: “Página não encontrada”).

## 8. Regras de Navegação por Tipo de Usuário

| Situação | Ação do Sistema | Destino |
|---------|-----------------|---------|
| Usuário **não autenticado** acessa rota pública | Carrega a rota | Rota solicitada |
| Usuário **não autenticado** acessa rota protegida | Redireciona | página de login |
| Usuário **autenticado** acessa Login ou Signup | Redireciona | Área autenticada |
| Usuário **autenticado** acessa rota protegida e **possui** a permission necessária | Carrega a rota | Rota solicitada |
| Usuário autenticado acessa rota protegida mas **não possui** permissions necessárias | Redireciona | Área autenticada |
| Usuário **não autenticado** acessa rota **inexistente** | Redireciona | página de login |
| Usuário **autenticado** acessa rota **inexistente** | Redireciona | Área autenticada |
