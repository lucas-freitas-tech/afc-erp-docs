# Visão Geral do Produto — AFC ERP

## Sobre o projeto

O AFC ERP é um sistema interno de gestão criado para centralizar e apoiar os processos administrativos da AFC Placas. O produto reúne, em uma única aplicação web, as funcionalidades necessárias para a operação da empresa e oferece uma base comum de identidade, segurança e controle de acesso.

O sistema está em desenvolvimento e evolui de forma incremental. Cada nova capacidade é organizada em um módulo com responsabilidades próprias, integrado à mesma experiência de uso e à infraestrutura compartilhada do ERP.

## Problema que o produto resolve

O AFC ERP busca oferecer um ponto central para a execução dos processos administrativos da AFC Placas. Essa centralização permite:

- reunir funcionalidades de negócio em um único sistema;
- controlar de forma consistente quem pode acessar cada operação;
- manter os dados necessários aos processos administrativos;
- reduzir a fragmentação entre ferramentas e rotinas isoladas;
- acrescentar novos módulos sem perder a organização do produto.

## Público e acesso

O produto é destinado aos usuários autorizados da AFC Placas. O acesso acontece pelo navegador, por meio de uma aplicação web que apresenta apenas as funcionalidades permitidas para cada usuário.

A identidade e as autorizações são centralizadas. Os módulos de negócio utilizam essa base comum para proteger suas operações, mas continuam responsáveis por suas próprias regras e dados.

## Escopo do produto

O AFC ERP é responsável por:

- disponibilizar as funcionalidades administrativas da empresa;
- autenticar os usuários e controlar seus acessos;
- organizar as capacidades do sistema em módulos de negócio;
- armazenar os dados operacionais e de acesso necessários;
- oferecer uma experiência integrada entre os diferentes módulos.

Integrações com sistemas externos ainda não fazem parte do escopo definido. Elas serão incorporadas quando existirem necessidades concretas e especificadas.

## Módulos e evolução

### Identity

O primeiro módulo estabelece a base de autenticação e controle de acesso do ERP. Ele contempla:

- autenticação com e-mail e senha;
- gerenciamento seguro da sessão;
- autorização baseada em permissions e roles;
- gestão de usuários e acessos;
- consulta e atualização do próprio perfil;
- troca voluntária de senha;
- senha temporária e troca obrigatória após criação ou redefinição administrativa.

O Identity é uma capacidade transversal. Sua responsabilidade é identificar o usuário e determinar quais ações ele pode realizar, sem assumir as regras dos demais módulos.

### Orçamentos

O módulo de Orçamentos está planejado como a primeira capacidade administrativa de negócio construída sobre a base de identidade. Seu escopo detalhado ainda será definido e deve incluir inicialmente a criação, listagem e visualização de orçamentos.

Os próximos módulos serão definidos conforme a evolução do produto e as necessidades confirmadas da empresa. A ordem atual pode ser consultada no [Roadmap do Produto](./ROADMAP.md).

## Visão da solução

O AFC ERP é uma aplicação web formada por três blocos principais:

- **Aplicação Web:** interface utilizada pelos usuários para acessar os módulos;
- **Aplicação do ERP:** concentra as regras, a identidade e as capacidades de negócio;
- **Base de Dados:** armazena os dados operacionais e de controle de acesso.

A organização modular é lógica e não significa que cada módulo seja um sistema ou serviço independente. A descrição técnica completa está na [Arquitetura Global](../architecture/architecture.md).

## Princípios do produto

- **Centralização:** oferecer um único ponto de acesso aos processos administrativos.
- **Segurança:** aplicar autenticação e autorização de forma consistente.
- **Modularidade:** manter responsabilidades e regras de cada domínio bem delimitadas.
- **Evolução incremental:** implementar capacidades de acordo com necessidades confirmadas.
- **Simplicidade:** evitar complexidade técnica ou operacional sem benefício concreto.
- **Sustentabilidade:** permitir que o sistema cresça sem comprometer sua manutenção.

## Estado atual

O produto encontra-se em desenvolvimento. A base de identidade e segurança constitui a primeira fase do projeto, enquanto o módulo de Orçamentos representa a próxima fase planejada. Os documentos de cada módulo registram o escopo confirmado, os fluxos, a arquitetura e as etapas de implementação.

[Voltar para o início](../README.md)
