# Fluxos do Usuário

## 1. Fluxo de Login e Troca Obrigatória de Senha

1. Usuário acessa uma página protegida ou a página de login.
2. Caso não esteja autenticado, é redirecionado para a página de login.
3. Usuário informa e-mail e senha.
4. O sistema envia `POST /auth/signin`.
5. Se as credenciais forem inválidas ou o usuário estiver inativo:
   - É exibida uma mensagem de erro amigável.
   - Nenhuma sessão é iniciada.
6. Se as credenciais forem válidas e a troca de senha **não** for exigida:
   - O backend retorna o access token no corpo da resposta.
   - O backend envia o refresh token em cookie `HttpOnly`.
   - Permissions e o indicador Root são carregados na sessão.
   - O frontend mantém o access token somente em memória e não acessa o refresh token.
   - O usuário é redirecionado para a área autenticada (home).
7. Se as credenciais forem válidas e a troca de senha for **obrigatória**:
   - O backend inicia uma sessão restrita.
   - A sessão não carrega permissions e não concede bypass Root.
   - O usuário só pode trocar a senha e fazer logout.
   - O frontend redireciona para a tela de nova senha.
8. Depois que o usuário define a nova senha:
   - A obrigatoriedade de troca é removida.
   - A sessão restrita é encerrada.
   - O usuário precisa realizar um novo login para iniciar uma sessão normal.

---

## 2. Fluxo de Acesso a Rota Protegida

1. O bootstrap da aplicação materializa o cookie CSRF com `GET /auth/csrf` e tenta restaurar a
   sessão com `POST /auth/refresh`.
2. O guard aguarda a conclusão dessa restauração antes de decidir a navegação.
3. Se a sessão estiver anônima, o usuário é redirecionado para a página de login.
4. Se a sessão estiver restrita por troca obrigatória de senha, o usuário não acessa as
   funcionalidades normais e é direcionado para a tela de nova senha.
5. Se a sessão estiver autenticada normalmente, a rota protegida pode ser carregada.
6. Quando uma rota exigir uma permission específica:
   - guards e menus podem usar as permissions para orientar a experiência do usuário;
   - o backend sempre realiza a autorização efetiva e não confia na decisão do frontend.

---

## 3. Fluxo de Renovação de Sessão

1. Usuário navega normalmente até o access token expirar.
2. Ao fazer uma requisição com token expirado:
   - Backend responde `401 UNAUTHORIZED` sem revelar detalhes sobre o motivo da rejeição.
3. Frontend:
   - Detecta o `401` de uma requisição protegida elegível para renovação.
   - Envia `POST /auth/refresh` com o cabeçalho CSRF.
   - O navegador envia automaticamente o refresh token armazenado no cookie `HttpOnly`.
4. Backend:
   - Valida e rotaciona o refresh token, preservando sua família.
   - Recalcula as permissions e o indicador Root do usuário.
   - Preserva ou recalcula o estado de troca obrigatória de senha.
   - Uma sessão restrita não pode ganhar acesso normal por meio do refresh.
   - Se válido, retorna um novo access token e envia o sucessor do refresh token no cookie.
   - Se o token for reutilizado, revoga toda a família.
   - Se for inválido, expirado ou pertencer a um usuário inativo, rejeita a renovação.
5. Frontend:
   - Em caso de sucesso, repete a requisição original com o novo token uma única vez.
   - Em caso de erro, limpa a sessão em memória e redireciona o usuário para a página de login.

---

## 4. Fluxo de Logout

1. Usuário clica em "Sair" na interface.
2. O frontend garante o token CSRF e envia `POST /auth/logout`.
3. O backend revoga a família do refresh token e expira seu cookie.
4. O frontend remove o access token e os dados do usuário mantidos em memória.
5. O usuário é redirecionado para a página de login.
6. Tentativas de acessar rotas protegidas após logout devem redirecionar para a página de login.

O logout aplica-se tanto à sessão normal quanto à sessão restrita por troca obrigatória.

---

## 5. Fluxo de Gestão de Usuários

1. Usuário com permission apropriada (ex.: `USER_MANAGE`) acessa a área administrativa de
   usuários.
2. Frontend consulta a listagem e os detalhes dos usuários.
3. Administrador pode:
   - criar usuário com dados, roles e senha temporária;
   - editar dados e roles;
   - ativar ou desativar usuário;
   - redefinir a senha esquecida por meio de senha temporária;
   - excluir usuário conforme regras futuras.
4. Na criação ou redefinição administrativa de senha, o backend:
   - armazena somente o hash da senha temporária;
   - marca a troca de senha como obrigatória;
   - revoga todas as famílias de refresh token do usuário na redefinição;
   - não permite que um administrador comum redefina a senha de uma conta Root.
5. A senha temporária é entregue por canal interno controlado e não é enviada por e-mail nesta
   versão. Ela nunca é registrada em logs.
6. Páginas e ações só são exibidas se o usuário tiver as permissions necessárias para a UX. O
   backend continua sendo a autoridade que autoriza cada operação.

---

## 6. Fluxo de Gestão de Roles e Permissions

1. Usuário com permission apropriada (ex.: `ROLE_MANAGE`) acessa a área de roles.
2. Frontend consulta:
   - a lista de roles administrativas;
   - o catálogo de permissions, somente para leitura.
3. Administrador pode:
   - criar, atualizar e excluir roles;
   - vincular e desvincular permissions às roles.
4. O catálogo de permissions não é criado, editado ou excluído pelo CRUD administrativo; a
   interface apenas o consulta.
5. A autorização efetiva continua sendo por permission, não por role.
6. A Role Root não é exibida nem manipulada por administradores comuns.
7. Alterações em roles não exigem mudanças em código:
   - a UI se baseia em permissions para mostrar ou ocultar ações;
   - o backend protege os endpoints por permissions e é a autoridade da autorização.

---

## 7. Fluxo do Perfil do Usuário

1. O usuário autenticado acessa o próprio perfil.
2. O backend identifica a conta pelo access token; o cliente não escolhe o identificador do
   usuário consultado.
3. O usuário pode:
   - consultar os próprios dados;
   - atualizar os campos pessoais permitidos;
   - trocar a própria senha informando a senha atual.
4. O fluxo de perfil não permite alterar roles, permissions, estado da conta ou dados de outro
   usuário.
5. Redefinição administrativa de senha não faz parte deste fluxo.
6. Dados sensíveis, password hashes e informações internas de segurança nunca são retornados.

---

## 8. Fluxo de Acesso a Rota Inexistente

1. Usuário acessa uma URL que não existe na aplicação.
2. Frontend detecta rota inexistente.
3. O sistema redireciona o usuário:
   - para a página de login, se não estiver autenticado;
   - para a home (área autenticada), se estiver autenticado.
4. Opcionalmente, pode ser apresentada uma mensagem de página não encontrada.

---

## 9. Regras de Navegação

| Situação | Ação do Sistema | Destino |
|---------|-----------------|---------|
| Visitante acessa rota pública | Carrega a rota | Rota solicitada |
| Visitante acessa rota protegida | Redireciona | Página de login |
| Usuário autenticado acessa login | Redireciona | Home |
| Usuário autenticado possui a permission necessária | Carrega a rota | Rota solicitada |
| Usuário autenticado não possui a permission necessária | Nega o acesso | Home ou área autenticada |
| Root acessa funcionalidade protegida | Permite pelo bypass | Rota solicitada |
| Usuário com troca obrigatória de senha | Restringe o acesso | Tela de nova senha |
| Sessão sem refresh válido | Encerra a sessão em memória | Página de login |
| Usuário inativo | Não inicia nem renova sessão | Página de login / erro de autenticação |
| Visitante acessa rota inexistente | Redireciona | Página de login |
| Usuário autenticado acessa rota inexistente | Redireciona | Home |

[◀ Voltar para o escopo](./scope.md) | [⯅ Ir para a especificação](./README.md) | [Ir para a arquitetura ▶](./architecture.md)
