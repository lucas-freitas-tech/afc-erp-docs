# Fluxos do módulo Identity

## FL001 — Autenticar usuário

- **Requisitos relacionados:** RF001, RN010, RN012, RNF001, RNF005
- **Ator:** visitante
- **Gatilho:** envio de e-mail e senha na página de login
- **Pré-condições:** nenhuma sessão normal ativa

**Fluxo principal:**

1. O frontend envia as credenciais ao contrato de signin.
2. O backend valida as credenciais e o estado da conta.
3. Para usuário ativo sem troca obrigatória, o backend inicia sessão normal com permissions
   efetivas e indicador Root.
4. O frontend mantém o access token somente em memória e direciona o usuário para a home.

**Alternativas e bloqueios:**

- credenciais inválidas ou usuário bloqueado produzem erro seguro e não iniciam sessão;
- senha temporária inicia somente a sessão restrita descrita em FL002.

**Resultado:** sessão normal ou restrita iniciada sem expor credenciais.

## FL002 — Trocar senha temporária

- **Requisitos relacionados:** RN012, RF005, RNF001, RNF003
- **Ator:** usuário em sessão restrita
- **Gatilho:** autenticação com senha temporária
- **Pré-condições:** indicador de troca obrigatória ativo

**Fluxo principal:**

1. O backend cria uma sessão sem permissions nem bypass Root.
2. O frontend direciona o usuário para a troca obrigatória.
3. O usuário informa e confirma uma senha definitiva válida e diferente da temporária.
4. O backend substitui o hash, remove a obrigatoriedade e encerra a sessão restrita.
5. O frontend retorna ao login para uma nova autenticação normal.

**Alternativas e bloqueios:** somente troca de senha e logout são permitidos durante a sessão
restrita; frontend e refresh não podem elevar essa sessão para acesso normal.

**Resultado:** senha definitiva cadastrada e nova autenticação exigida.

## FL003 — Acessar recurso protegido

- **Requisitos relacionados:** RN002, RN004, RN006, RF004, RF025, RNF003
- **Ator:** visitante ou usuário autenticado
- **Gatilho:** navegação ou requisição para recurso protegido
- **Pré-condições:** recurso exige autenticação e, quando aplicável, uma permission

**Fluxo principal:**

1. O frontend restaura a sessão antes de decidir a navegação inicial.
2. Guards e menus orientam a experiência conforme estado e permissions conhecidos.
3. O backend valida autenticação e autoriza a operação pela permission exigida ou bypass Root.

**Alternativas e bloqueios:**

- visitante é direcionado ao login;
- sessão restrita é direcionada à troca obrigatória;
- usuário sem permission recebe negação e retorna a uma área autenticada segura;
- decisões do frontend nunca substituem a autorização do backend.

**Resultado:** recurso carregado somente para sessão autorizada.

## FL004 — Renovar sessão

- **Requisitos relacionados:** RN010, RF002, RNF004, RNF005, RNF006
- **Ator:** usuário com sessão renovável
- **Gatilho:** restauração da aplicação ou resposta `401` elegível para renovação
- **Pré-condições:** navegador possui refresh token em cookie

**Fluxo principal:**

1. O frontend garante a proteção CSRF e solicita refresh.
2. O navegador envia automaticamente o cookie `HttpOnly`.
3. O backend valida o token, o usuário e a família; recalcula permissions, Root e troca obrigatória.
4. O backend rotaciona o refresh token e retorna novo access token.
5. Quando aplicável, o frontend repete a requisição original uma única vez.

**Alternativas e bloqueios:** token inválido, expirado, reutilizado, revogado ou de usuário bloqueado
é rejeitado; reutilização revoga a família; falha limpa a sessão local e direciona ao login.

**Resultado:** sessão renovada com autorizações atuais ou encerrada com segurança.

## FL005 — Encerrar sessão

- **Requisitos relacionados:** RF003, RNF001
- **Ator:** usuário em sessão normal ou restrita
- **Gatilho:** ação de logout

**Fluxo principal:**

1. O frontend garante a proteção CSRF e solicita logout.
2. O backend revoga a família do refresh token e expira o cookie.
3. O frontend remove access token e dados de sessão mantidos em memória.
4. O usuário retorna ao login.

**Resultado:** sessão local encerrada e refresh revogado.

## FL006 — Consultar e atualizar o próprio perfil

- **Requisitos relacionados:** RN013, RF006, RF007, RNF001
- **Ator:** usuário autenticado
- **Gatilho:** acesso ao próprio perfil
- **Pré-condições:** sessão normal válida

**Fluxo principal:**

1. O backend identifica o usuário pelo principal autenticado.
2. O sistema apresenta somente os dados pessoais permitidos.
3. O usuário altera campos permitidos e confirma a atualização.
4. O backend valida e persiste os novos dados, incluindo a unicidade de e-mail.

**Alternativas e bloqueios:** o cliente não escolhe outro usuário; roles, permissions, estado da
conta e credenciais não fazem parte da edição comum.

**Resultado:** perfil próprio consultado ou atualizado sem ampliar privilégios.

## FL007 — Trocar a própria senha

- **Requisitos relacionados:** RF008, RNF001, RNF002
- **Ator:** usuário autenticado
- **Gatilho:** ação separada de troca de senha
- **Pré-condições:** sessão normal válida

**Fluxo principal:**

1. O usuário informa a senha atual, a nova senha e sua confirmação.
2. O backend valida a senha atual, a política aplicável e a diferença entre as senhas.
3. O backend persiste apenas o novo hash e revoga todas as famílias de refresh token do usuário.
4. O frontend encerra a sessão local e exige novo login.

**Alternativas e bloqueios:** senha atual inválida, confirmação divergente ou nova senha inválida
impedem a alteração sem revelar credenciais.

**Resultado:** credencial própria alterada e sessões anteriores encerradas.

## FL008 — Consultar catálogo de permissions

- **Requisitos relacionados:** RN005, RF009, RF010, RNF003
- **Ator:** administrador autorizado
- **Autorização da rota:** `PERMISSION_READ` OR `ROLE_READ` OR Root
- **Gatilho:** acesso ao catálogo ou consulta auxiliar da gestão de roles

**Fluxo principal:**

1. A página administrativa valida `PERMISSION_READ` ou Root.
2. O frontend consulta o catálogo com paginação e pesquisa por código, nome ou descrição.
3. Cada item apresenta código, nome, descrição e `module_code`.

**Alternativas e bloqueios:** a página exige `PERMISSION_READ` ou Root; `ROLE_READ` permite usar a
mesma rota somente como apoio à gestão de roles. Nenhum fluxo oferece escrita de permissions.

**Resultado:** catálogo consultado em modo somente leitura.

## FL009 — Listar roles

- **Requisitos relacionados:** RN006, RN007, RF011, RF010
- **Ator:** administrador autorizado
- **Autorização:** `ROLE_READ` ou Root
- **Gatilho:** acesso à gestão de roles

**Fluxo principal:**

1. O frontend consulta roles com paginação.
2. O usuário pode pesquisar por código, nome ou descrição.
3. Pode filtrar por uma ou mais permissions, combinadas com AND, e combinar o filtro com a pesquisa.
4. O autocomplete de permissions usa a mesma rota do catálogo, autorizada conforme RF010.

**Alternativas e bloqueios:** a Role Root não aparece; ausência de permission impede a página.

**Resultado:** roles não reservadas listadas conforme os filtros.

## FL010 — Consultar role

- **Requisitos relacionados:** RN007, RF012
- **Ator:** administrador autorizado
- **Autorização:** `ROLE_READ` ou Root
- **Gatilho:** seleção de uma role não reservada

**Fluxo principal:** o sistema apresenta código, nome, descrição e somente as permissions
atribuídas, agrupadas por módulo e sem controles editáveis.

**Resultado:** role consultada em modo somente leitura.

## FL011 — Criar ou atualizar role

- **Requisitos relacionados:** RN005, RN009, RN014, RF010, RF013, RF014
- **Ator:** administrador autorizado
- **Autorização:** `ROLE_CREATE` para criação; `ROLE_UPDATE` para edição; Root em ambos
- **Gatilho:** abertura do formulário de role

**Fluxo principal:**

1. O sistema apresenta código, nome, descrição e catálogo completo agrupado por módulos.
2. O usuário pesquisa permissions por módulo, código, nome ou descrição.
3. Pode selecionar permissions individualmente e preservar seleções ao alterar o filtro.
4. O sistema apresenta contadores e ordena permissions pelo código dentro do módulo.
5. O backend valida se toda permission de criação, atualização ou exclusão possui a permission de
   leitura correspondente no mesmo conjunto.
6. Na edição, o conjunto confirmado substitui os vínculos anteriores somente após a validação.

**Alternativas e bloqueios:** código duplicado, role reservada, falta da permission exigida para a
operação ou conjunto que viole a RN014 impedem o cadastro ou a alteração sem modificar a role;
seleção em massa não faz parte desta entrega.

**Resultado:** role criada ou atualizada com zero ou mais permissions.

## FL012 — Excluir role

- **Requisitos relacionados:** RN005, RN007, RN008, RF015
- **Ator:** administrador autorizado
- **Autorização:** `ROLE_DELETE` ou Root
- **Gatilho:** solicitação de exclusão de role não reservada

**Fluxo principal:** o backend verifica vínculos, remove vínculos com usuários bloqueados quando
existirem e exclui a role sem alterar o catálogo de permissions.

**Alternativas e bloqueios:** Role Root ou role vinculada a usuário ativo não pode ser excluída.

**Resultado:** role elegível excluída ou operação recusada com motivo seguro.

## FL013 — Listar usuários

- **Requisitos relacionados:** RN007, RF016, RF017
- **Ator:** administrador autorizado
- **Autorização:** `USER_READ` ou Root
- **Gatilho:** acesso à gestão de usuários

**Fluxo principal:**

1. O frontend consulta usuários com paginação.
2. O usuário pode pesquisar por nome ou e-mail.
3. Pode filtrar por ativos, bloqueados ou todos.
4. A gestão usa a mesma rota de listagem de roles, autorizada conforme RF016.

**Alternativas e bloqueios:** a conta Root não aparece; filtro por role não faz parte da entrega.

**Resultado:** usuários não Root listados conforme pesquisa e estado.

## FL014 — Consultar usuário

- **Requisitos relacionados:** RN007, RF018, RF016, RNF001
- **Ator:** administrador autorizado
- **Autorização:** `USER_READ` ou Root
- **Gatilho:** seleção de um usuário não Root

**Fluxo principal:** o sistema apresenta dados administrativos, estado e roles associadas sem
expor senha, hash, tokens ou outros dados internos de segurança.

**Resultado:** usuário consultado em modo seguro.

## FL015 — Criar usuário

- **Requisitos relacionados:** RN001, RN003, RN004, RN012, RF019, RNF001, RNF002
- **Ator:** administrador autorizado
- **Autorização:** `USER_CREATE` ou Root
- **Gatilho:** envio do formulário de criação

**Fluxo principal:**

1. O administrador informa nome, e-mail, roles opcionais, senha temporária e confirmação.
2. O backend valida unicidade, roles e credencial.
3. O backend persiste somente o hash e marca a troca obrigatória.
4. A senha temporária é entregue por canal interno controlado.

**Alternativas e bloqueios:** e-mail duplicado, role reservada, confirmação divergente ou credencial
inválida impedem a criação.

**Resultado:** usuário criado sem acesso normal até trocar a senha.

## FL016 — Atualizar usuário e roles

- **Requisitos relacionados:** RN001, RN003, RN004, RN007, RF020, RF016
- **Ator:** administrador autorizado
- **Autorização:** `USER_UPDATE` ou Root
- **Gatilho:** confirmação da edição de usuário não Root

**Fluxo principal:** o administrador altera nome, e-mail e conjunto de roles; o backend valida
unicidade e substitui os vínculos conforme o conjunto informado.

**Alternativas e bloqueios:** conta Root, role reservada ou e-mail duplicado impedem a alteração.

**Resultado:** dados e roles do usuário atualizados sem permissions diretas.

## FL017 — Bloquear ou desbloquear usuário

- **Requisitos relacionados:** RN007, RN010, RN013, RF021, RF022, RNF006
- **Ator:** administrador autorizado
- **Autorização:** `USER_UPDATE` ou Root
- **Gatilho:** ação de bloquear ou desbloquear usuário não Root

**Fluxo principal:**

- no bloqueio, o backend altera o estado, preserva roles e referências e revoga famílias de refresh;
- no desbloqueio, preserva roles e não restaura sessões anteriores; novo login é necessário.

**Alternativas e bloqueios:** o administrador não pode atuar sobre Root nem sobre a própria conta.

**Resultado:** estado de acesso alterado sem usar exclusão como bloqueio.

## FL018 — Excluir usuário

- **Requisitos relacionados:** RN007, RN011, RN013, RF023
- **Ator:** administrador autorizado
- **Autorização:** `USER_DELETE` ou Root
- **Gatilho:** solicitação de exclusão de usuário não Root

**Fluxo principal:** o backend confirma ausência de referências impeditivas, revoga famílias de
refresh, remove vínculos técnicos dependentes e exclui fisicamente o usuário.

**Alternativas e bloqueios:** Root, própria conta ou usuário com referências de negócio não pode ser
excluído; o bloqueio é apresentado como alternativa quando aplicável.

**Resultado:** usuário elegível excluído ou operação recusada sem perda de histórico.

## FL019 — Redefinir senha administrativamente

- **Requisitos relacionados:** RN007, RN012, RF024, RNF001, RNF002
- **Ator:** administrador autorizado
- **Autorização:** `USER_UPDATE` ou Root
- **Gatilho:** ação separada de redefinição sobre usuário não Root

**Fluxo principal:**

1. O administrador informa e confirma uma senha temporária.
2. O backend substitui o hash sem exigir a senha anterior, marca troca obrigatória e revoga todas
   as famílias de refresh token.
3. A senha é entregue por canal interno controlado.

**Alternativas e bloqueios:** a operação não se aplica ao Root por administrador comum; redefinir a
senha de usuário bloqueado não o desbloqueia.

**Resultado:** senha temporária definida, sessões revogadas e troca obrigatória ativada.

## FL020 — Tratar rota inexistente

- **Requisitos relacionados:** RF025
- **Ator:** visitante ou usuário autenticado
- **Gatilho:** navegação para URL inexistente

**Fluxo principal:** visitante é direcionado ao login; usuário autenticado é direcionado à home ou
outra área autenticada segura.

**Resultado:** navegação recuperada sem expor recurso inexistente ou área não autorizada.

[Voltar para o escopo](./scope.md) | [Voltar para a especificação](./README.md) |
[Ir para a arquitetura](./architecture.md)
