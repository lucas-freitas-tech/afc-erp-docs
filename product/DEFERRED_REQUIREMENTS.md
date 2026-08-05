# Requisitos obrigatórios adiados

Este arquivo registra requisitos já decididos que ainda não pertencem às especificações atuais.
Eles não são opcionais. Devem ser planejados, implementados e incorporados à documentação
definitiva antes do primeiro deploy em produção, previsto após a conclusão do módulo 2.

Depois que todos os itens forem transferidos para suas especificações definitivas, este arquivo
deve ser removido.

## Provisionamento inicial do Root

- O primeiro deploy deve provisionar um usuário Root de forma segura e idempotente.
- Credenciais e senhas iniciais não podem ser armazenadas no repositório ou em migrations.
- A senha inicial deve ser fornecida por um mecanismo seguro de secrets do ambiente de deploy.
- O usuário deve ser obrigado a trocar a senha inicial.
- Deploys posteriores não podem recriar o Root nem redefinir sua senha.
- Uma falha no provisionamento obrigatório do primeiro Root deve impedir a conclusão do primeiro
  deploy.
- O `DevelopmentUserInitializer` não deve ser utilizado para provisionamento em produção.

Destino futuro da documentação: especificação do Identity e documentação da arquitetura de
deploy.

## Validação integrada das permissões e dos módulos

- Antes do primeiro deploy, deve existir um teste de contrato integrado entre banco, backend e
  frontend.
- O teste deve executar as migrations em um banco descartável, iniciar o backend e consultar o
  catálogo real de permissões por meio da API.
- O teste deve autenticar com um Root exclusivo do ambiente de testes e nunca utilizar credenciais
  de produção.
- Todos os códigos de permissões utilizados pelo frontend devem existir no catálogo retornado pelo
  backend.
- Toda permissão persistida deve possuir um identificador de módulo válido e não vazio.
- O catálogo retornado pelo backend deve expor o identificador de módulo associado a cada
  permissão.
- Os identificadores de módulos utilizados pelas permissões do frontend devem corresponder aos
  módulos conhecidos pelo manifesto de navegação do frontend.
- O teste deve validar conjuntamente o código da permissão e seu módulo, detectando permissões
  cadastradas ou utilizadas no módulo incorreto.
- O backend pode possuir permissões que a versão atual do frontend ainda não utiliza.
- Permissões ou módulos ainda desconhecidos pela versão atual do frontend não podem desaparecer
  silenciosamente da interface; deve existir um tratamento visual seguro para contratos novos.
- A validação não depende da renderização de telas; trata-se de um teste integrado de contrato da
  API.
- Uma incompatibilidade entre códigos ou módulos utilizados pelo frontend e o catálogo produzido
  pelo backend e pelas migrations deve impedir o deploy.

Destino futuro da documentação: estratégia de testes, pipeline de integração e documentação dos
contratos de autorização e módulos.
