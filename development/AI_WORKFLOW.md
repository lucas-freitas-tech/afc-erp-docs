# Desenvolvimento com IA — AFC ERP

Este documento define como ferramentas de inteligência artificial auxiliam o desenvolvimento do AFC ERP. A IA apoia a análise, o planejamento, a implementação e a revisão, mas as decisões sobre o produto e a aprovação das alterações permanecem sob responsabilidade do desenvolvedor.

## Responsabilidades

### Desenvolvedor

- define o escopo, as regras de negócio e as restrições da tarefa;
- esclarece decisões que não podem ser concluídas a partir do repositório;
- acompanha as alterações pelo diff e pelos arquivos modificados;
- aprova a solução, o stage e o commit.

### Codex — planejamento e revisão

- investiga o código e a documentação existentes antes de propor mudanças;
- pesquisa documentação oficial e práticas atuais quando a decisão depender de informações externas;
- identifica impactos em arquitetura, segurança, dados, testes e compatibilidade;
- compara alternativas e justifica a solução recomendada;
- produz um plano executável pelo Cursor;
- revisa a implementação e os testes quando solicitado.

### Cursor — implementação

- usa o Composer como executor padrão das tarefas cotidianas;
- pode usar um modelo de raciocínio mais profundo em investigações ou tarefas complexas;
- confirma o plano contra o estado atual do repositório antes de editar;
- adapta detalhes de implementação quando encontrar divergências, sem alterar o objetivo definido;
- executa as verificações aplicáveis e apresenta as mudanças para revisão.

O modelo utilizado pode mudar conforme a evolução das ferramentas. As responsabilidades acima são mais importantes do que um modelo específico.

## Fluxo de trabalho

### Alterações simples

Correções localizadas, ajustes visuais e mudanças pequenas podem ser executados diretamente no Cursor, desde que o escopo e os critérios de aceitação estejam claros.

1. O desenvolvedor descreve o resultado esperado.
2. O Cursor investiga e implementa a alteração.
3. O Cursor executa os testes e verificadores relacionados.
4. O desenvolvedor revisa o diff e valida o comportamento.

### Alterações complexas

Novos módulos, mudanças arquiteturais, autenticação, autorização, operações financeiras, estoque, integrações e migrações relevantes devem passar por planejamento prévio.

1. O desenvolvedor fornece o escopo, as regras de negócio e as restrições conhecidas.
2. O Codex investiga o repositório e, quando necessário, fontes oficiais atuais.
3. O Codex registra alternativas, decisões, riscos, etapas e critérios de aceitação.
4. O Cursor verifica se o plano corresponde ao código atual e realiza a implementação.
5. O Cursor executa testes, lint, formatação e build aplicáveis.
6. O desenvolvedor revisa as alterações.
7. Quando o risco justificar, o Codex faz uma revisão final do diff, dos testes e da aderência ao plano.

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
- Código gerado por IA segue os mesmos padrões de arquitetura, qualidade, testes e segurança aplicados ao restante do projeto.
- Nenhuma resposta da IA substitui testes automatizados, documentação oficial ou revisão do desenvolvedor.

[Voltar para o início](../README.md)
