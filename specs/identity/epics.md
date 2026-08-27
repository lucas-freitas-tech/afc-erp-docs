# Épicos do módulo Identity

Os IDs semânticos abaixo identificam capacidades permanentes do produto e também formam o prefixo
das tasks no board (`AUTH-NN`, `RBAC-NN` e `ADMIN-NN`). O épico `INFRA` trata do bootstrap
transversal do AFC ERP e, por isso, não integra a especificação normativa do módulo Identity.

## AUTH — Autenticação e sessão

### Objetivo

Permitir que usuários autorizados iniciem, restaurem e encerrem sessões com proteção adequada de
credenciais e tratamento da senha temporária.

### Valor entregue

O usuário acessa o ERP com uma sessão segura e previsível, enquanto contas bloqueadas, credenciais
inválidas e sessões restritas não alcançam recursos indevidos.

### Requisitos relacionados

- Regras: RN010, RN012 e RN013.
- Funcionais: RF001 a RF008 e RF025.
- Não funcionais: RNF001 a RNF006.
- Fluxos: FL001 a FL007 e FL020.

### Capacidades

- signin e restauração de sessão;
- renovação rotativa e logout;
- proteção de rotas autenticadas;
- troca obrigatória de senha temporária;
- consulta e manutenção segura do próprio perfil e senha.

### Critérios de sucesso

- somente usuário ativo com credenciais válidas inicia ou renova sessão;
- senha temporária não concede uma sessão normal;
- logout e operações de credencial revogam as sessões previstas na spec;
- falhas não expõem informações sensíveis;
- recursos protegidos rejeitam sessão ausente, inválida ou insuficiente.

## RBAC — Controle de acesso

### Objetivo

Autorizar capacidades por permissions efetivas agrupadas em roles, incluindo o bypass e o
isolamento reservados ao Root.

### Valor entregue

As regras de acesso permanecem explícitas e desacopladas dos nomes de perfil, permitindo evoluir a
organização administrativa sem enfraquecer a segurança.

### Requisitos relacionados

- Regras: RN002 a RN007, RN009 e RN014.
- Funcionais: RF004 e RF009 a RF016.
- Não funcionais: RNF003 e RNF006.
- Fluxos: FL003 e FL008 a FL012.

### Capacidades

- resolução da união de permissions das roles;
- autorização no backend por permission;
- consulta do catálogo técnico imutável;
- consulta, criação, atualização e exclusão condicionada de roles;
- associação integral de permissions a roles;
- validação das dependências de leitura nas permissions de escrita;
- bypass e isolamento do Root.

### Critérios de sucesso

- nenhuma funcionalidade protegida depende diretamente de uma role comum;
- o frontend não substitui a decisão de autorização do backend;
- o catálogo não aceita escrita administrativa;
- a Role Root permanece fora dos fluxos comuns;
- uma role com usuário ativo vinculado não pode ser excluída;
- uma role não pode ser salva com permission de escrita sem o `READ` correspondente.

## ADMIN — Administração de usuários e acessos

### Objetivo

Permitir que administradores autorizados mantenham usuários, seus estados, credenciais temporárias
e vínculos com roles sem violar as proteções do Root e da própria conta.

### Valor entregue

O ERP mantém o ciclo de vida dos acessos em um fluxo administrativo controlado, reversível quando
possível e compatível com a preservação futura de referências de negócio.

### Requisitos relacionados

- Regras: RN001, RN003, RN004 e RN007 a RN014.
- Funcionais: RF009 a RF024.
- Não funcionais: RNF001 a RNF004 e RNF006.
- Fluxos: FL008 a FL019.

### Capacidades

- pesquisa e consulta administrativa de usuários;
- criação e atualização de usuário e de seus vínculos com roles;
- bloqueio e desbloqueio;
- exclusão física condicionada;
- redefinição administrativa com senha temporária;
- uso auxiliar dos catálogos de roles e permissions.

### Critérios de sucesso

- a conta Root não aparece nem pode ser alterada nos fluxos comuns;
- o administrador não bloqueia nem exclui a própria conta;
- o bloqueio preserva vínculos e impede novas sessões;
- a exclusão respeita referências impeditivas;
- criação e redefinição de senha exigem troca antes do acesso normal;
- respostas e logs não expõem credenciais ou hashes.

---

[Voltar para a arquitetura](./architecture.md) | [Ir para a especificação](./README.md) | [Ir para as histórias](https://github.com/orgs/lucas-freitas-tech/projects/3/views/1)
