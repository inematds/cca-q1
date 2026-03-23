# Plano de Curso — Claude Certified Architect (CCA-F)

> Preparatorio completo para a certificacao Claude Certified Architect - Foundations da Anthropic.

---

## Contexto da Certificacao

| Item | Detalhe |
|------|---------|
| Nome | Claude Certified Architect — Foundations (CCA-F) |
| Lancamento | 12 de marco de 2026 |
| Formato | 60 questoes multipla escolha (cenarios), 120 minutos, com proctoring |
| Nota minima | 720 / 1.000 |
| Custo | Gratis (primeiros 5.000 via Partner Network) / US$ 99 por tentativa |
| Registro | Via Claude Partner Network em anthropic.skilljar.com |
| Publico | Arquitetos de solucao com 6+ meses construindo com Claude API, Agent SDK, Claude Code e MCP |

---

## Estrutura do Curso

O curso segue os **5 dominios oficiais do exame**, na proporcao exata dos pesos da prova. Cada dominio e uma **trilha**, e cada conceito-chave e um **modulo** dentro da trilha.

---

## TRILHA 1 — Arquitetura Agenticae Orquestracao (27%)

> A maior trilha. Vale mais de 1/4 da prova. Foco: como agentes pensam, coordenam e impoem regras.

### Modulo 1.1 — O Loop Agenticoi
**Conteudo:**
- Ciclo de vida: enviar requisicao → inspecionar `stop_reason` → executar ferramenta → retornar resultado
- `stop_reason`: `tool_use` (continuar) vs `end_turn` (parar)
- Resultados de ferramentas sao adicionados ao historico para o proximo passo de raciocinio
- Decisao guiada pelo modelo vs arvores de decisao pre-configuradas
- **3 anti-padroes**: parsing de texto natural para terminar, limites arbitrarios de iteracao, checar texto do assistente por "completude"

**Exercicio pratico:** Construir um loop agenticobasico com Agent SDK que use `stop_reason` corretamente.

**Quiz:** 3-5 questoes cenario sobre anti-padroes e fluxo do loop.

---

### Modulo 1.2 — Arquitetura Hub-and-Spoke (Coordenador-Subagente)
**Conteudo:**
- Padrao hub-and-spoke: coordenador gerencia toda comunicacao entre subagentes
- Subagentes tem contexto isolado (nao herdam historico do coordenador)
- Papel do coordenador: decomposicao de tarefas, delegacao, agregacao de resultados, roteamento por complexidade
- Risco: decomposicao estreita demais → cobertura incompleta
- Selecao dinamica de subagentes, loops de refinamento iterativo

**Exercicio pratico:** Projetar sistema multi-agente para pesquisa (busca + analise + sintese).

**Quiz:** Questoes sobre isolamento de contexto e erros de decomposicao.

---

### Modulo 1.3 — Invocacao de Subagentes, Passagem de Contexto e Spawning
**Conteudo:**
- Ferramenta `Agent` (antigo `Task`, renomeado v2.1.63) para spawnar subagentes
- `allowedTools` deve incluir "Agent" para o coordenador
- Contexto do subagente deve ser explicitamente fornecido (sem heranca automatica)
- `AgentDefinition`: descricoes, system prompts, restricoes de ferramentas
- `fork_session` para explorar abordagens divergentes
- Spawnar subagentes paralelos em resposta unica do coordenador

**Exercicio pratico:** Configurar `AgentDefinition` com restricoes de ferramentas e contexto explicito.

**Quiz:** Questoes sobre passagem de contexto e configuracao de subagentes.

---

### Modulo 1.4 — Imposicao e Padroes de Handoff
**Conteudo:**
- Imposicao programatica (hooks, prerequisite gates) vs orientacao via prompt
- **Regra de ouro:** Para financas/compliance → hooks (deterministicos), nao prompts (probabilisticos)
- Prompts = sugestoes (funcionam ~99%). Hooks = leis (100% de imposicao)
- Protocolos de handoff estruturado: detalhes do cliente, causa raiz, acoes recomendadas
- Decomposicao de requisicoes multi-preocupacao

**Exercicio pratico:** Implementar hook que bloqueia reembolsos acima de um valor.

**Quiz:** Cenarios financeiros onde a resposta correta e hooks, nao prompts.

---

### Modulo 1.5 — Hooks do Agent SDK
**Conteudo:**
- `PostToolUse` hooks: interceptam resultados de ferramentas para transformacao antes do modelo processar
- Hooks para impor regras de compliance (bloquear refund acima do limite)
- Hooks para normalizacao de dados: timestamps ISO 8601, codigos de status
- Hooks = garantias deterministicas vs prompts = conformidade probabilistica

**Exercicio pratico:** Criar hook `PostToolUse` que normaliza timestamps para ISO 8601.

**Quiz:** Questoes sobre quando usar hooks vs prompts.

---

### Modulo 1.6 — Estrategias de Decomposicao de Tarefas
**Conteudo:**
- Pipelines sequenciais fixos (prompt chaining) vs decomposicao adaptativa dinamica
- Prompt chaining: analisar cada arquivo individualmente, depois passo de integracao cross-file
- Planos de investigacao adaptativos: gerar subtarefas com base em descobertas
- Quando usar chaining (revisoes previsiveis) vs decomposicao dinamica (tarefas abertas)

**Exercicio pratico:** Projetar pipeline de code review com analise por-arquivo + integracao.

**Quiz:** Questoes sobre escolha entre chaining e decomposicao dinamica.

---

### Modulo 1.7 — Estado de Sessao, Retomada e Forking
**Conteudo:**
- `--resume <session-name>` para continuar sessoes nomeadas
- `fork_session` para branches de exploracao paralela
- Nova sessao com resumo estruturado > retomar com resultados de ferramentas velhos
- Sessoes nomeadas para investigacao, forking para comparar abordagens

**Exercicio pratico:** Cenario onde o aluno decide entre resume, fork ou nova sessao.

**Quiz:** Questoes sobre gerenciamento de estado de sessao.

---

## TRILHA 2 — Design de Ferramentas e Integracao MCP (18%)

> Como Claude se conecta ao mundo externo. Foco: descricoes de ferramentas, erros estruturados, escopo.

### Modulo 2.1 — Design de Interfaces de Ferramentas
**Conteudo:**
- Descricoes de ferramentas = mecanismo primario para Claude escolher qual ferramenta usar
- Incluir: formato de entrada, tipo de retorno, casos de borda, fronteiras
- Descricoes vagas/sobrepostas causam roteamento incorreto
- Adicionar "NAO use para..." reduz erros drasticamente
- **A descricao E a interface** — corrija descricoes antes de tudo

**Exercicio pratico:** Reescrever descricoes ambiguas de ferramentas para eliminar sobreposicao.

**Quiz:** Cenarios de roteamento incorreto e como corrigir.

---

### Modulo 2.2 — Respostas de Erro Estruturadas para MCP
**Conteudo:**
- Padrao `isError` para comunicar falhas
- Categorias: transiente (timeout), validacao (input ruim), negocio (politica), permissao
- Erros uniformes impedem recuperacao apropriada
- Metadados estruturados: `errorCategory`, `isRetryable`, descricao legivel
- Distinguir falhas de acesso de resultados vazios validos

**Exercicio pratico:** Implementar resposta de erro estruturada com categorias e retryable flag.

**Quiz:** Questoes sobre categorias de erro e recuperacao.

---

### Modulo 2.3 — Distribuicao de Ferramentas e Tool Choice
**Conteudo:**
- 18 ferramentas = agente confuso. 4-5 ferramentas = agente preciso
- Agentes com ferramentas fora do escopo tendem a usa-las incorretamente
- `tool_choice`: "auto", "any", forced (`{"type": "tool", "name": "..."}`)
- Padrao: forcar ferramenta correta no passo 1, depois `auto`
- Restricao e um recurso, nao uma limitacao

**Exercicio pratico:** Projetar distribuicao de ferramentas para sistema multi-agente.

**Quiz:** Cenarios de sobrecarga de ferramentas e como resolver.

---

### Modulo 2.4 — Servidores MCP no Claude Code
**Conteudo:**
- Nivel de projeto (`.mcp.json`): compartilhado via git, variaveis de ambiente para credenciais
- Nivel de usuario (`~/.claude.json`): sandbox pessoal, nao compartilhado
- Expansao de variaveis de ambiente (`${GITHUB_TOKEN}`)
- MCP resources para catalogos de conteudo
- Servidores MCP da comunidade para integracoes padrao vs customizado para fluxos unicos

**Exercicio pratico:** Configurar `.mcp.json` de projeto com servidor MCP e variaveis de ambiente.

**Quiz:** Questoes sobre escopo projeto vs usuario.

---

### Modulo 2.5 — Ferramentas Built-in (Read, Write, Edit, Bash, Grep, Glob)
**Conteudo:**
- Grep = busca por conteudo, Glob = busca por padrao de arquivo
- Read/Write para arquivos inteiros, Edit para modificacoes pontuais
- Edit falha em matches nao-unicos → fallback Read + Write
- Padrao de entendimento incremental: Grep → Read → seguir imports

**Exercicio pratico:** Cenario de exploracao de codebase usando a cadeia Grep → Read → imports.

**Quiz:** Questoes sobre escolha da ferramenta correta.

---

## TRILHA 3 — Configuracao e Fluxos de Trabalho do Claude Code (20%)

> CLAUDE.md, skills, commands, plan mode e CI/CD.

### Modulo 3.1 — Hierarquia de CLAUDE.md
**Conteudo:**
- 3 niveis: usuario (`~/.claude/CLAUDE.md`), projeto (`.claude/CLAUDE.md`), diretorio
- `@import` para referencias modulares
- `.claude/rules/` para arquivos de regras por topico
- CLAUDE.md e instrucao, nao base de conhecimento — injetado em toda sessao
- Regras especificas por caminho economizam tokens

**Exercicio pratico:** Reestruturar um CLAUDE.md monolitico em 3 camadas com rules/.

**Quiz:** Questoes sobre hierarquia e quando usar cada nivel.

---

### Modulo 3.2 — Commands e Skills Customizados
**Conteudo:**
- Commands de projeto: `.claude/commands/` (versionados, compartilhados)
- Commands pessoais: `~/.claude/commands/`
- Skills: `.claude/skills/` com frontmatter SKILL.md
- `context: fork` para isolamento, `allowed-tools` para restringir acesso, `argument-hint`
- Commands = prompts reutilizaveis. Skills = execucao isolada com ferramentas restritas.

**Exercicio pratico:** Criar um command de equipe e uma skill com context: fork.

**Quiz:** Questoes sobre quando usar command vs skill.

---

### Modulo 3.3 — Regras Condicionais por Caminho
**Conteudo:**
- Arquivos `.claude/rules/` com frontmatter YAML `paths`
- Regras carregam apenas ao editar arquivos correspondentes
- Glob patterns > CLAUDE.md por diretorio para convencoes cross-diretorio
- Exemplo: `paths: ["terraform/**/*"]`, `**/*.test.tsx`

**Exercicio pratico:** Configurar regras por caminho para testes, API e infraestrutura.

**Quiz:** Questoes sobre path-scoping e glob patterns.

---

### Modulo 3.4 — Plan Mode vs Execucao Direta
**Conteudo:**
- Plan mode: tarefas complexas, mudancas em larga escala, multiplas abordagens, decisoes de arquitetura
- Execucao direta: mudancas simples e bem definidas
- Subagente Explore para descoberta verbosa sem poluir contexto principal
- Plan mode para migracoes de 45+ arquivos. Execucao direta para correcoes em 1 arquivo.

**Exercicio pratico:** Cenarios onde o aluno decide entre plan mode e execucao direta.

**Quiz:** Questoes sobre escolha do modo correto.

---

### Modulo 3.5 — Tecnicas de Refinamento Iterativo
**Conteudo:**
- Exemplos concretos de input/output > descricoes em prosa
- Iteracao test-driven: escrever testes primeiro, iterar compartilhando falhas
- Padrao entrevista: Claude faz perguntas antes de implementar
- 2-3 exemplos concretos para clarificar requisitos

**Exercicio pratico:** Usar padrao entrevista + TDD para implementar feature.

**Quiz:** Questoes sobre tecnicas de refinamento.

---

### Modulo 3.6 — Claude Code em CI/CD
**Conteudo:**
- Flag `-p`: modo nao-interativo (sem prompts, sem confirmacoes)
- `--output-format json` + `--json-schema` para saida estruturada
- CLAUDE.md fornece contexto ao Claude Code invocado no CI
- **Regra critica:** sessao SEPARADA para revisao — auto-revisao e enviesada
- Olhos frescos (mesmo de IA) encontram mais coisas

**Exercicio pratico:** Configurar pipeline CI que gera e revisa codigo com sessoes separadas.

**Quiz:** Cenarios de CI/CD e armadilhas de auto-revisao.

---

## TRILHA 4 — Engenharia de Prompt e Saida Estruturada (20%)

> Tornando a saida do Claude confiavel e consistente.

### Modulo 4.1 — Prompts com Criterios Explicitos
**Conteudo:**
- Criterios explicitos > instrucoes vagas ("flaggear quando comportamento contradiz codigo" vs "checar comentarios")
- Instrucoes genericas "seja conservador" falham vs criterios categoricos especificos
- Falsos positivos em uma categoria minam confianca em todas
- Desabilitar temporariamente categorias com alto falso-positivo

**Exercicio pratico:** Reescrever prompt de code review vago para criterios especificos.

**Quiz:** Cenarios de falsos positivos e como reduzir.

---

### Modulo 4.2 — Few-Shot Prompting
**Conteudo:**
- Few-shot = tecnica mais eficaz para consistencia quando instrucoes detalhadas falham
- Demonstra tratamento de casos ambiguos, generalizacao de julgamento
- Reduz alucinacao em tarefas de extracao
- 2-3 exemplos vencem um paragrafo de instrucoes detalhadas — SEMPRE
- Exemplos ensinam julgamento, nao apenas formato

**Exercicio pratico:** Adicionar exemplos few-shot a prompt de extracao inconsistente.

**Quiz:** Questoes sobre quando e como usar few-shot.

---

### Modulo 4.3 — Saida Estruturada com tool_use e JSON Schema
**Conteudo:**
- `tool_use` + JSON schema = metodo mais confiavel para saida estruturada
- `tool_choice`: "auto" (pode retornar texto), "any" (deve chamar ferramenta), forced (ferramenta especifica)
- Schemas eliminam erros de SINTAXE, mas NAO erros SEMANTICOS
- Campos nullable quando dados podem estar ausentes — evita fabricacao de valores
- Ainda precisa de loops de validacao para checar corretude do conteudo

**Exercicio pratico:** Definir ferramenta de extracao com schema JSON e campos nullable.

**Quiz:** Questoes sobre tool_choice e limitacoes de schemas.

---

### Modulo 4.4 — Validacao, Retry e Feedback Loops
**Conteudo:**
- Padrao: extrair → validar → retry com erro ESPECIFICO (nao "tente de novo")
- Bom feedback: "a receita saiu como 0, mas o documento declara 4.2M na pagina 3"
- Retries sao ineficazes quando a informacao nao esta na fonte
- Claude inventa coisas se voce forcar retries alem do ponto onde a informacao existe
- Campo `detected_pattern` para analise sistematica de falsos positivos

**Exercicio pratico:** Implementar loop de validacao com feedback especifico e criterio de parada.

**Quiz:** Cenarios sobre quando parar de fazer retry.

---

### Modulo 4.5 — Estrategias de Processamento em Lote
**Conteudo:**
- Message Batches API: 50% de economia, janela de ate 24h, sem SLA de latencia
- Batch = apenas workloads nao-bloqueantes (nao para checks pre-merge)
- Batch API nao suporta tool calling multi-turn
- `custom_id` para correlacionar pares request/response
- Reenviar apenas documentos que falharam

**Exercicio pratico:** Projetar pipeline batch para processamento de documentos.

**Quiz:** Questoes sobre quando usar batch vs sincrono.

---

### Modulo 4.6 — Arquiteturas de Revisao Multi-Instancia
**Conteudo:**
- Auto-revisao: modelo retem contexto de raciocinio, menos provavel de questionar decisoes
- Instancias de revisao independentes > auto-revisao ou extended thinking
- Multi-pass: analise local por-arquivo + passes de integracao cross-file
- Passes de verificacao com scores de confianca

**Exercicio pratico:** Projetar pipeline de revisao com instancia separada.

**Quiz:** Questoes sobre limitacoes de auto-revisao.

---

## TRILHA 5 — Gerenciamento de Contexto e Confiabilidade (15%)

> Mantendo Claude afiado em conversas longas e garantindo confiabilidade.

### Modulo 5.1 — Gerenciamento de Contexto de Conversacao
**Conteudo:**
- Efeito "lost in the middle": modelo processa inicio e fim bem, pode perder o meio
- Resultados de ferramentas acumulam tokens desproporcionalmente a relevancia
- Correcao 1: fixar fatos-chave no TOPO em bloco de resumo ("case facts")
- Correcao 2: cortar saidas de ferramentas aos campos relevantes
- Correcao 3: delegar exploracao verbosa a subagentes
- Nova sessao com resumo > conversa velha e inchada

**Exercicio pratico:** Otimizar conversa longa aplicando as 3 correcoes.

**Quiz:** Cenarios sobre degradacao de contexto e como mitigar.

---

### Modulo 5.2 — Padroes de Escalonamento e Resolucao de Ambiguidade
**Conteudo:**
- Gatilhos: cliente pede humano, lacuna de politica, incapacidade de progredir
- Escalonamento por sentimento e scores de confianca sao proxies pouco confiaveis
- Multiplos matches de cliente → pedir identificadores adicionais (nao selecao heuristica)
- **Se pedirem humano, entregue humano. Sem excecoes.**

**Exercicio pratico:** Projetar arvore de decisao de escalonamento para suporte.

**Quiz:** Cenarios sobre quando escalar vs resolver.

---

### Modulo 5.3 — Propagacao de Erros em Sistemas Multi-Agente
**Conteudo:**
- Erros genericos cegam o coordenador. Erros estruturados permitem recuperacao.
- Estrutura: tipo de falha, query tentada, resultados parciais, alternativas
- Distinguir falhas de acesso (timeout) de resultados vazios validos (sem matches)
- Anti-padroes: supressao silenciosa e terminacao total
- Recuperacao local em subagentes, anotacoes de cobertura

**Exercicio pratico:** Converter erros genericos em erros estruturados com alternativas.

**Quiz:** Cenarios sobre tratamento de erro e recuperacao.

---

### Modulo 5.4 — Contexto em Exploracao de Codebases Grandes
**Conteudo:**
- Sinais de degradacao: respostas inconsistentes, referencia a "padroes tipicos" em vez de especificos
- Arquivos scratchpad para persistir descobertas entre limites de contexto
- Delegacao a subagentes para exploracao verbosa
- Persistencia de estado estruturada para recuperacao de crash (manifests)
- `/compact` para reducao de contexto

**Exercicio pratico:** Cenario de exploracao de codebase grande com subagentes e scratchpad.

**Quiz:** Questoes sobre quando usar subagentes vs contexto principal.

---

### Modulo 5.5 — Workflows de Revisao Humana e Calibracao de Confianca
**Conteudo:**
- Acuracia agregada (97%) pode mascarar performance ruim em tipos/campos especificos
- Amostragem aleatoria estratificada para medir taxa de erro
- Scores de confianca por campo calibrados com conjuntos de validacao rotulados
- Rota de baixa confianca para revisao humana

**Exercicio pratico:** Projetar pipeline com amostragem estratificada e roteamento por confianca.

**Quiz:** Questoes sobre metricas mascaradas e calibracao.

---

### Modulo 5.6 — Proveniencia de Informacao e Incerteza em Sintese Multi-Fonte
**Conteudo:**
- Atribuicao de fonte se perde durante sumarizacao sem mapeamentos claim-source
- Mapeamentos claim-source estruturados sao obrigatorios para sintese
- Estatisticas conflitantes: anotar conflitos com atribuicao de fonte (nao escolher arbitrariamente)
- Dados temporais: incluir datas de publicacao/coleta para evitar falsas contradicoes

**Exercicio pratico:** Sintetizar relatorio de 3 fontes com mapeamentos claim-source.

**Quiz:** Questoes sobre conflitos de dados e proveniencia.

---

## Modulo Bonus — Simulacao de Exame

### Simulado Completo
- 4 cenarios aleatorios (dos 6 oficiais) com 3 questoes cada = 12 questoes
- Sem feedback durante o exame
- Relatorio final: nota por dominio, areas prioritarias de revisao
- Criterio: 720/1000 = aprovado

### Os 6 Cenarios do Exame
1. **Agente de Suporte ao Cliente** — Agent SDK, ferramentas MCP, 80%+ resolucao no primeiro contato
2. **Geracao de Codigo com Claude Code** — Slash commands, CLAUDE.md, plan mode vs execucao direta
3. **Sistema Multi-Agente de Pesquisa** — Padrao coordenador-subagente, busca + analise + sintese
4. **Produtividade do Desenvolvedor com Claude** — Agent SDK, ferramentas built-in, servidores MCP
5. **Claude Code para CI/CD** — Code review automatizado, geracao de testes, feedback em PRs
6. **Extracao de Dados Estruturados** — JSON schemas, documentos nao-estruturados, integracao downstream

---

## Folha de Referencia — As 5 Regras

> Pendure isso. Esses padroes aparecem em todos os 5 dominios.

1. **HOOKS > PROMPTS** — Quando compliance precisa ser 100%, use imposicao programatica.
2. **ERROS ESTRUTURADOS SEMPRE** — Nunca retorne "falhou" generico. Inclua o que quebrou, tentou, e alternativas.
3. **DEFINA O ESCOPO DAS FERRAMENTAS** — 4-5 ferramentas por agente, nao 18.
4. **REVISAO INDEPENDENTE** — Segunda instancia percebe o que a primeira nao percebe. Auto-revisao e enviesada.
5. **EXEMPLOS > INSTRUCOES** — 2-3 exemplos few-shot vencem um paragrafo de instrucoes.

---

## Recursos Complementares

### Cursos Gratuitos da Anthropic Academy (anthropic.skilljar.com)
| Curso | Relevancia para CCA |
|-------|-------------------|
| Building with the Claude API | Alta — Dominio 1, 2, 4 |
| Introduction to Model Context Protocol | Alta — Dominio 2 |
| MCP: Advanced Topics | Alta — Dominio 2 |
| Claude Code in Action | Alta — Dominio 3 |
| Introduction to Agent Skills | Media — Dominio 1, 3 |
| Introduction to Subagents | Alta — Dominio 1 |
| Claude 101 | Baixa — Fundamentos |

### Cronograma Sugerido de Estudo

| Semana | Foco |
|--------|------|
| 1 | Trilha 1 — Arquitetura Agentica (modulos 1.1–1.4) + curso "Building with Claude API" |
| 2 | Trilha 1 (modulos 1.5–1.7) + Trilha 2 — Design de Ferramentas + cursos MCP |
| 3 | Trilha 3 — Claude Code Config + Trilha 4 — Prompt Engineering + curso "Claude Code in Action" |
| 4 | Trilha 5 — Contexto e Confiabilidade + Simulado Completo + revisao de pontos fracos |

---

## Proximos Passos

Para criar as paginas HTML do curso no formato padrao, precisamos:

1. **Criar a pagina index** com as 5 trilhas e seus pesos visuais
2. **Criar 30 paginas de modulo** (uma por task statement) com:
   - Conteudo teorico do guia oficial
   - Diagrama visual (ASCII ou SVG)
   - Caixa de conclusao com a ideia principal
   - Dica de prova (quando aplicavel)
   - Quiz interativo de 3-5 questoes
   - Exercicio pratico
3. **Criar pagina de simulado** com o motor de quiz ponderado por dominio
4. **Criar pagina de folha de referencia** com as 5 regras

Total estimado: **~35 paginas HTML**
