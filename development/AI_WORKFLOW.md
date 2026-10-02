# Desenvolvimento com IA — AFC ERP

Este documento define como ferramentas de inteligência artificial auxiliam o desenvolvimento do AFC ERP. A IA apoia especificação, planejamento, implementação e revisão, mas as decisões de produto, o controle do Git e a aprovação das entregas permanecem sob responsabilidade do desenvolvedor.

## Responsabilidades

### Desenvolvedor

- define o escopo, as regras de negócio e as restrições da tarefa;
- esclarece decisões que não podem ser concluídas a partir do repositório;
- acompanha as alterações pelo diff e pelos arquivos modificados;
- aprova a solução, o stage, o commit e o merge;
- conduz ou acompanha a validação do cliente e encerra manualmente a história.

### Codex — planejamento e revisão

- investiga o código e a documentação existentes antes de propor mudanças;
- pesquisa documentação oficial e práticas atuais quando a decisão depender de informações externas;
- identifica impactos em arquitetura, segurança, dados, testes e compatibilidade;
- compara alternativas e justifica a solução recomendada;
- cria e refina histórias a partir da spec quando acionado para essa finalidade;
- produz planos temporários e rastreáveis por repositório para execução pelo Cursor;
- revisa a implementação e os testes quando solicitado.

### Cursor — implementação

- usa o Composer como executor padrão das tarefas cotidianas;
- pode usar um modelo de raciocínio mais profundo em investigações ou tarefas complexas;
- confirma o plano contra o estado atual do repositório antes de editar;
- adapta detalhes de implementação quando encontrar divergências, sem alterar o objetivo definido;
- executa as verificações aplicáveis e apresenta as mudanças para revisão.

O modelo utilizado pode mudar conforme a evolução das ferramentas. As responsabilidades acima são
mais importantes do que um modelo específico.

## Fluxo de trabalho

### Alterações simples

Correções localizadas, ajustes visuais e mudanças pequenas podem ser executados diretamente no
Cursor, desde que o escopo e os critérios de aceitação estejam claros e que a mudança não dependa de
uma decisão pendente de produto ou arquitetura.

1. O desenvolvedor descreve o resultado esperado.
2. O Cursor investiga e implementa a alteração.
3. O Cursor executa os testes e verificadores relacionados.
4. O desenvolvedor revisa o diff e valida o comportamento.

Esse fluxo não exige automaticamente task, subissues, planos ou Pull Request. O desenvolvedor pode
adotar esses controles quando o risco da alteração justificar.

### Alterações complexas

Novos módulos, mudanças arquiteturais, autenticação, autorização, operações financeiras, estoque,
integrações e migrations relevantes devem passar pelo fluxo rastreado abaixo.

#### 1. Especificar a entrega

1. O desenvolvedor e o Codex discutem o comportamento, as regras e as decisões relevantes.
2. Mudanças de produto ou arquitetura são aprovadas e registradas primeiro na spec correspondente.
3. A spec descreve a visão final da entrega; não registra andamento de implementação.

#### 2. Criar e refinar a história

1. A skill `afc-erp-task-writer` recorta uma entrega observável sustentada pela spec.
2. Backend e frontend permanecem na mesma história quando compõem o mesmo valor de produto.
3. Após autorização, a skill publica a task principal no `afc-erp-docs` e no GitHub Project do AFC
   ERP.
4. O desenvolvedor posiciona a task no backlog e a move para `Refinamento` quando ela estiver pronta
   para planejamento técnico.

#### 3. Planejar e iniciar a implementação

1. A skill `afc-erp-plan-handoff` seleciona uma única task em `Refinamento`.
2. O Codex confirma a task contra a spec e investiga os repositórios realmente afetados.
3. É criado um plano temporário em cada repositório afetado, com escopo isolado, ordem e
   dependências explícitas.
4. Após autorização, a skill cria uma subissue no backend e/ou frontend para cada plano de código,
   vincula as subissues à task principal e registra suas URLs nos planos.
5. A skill cria em cada repositório uma branch local a partir de uma `main` limpa e sincronizada com
   `origin/main`. O nome contém o ID da task e o número da subissue.
6. Somente depois de confirmar vínculos, planos e branches, a skill move a task principal para
   `🚧 Em Desenvolvimento`.

#### 4. Implementar

1. O Cursor executa os planos na ordem indicada e somente dentro do repositório de cada plano.
2. Um plano dependente não deve ser executado enquanto o arquivo do plano que o bloqueia continuar
   presente.
3. O Cursor confirma contratos e estado atual antes de editar, implementa a mudança e executa os
   testes, lint, formatação, type check e build aplicáveis.
4. O plano é removido depois da conclusão do escopo e das validações, mas antes de qualquer stage ou
   commit. O nome da branch preserva a referência necessária para o futuro PR.
5. O desenvolvedor revisa os diffs e controla stage, commit e push.

#### 5. Revisar e integrar

1. Cada implementação rastreada por subissue é entregue por um Pull Request para a branch padrão do
   mesmo repositório.
2. O body do PR contém exatamente `Closes #<número-da-subissue>` para que o merge conclua a unidade
   técnica correspondente.
3. O workflow valida o vínculo entre PR, subissue, task principal e GitHub Project. No plano atual do
   GitHub, esse check é informativo e o desenvolvedor não deve realizar o merge quando ele falhar.
4. Quando o risco justificar, a skill geral `multi-repo-integration-review` revisa as branches e os
   contratos entre os repositórios antes do merge. Essa revisão é manual, somente leitura e não é um
   gate automatizado.
5. Correções encontradas na revisão são implementadas e validadas antes da integração.
6. Os PRs são integrados respeitando a ordem dos contratos, quando houver dependência entre eles.

#### 6. Aguardar validação

1. Cada merge na branch padrão fecha sua subissue como concluída pelo vínculo `Closes #N`.
2. Enquanto alguma subissue estiver pendente, a task principal permanece em
   `🚧 Em Desenvolvimento`.
3. Quando todas as subissues estiverem fechadas como concluídas, o workflow move a task para
   `Aguardando validação`.
4. Se uma subissue for reaberta, o workflow devolve a task para `🚧 Em Desenvolvimento`.
5. Subissue fechada como não planejada não conta como implementação concluída.

#### 7. Validar e encerrar

1. O cliente, ou o desenvolvedor responsável pela validação nesta fase do produto, verifica a
   história completa e seus critérios de aceitação.
2. A automação não interpreta merge como aceite funcional e nunca conclui a task principal.
3. Depois do aceite, o desenvolvedor move manualmente a task para `Concluído` e fecha sua issue no
   `afc-erp-docs`.

O tratamento formal de ajustes solicitados durante a validação não possui uma skill especializada
neste momento e permanece fora deste fluxo documentado.

## Conteúdo esperado de um plano

Um plano deve ser proporcional ao risco da tarefa e, quando aplicável, apresentar:

1. estado atual relevante do sistema;
2. objetivo, requisitos e itens fora do escopo;
3. regras de negócio e restrições técnicas;
4. alternativas consideradas e justificativa da escolha;
5. impacto na arquitetura, no modelo de dados e nos contratos de API;
6. riscos de segurança, consistência, concorrência e compatibilidade;
7. etapas de implementação e arquivos ou componentes envolvidos;
8. estratégia de migração e reversibilidade, quando necessária;
9. testes, verificadores e critérios de aceitação;
10. decisões que ainda dependem do desenvolvedor.

O plano orienta a execução, mas não substitui a leitura do código. Caso o executor encontre uma divergência relevante, deve interromper a suposição, explicar o impacto e ajustar o plano ou solicitar uma decisão.

## Escolha de tecnologias e arquitetura

Tecnologias não devem ser escolhidas apenas por popularidade ou recomendação genérica da IA. A decisão deve considerar:

- adequação ao problema e à escala esperada;
- compatibilidade com a arquitetura e as versões atuais do projeto;
- segurança e manutenção;
- maturidade, documentação e suporte do ecossistema;
- custo operacional e complexidade introduzida;
- possibilidade de testar, migrar e substituir a solução.

Deve-se preferir a solução mais simples que cubra os requisitos e riscos reais. Decisões arquiteturais duradouras devem ser registradas na documentação do módulo correspondente, e não permanecer somente na conversa com a IA.

## Controle das alterações

- Ferramentas de IA não devem executar `git add`, `git commit` ou `git push` sem solicitação explícita do desenvolvedor na tarefa atual.
- Ao concluir a implementação, os arquivos devem permanecer no working tree para revisão.
- Código gerado por IA segue os mesmos padrões de arquitetura, qualidade, testes e segurança
  aplicados ao restante do projeto.
- Nenhuma resposta da IA substitui testes automatizados, documentação oficial, revisão do
  desenvolvedor ou validação do cliente.
- As regras de branches, Pull Requests e transições automatizadas estão detalhadas no
  [Fluxo Git](./GIT_WORKFLOW.md).

[Voltar para o início](../README.md)
