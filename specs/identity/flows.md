# Fluxos do Usuário

## 1. Fluxo de Login (credenciais)

1. Usuário acessa uma página protegida ou a página de login.
2. Caso não esteja autenticado, é redirecionado para a página de login.
3. Usuário informa email e senha.
4. O sistema envia `POST /auth/signin`.
5. Se as credenciais forem válidas e o usuário estiver ativo:
   - O backend retorna o access token no corpo da resposta.
   - O backend envia o refresh token em cookie `HttpOnly`.
   - O frontend mantém o access token somente em memória e não acessa o refresh token.
   - O usuário é redirecionado para a área autenticada.
6. Se as credenciais forem inválidas ou o usuário estiver bloqueado:
   - É exibida uma mensagem de erro amigável.
   - Nenhuma sessão é iniciada.

## 2. Fluxo de Acesso a Rota Protegida

1. O bootstrap da aplicação materializa o cookie CSRF com `GET /auth/csrf` e tenta restaurar a
   sessão com `POST /auth/refresh`.
2. O guard aguarda a conclusão dessa restauração antes de decidir a navegação.
3. Se a sessão estiver anônima, o usuário é redirecionado para a página de login.
4. Se a sessão estiver autenticada, a rota protegida pode ser carregada.
5. Quando uma rota exigir uma permission específica:
   - guards e menus podem usar as permissions para orientar a experiência do usuário;
   - o backend sempre realiza a autorização efetiva e não confia na decisão do frontend.

---

## 3. Fluxo de Renovação de Token (Refresh)

1. Usuário navega normalmente até o access token expirar.
2. Ao fazer uma requisição com token expirado:
   - Backend responde com erro indicando expiração do token (ex.: 401 com código específico).
3. Frontend:
   - Detecta o erro de expiração.
   - Envia `POST /auth/refresh` com o cabeçalho CSRF.
   - O navegador envia automaticamente o refresh token armazenado no cookie `HttpOnly`.
4. Backend:
   - Valida e rotaciona o refresh token, preservando sua família.
   - Recalcula as permissions e o indicador Root do usuário.
   - Se válido, retorna um novo access token e envia o sucessor do refresh token no cookie.
   - Se o token for reutilizado, revoga toda a família.
   - Se for inválido, expirado ou pertencer a um usuário inativo, rejeita a renovação.
5. Frontend:
   - Em caso de sucesso, repete a requisição original com o novo token.
   - Em caso de erro, limpa a sessão em memória e redireciona o usuário para a página de login.

---

## 4. Fluxo de Logout

1. Usuário clica em "Sair" na interface.
2. O frontend garante o token CSRF e envia `POST /auth/logout`.
3. O backend revoga a família do refresh token e expira seu cookie.
4. O frontend remove o access token e os dados do usuário mantidos em memória.
5. O usuário é redirecionado para a página de login.
6. Tentativas de acessar rotas protegidas após logout:
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
4. Páginas e ações só são exibidas se o usuário tiver as **permissions** necessárias para a UX.
   O backend continua sendo a autoridade que autoriza cada operação.

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
   - O backend protege os endpoints por permissions, não por roles, e é a autoridade da
     autorização.

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

[◀ Voltar para o escopo](./scope.md) | [⯅ Ir para a especificação](./README.md) | [Ir para a arquitetura ▶](./architecture.md)
