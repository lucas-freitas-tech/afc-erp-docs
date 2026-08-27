# Escopo do módulo Identity

## Visão geral

O módulo Identity fornece autenticação, sessão, perfil do usuário e controle de acesso baseado em
roles e permissions para o AFC ERP. Esta spec descreve a entrega final prevista para a primeira
versão do módulo.

## Objetivos

- autenticar usuários e manter sessões com renovação controlada;
- autorizar operações por permissions efetivas, sem usar roles diretamente como autoridade;
- permitir que usuários mantenham os próprios dados e credenciais autorizados;
- permitir a administração de usuários, roles e do catálogo de permissions;
- proteger a conta Root, credenciais e operações administrativas críticas.

## Atores

| Ator | Responsabilidade |
|---|---|
| Visitante | Acessar rotas públicas e iniciar autenticação |
| Usuário autenticado | Acessar capacidades autorizadas e manter o próprio perfil |
| Administrador autorizado | Gerenciar usuários, roles e permissions conforme suas permissions |
| Root | Administrar qualquer recurso pelo bypass reservado à conta Root |

## Capacidades incluídas

### Autenticação e sessão

- login com e-mail e senha;
- restauração e renovação da sessão;
- logout;
- proteção de rotas e operações autenticadas;
- sessão restrita e troca obrigatória de senha temporária.

### Perfil e credenciais próprias

- consulta e atualização dos próprios dados permitidos;
- troca voluntária da própria senha.

### Administração de acessos

- consulta do catálogo técnico de permissions;
- consulta, criação, edição e exclusão de roles;
- associação de permissions a roles;
- consulta, criação, edição, bloqueio, desbloqueio e exclusão condicionada de usuários;
- associação de roles a usuários;
- redefinição administrativa de senha.

## Regras de negócio

### RN001 — E-mail único

Cada usuário deve possuir um e-mail de autenticação único no sistema.

### RN002 — Autorização por permissions

O acesso às funcionalidades deve ser autorizado por permissions efetivas, nunca diretamente pelo
nome ou código de uma role.

### RN003 — Concessão por roles

Usuários recebem permissions exclusivamente por meio de roles. Não existem permissions atribuídas
diretamente a usuários.

### RN004 — União das permissions

As permissions efetivas de um usuário correspondem à união das permissions de todas as suas roles.
Um usuário pode permanecer sem roles e, nesse estado, não acessa capacidades protegidas por
permission.

### RN005 — Catálogo de permissions imutável

Permissions são definidas pelo sistema e não podem ser criadas, editadas ou removidas pela
administração. Cada permission possui código técnico imutável, nome de apresentação, descrição e
`module_code` obrigatório.

### RN006 — Acesso Root

A conta Root possui acesso a qualquer recurso independentemente das roles e permissions associadas.
A Role Root é reservada a essa conta e concede acesso total sem vínculos no catálogo de
permissions.

### RN007 — Isolamento do Root

A conta e a Role Root não aparecem nos fluxos administrativos comuns. Administradores comuns não
podem criar outro Root, alterar seus acessos, redefinir sua senha, bloqueá-lo ou excluí-lo.

### RN008 — Exclusão de role

A exclusão de uma role é bloqueada enquanto existir usuário ativo vinculado. Vínculos apenas com
usuários bloqueados podem ser removidos com a role, sem remover permissions do catálogo.

### RN009 — Vínculos de role

Uma role pode possuir zero ou mais permissions. Ao atualizar uma role, o conjunto informado
substitui integralmente seus vínculos anteriores.

### RN010 — Bloqueio de usuário

O bloqueio é um estado reversível que impede novos signins e refreshes, revoga todas as famílias de
refresh token e preserva roles e referências. Access tokens stateless já emitidos permanecem
utilizáveis somente até sua expiração.

### RN011 — Exclusão de usuário

A exclusão é física, excepcional e permitida somente para usuário não Root sem referências de
negócio que precisem ser preservadas. A operação não exige bloqueio prévio, revoga as famílias de
refresh token e remove apenas vínculos técnicos dependentes. Quando houver referências impeditivas,
a exclusão deve ser recusada e o bloqueio permanece como alternativa.

### RN012 — Credencial temporária

Uma senha temporária não concede acesso normal. Usuários criados com senha temporária ou submetidos
a redefinição administrativa devem definir uma senha definitiva antes de acessar as demais
capacidades.

### RN013 — Proteção da própria conta

O usuário autenticado não pode bloquear nem excluir a própria conta pelos fluxos administrativos.
O fluxo de perfil não permite alterar roles, permissions ou estado da conta.

### RN014 — Dependência de leitura nas roles

Uma role que possua permission de criação, atualização ou exclusão de um domínio deve possuir
também a permission de leitura correspondente. Para o Identity, `USER_CREATE`, `USER_UPDATE` ou
`USER_DELETE` exigem `USER_READ`, enquanto `ROLE_CREATE`, `ROLE_UPDATE` ou `ROLE_DELETE` exigem
`ROLE_READ`. A combinação inválida deve ser rejeitada na criação ou atualização da role.

## Requisitos funcionais

### RF001 — Autenticar usuário

Quando um usuário ativo informar credenciais válidas, o sistema deve iniciar uma sessão normal ou
restrita conforme o estado da senha. Credenciais inválidas e usuários bloqueados não iniciam sessão.

### RF002 — Restaurar e renovar sessão

O sistema deve restaurar ou renovar uma sessão quando o refresh token for válido e rejeitar tokens
inválidos, expirados, reutilizados, revogados ou pertencentes a usuário bloqueado.

### RF003 — Encerrar sessão

Quando o usuário solicitar logout, o sistema deve revogar a família do refresh token, expirar o
cookie correspondente, limpar a sessão local e retornar o usuário ao login.

### RF004 — Proteger recursos

Recursos autenticados devem rejeitar sessões ausentes ou inválidas. Recursos protegidos por
permission devem ser autorizados efetivamente pelo backend, incluindo o bypass reservado ao Root.

### RF005 — Exigir troca de senha temporária

Enquanto a senha for temporária, o sistema deve permitir somente troca de senha e logout. Após a
troca, deve encerrar a sessão restrita e exigir novo login.

### RF006 — Consultar o próprio perfil

O usuário autenticado deve poder consultar os próprios dados permitidos, identificados pela sessão
e sem escolher outro usuário.

### RF007 — Atualizar o próprio perfil

O usuário autenticado deve poder atualizar somente os próprios campos pessoais permitidos.

### RF008 — Trocar a própria senha

O usuário autenticado deve poder trocar a própria senha após informar a senha atual e confirmar uma
nova senha válida e diferente da atual. A operação deve revogar suas famílias de refresh token,
encerrar a sessão local e exigir novo login.

### RF009 — Consultar permissions

Usuários com `PERMISSION_READ` ou Root devem poder consultar o catálogo de permissions com pesquisa
por código, nome ou descrição e paginação, sem operações de escrita.

### RF010 — Consultar permissions para gestão de roles

A mesma rota de listagem do catálogo deve autorizar `PERMISSION_READ`, `ROLE_READ` ou Root, com
semântica OR. `ROLE_READ` sustenta filtros e formulários de roles, mas não concede acesso à página
administrativa de permissions.

### RF011 — Listar roles

Usuários com `ROLE_READ` ou Root devem poder listar roles com paginação, pesquisa por código, nome
ou descrição e filtro por uma ou mais permissions combinadas com semântica AND. A Role Root não deve
aparecer.

### RF012 — Consultar role

Usuários com `ROLE_READ` ou Root devem poder consultar uma role e suas permissions atribuídas em
modo somente leitura.

### RF013 — Criar role

Usuários com `ROLE_CREATE` ou Root devem poder criar uma role com código administrativo único, nome,
descrição e zero ou mais permissions. Antes de persistir, o sistema deve validar as dependências
definidas na RN014 e rejeitar o cadastro inconsistente.

### RF014 — Atualizar role

Usuários com `ROLE_UPDATE` ou Root devem poder atualizar código, nome, descrição e conjunto de
permissions de uma role não reservada. O conjunto final deve atender à RN014; caso contrário, a
alteração deve ser rejeitada sem modificar a role.

### RF015 — Excluir role

Usuários com `ROLE_DELETE` ou Root devem poder excluir uma role não reservada quando a RN008 for
atendida.

### RF016 — Consultar roles para gestão de usuários

A mesma rota de listagem de roles deve autorizar `ROLE_READ`, `USER_READ` ou Root, com semântica OR.
`USER_READ` sustenta a seleção de roles na gestão de usuários, mas não concede acesso à página
administrativa de roles.

### RF017 — Listar usuários

Usuários com `USER_READ` ou Root devem poder listar usuários com paginação, pesquisa por nome ou
e-mail e filtro de estado entre ativos, bloqueados ou todos. A conta Root não deve aparecer.

### RF018 — Consultar usuário

Usuários com `USER_READ` ou Root devem poder consultar dados administrativos e roles de um usuário
não Root, sem expor credenciais ou hashes.

### RF019 — Criar usuário

Usuários com `USER_CREATE` ou Root devem poder criar usuário com nome, e-mail único, roles opcionais
e senha temporária confirmada.

### RF020 — Atualizar usuário

Usuários com `USER_UPDATE` ou Root devem poder atualizar nome, e-mail e conjunto de roles de um
usuário não Root, preservando a unicidade do e-mail.

### RF021 — Bloquear usuário

Usuários com `USER_UPDATE` ou Root devem poder bloquear outro usuário não Root conforme RN010 e
RN013.

### RF022 — Desbloquear usuário

Usuários com `USER_UPDATE` ou Root devem poder desbloquear usuário preservando suas roles, sem
restaurar sessões anteriores e exigindo nova autenticação.

### RF023 — Excluir usuário

Usuários com `USER_DELETE` ou Root devem poder excluir outro usuário não Root quando a RN011 e a
RN013 forem atendidas.

### RF024 — Redefinir senha administrativamente

Usuários com `USER_UPDATE` ou Root devem poder substituir a senha de um usuário não Root por uma
senha temporária confirmada, sem conhecer a senha anterior. A operação deve revogar todas as
famílias de refresh token e não deve desbloquear uma conta bloqueada.

### RF025 — Tratar navegação inválida

O frontend deve direcionar visitantes sem sessão para o login, sessões restritas para a troca de
senha e usuários autenticados sem acesso ou diante de rota inexistente para uma área autenticada
segura.

## Requisitos não funcionais

### RNF001 — Confidencialidade de credenciais

Senhas, hashes, refresh tokens e demais credenciais não devem ser retornados em respostas,
registrados em logs ou expostos em mensagens de erro.

### RNF002 — Armazenamento de senhas

Senhas devem ser persistidas exclusivamente como hashes produzidos por mecanismo adaptativo
aprovado na arquitetura. Valores em claro não podem ser persistidos.

### RNF003 — Autoridade no backend

Controles de frontend servem apenas à experiência do usuário. Toda autorização e restrição de
segurança deve ser aplicada pelo backend independentemente da navegação ou visibilidade da UI.

### RNF004 — Proteção da renovação

O mecanismo de renovação deve impedir leitura do refresh token pelo frontend, proteger operações
baseadas em cookie contra CSRF e rejeitar reutilização de token.

### RNF005 — Sigilo das falhas de autenticação

Falhas de autenticação e renovação não devem revelar se e-mail, senha, token ou estado específico da
conta causou a rejeição.

### RNF006 — Validade limitada das autorizações

Permissions, estado e bypass Root presentes em uma sessão devem ser recalculados no signin e no
refresh. Alterações posteriores podem permanecer refletidas em access tokens já emitidos somente
até sua expiração configurada.

## Dependências e limites

- OpenAPI é a fonte canônica dos contratos HTTP detalhados; esta spec define suas regras e
  garantias.
- O Identity é um módulo lógico do monólito e não um microsserviço independente.
- O provisionamento definitivo do Root em produção e a validação integrada do catálogo permanecem
  nos requisitos obrigatórios adiados para o primeiro deploy.
- O canal de entrega de senha temporária é interno e controlado nesta versão; envio por e-mail não
  faz parte da entrega.

## Fora do escopo

- login social;
- recuperação pública de senha ou por e-mail;
- confirmação de e-mail no cadastro;
- autenticação em dois fatores;
- auditoria detalhada de ações;
- revogação imediata de access tokens stateless já emitidos;
- criação, edição ou remoção administrativa de permissions;
- seleção em massa de permissions na gestão de roles;
- filtro de usuários por role.

[Voltar para a especificação](./README.md) | [Ir para os fluxos](./flows.md)
