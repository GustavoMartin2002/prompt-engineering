# Guia - Engenharia de Prompt

<strong>Documentação de Boas Práticas com GenAI e LLMs</strong>

Este guia é um **Manual de Consulta Permanente** desenhado para ser utilizado no dia a dia do desenvolvimento de software. Use-o para extrair o máximo de precisão, segurança e qualidade de arquitetura das ferramentas de Inteligência Artificial.

## Consulta Rápida ⚡

| Situação / Problema no Dia a Dia | Técnica / Prompt Recomendado | Ferramenta Ideal |
| :--- | :--- | :--- |
| **Iniciar um projeto ou microsserviço do zero** | Fluxo *Meta-Prompting* (Etapas 1, 2b e 3) | Modelo *Thinking* (Planejamento) + Claude/Cursor (Código) |
| **Escolher entre 2 ou mais arquiteturas/stacks** | Pedir múltiplas opções + Combate à Bajulação | Gemini Pro / GPT Thinking / Claude Opus |
| **Bug complexo que a IA não resolve** | *Code Debugger* + Regra das 3 Chances + `git reset` | Cursor / Claude Code |
| **Revisar PR antes de subir para produção** | *The Code Review* (P0/P1/P2) + Técnica de *Alloying* | Claude (Gera) ➔ Gemini (Revisa) ➔ ChatGPT (Refina) |
| **A IA começou a alucinar ou esquecer regras** | Prevenção de *Context Bloat* (Nova sessão + `@arquivo`) | Cursor / Claude Code (`CLAUDE.md`) |
| **Preciso usar uma lib recém-lançada / atualizada** | Injeção de documentação via *Context7 MCP* | Cursor / Claude Code |
| **Classificar ou extrair dados de logs/JSON/código**| Uso de Delimitadores XML (`<example>`, `<output>`) + *Few-Shot* | Qualquer LLM (API ou Chat) |
| **Defender refatoração ou débito técnico para gestão**| *The Persuasion Artist* + *The $Million Consultant* (MECE)| ChatGPT / Claude / Gemini |

## 📑 Sumário de Navegação

1. [Resumo](#-1-resumo)
2. [AI Tools e Agents para Engenharia de Software](#%EF%B8%8F-2-ai-tools-e-agents-para-engenharia-de-software)
3. [Os 9 Ingredientes de um Bom Prompt Técnico](#-3-os-9-ingredientes-de-um-bom-prompt-técnico)
4. [Tarefas Técnicas e de Codificação (Prompts Copiáveis)](#-4-técnicas-de-codificação-prompts-copiáveis)
5. [Os 10 Mandamentos do Vibe Coding](#-5-os-10-mandamentos-do-vibe-coding)
6. [Padronização Permanente de Repositório (`GEMINI.md` / `CLAUDE.md`)](#%EF%B8%8F-6-padronização-permanente-de-repositório-geminimd--claudemd)

## 📌 1. Resumo

A qualidade do código, da arquitetura e das análises geradas por Grandes Modelos de Linguagem (LLMs) é diretamente proporcional à precisão do contexto inicial fornecido (**Garbage In, Garbage Out**). 

Conversar com uma IA sem contexto estruturado é como passar requisitos vagos para um desenvolvedor júnior: o resultado pode até compilar, mas falhará em segurança, escalabilidade e regras de negócio. Este guia traduz os fundamentos de *Prompt Engineering* para o Ciclo de Vida de Desenvolvimento de Software (SDLC).

## 🛠️ 2. AI Tools e Agents para Engenharia de Software

### 2.1. LLMs de Propósito Geral e Raciocínio
* **Anthropic Claude (Opus / Sonnet):** Referência para geração de código limpo, refatoração complexa e execução do prompt final de codificação.
* **Google Gemini (Pro):** Excelente para janelas de contexto extensas, auditoria cruzada de código (*Alloying*) e análise direta de gravações/transcrições de reuniões de alinhamento.
* **OpenAI ChatGPT (Modelos Thinking / 4o):** Forte em raciocínio arquitetural passo a passo, estruturação de planos e modos de voz/ditado para *brainstorming*.
* **X Grok:** Alternativa para consultas rápidas e exploração de tendências.

### 2.2. Ferramentas Especializadas, IDEs e Ecossistema Dev
* **Cursor/Antigravity:** Editor de código (IDE) nativo para IA que permite referenciar arquivos (`@arquivo`) e configurar regras globais.
* **Claude Code:** Agente de linha de comando (CLI) que mantém a memória arquitetural do repositório através de `/init` e `CLAUDE.md`.
* **Antigravity App:** Central de comando para gerenciar vários agentes locais em paralelo, agrupando conversas em projetos, múltiplos espaços de trabalho e automatiza tarefas rotineiras.
* **GitIngest (`gitingest.com`):** Converte repositórios inteiros em texto estruturado amigável para LLMs.
* **Context7 MCP:** Fornece documentação atualizada em tempo real dos frameworks utilizados.
* **Skills (`skills.sh`) :** Habilidades que são reutilizáveis ​​para agentes de IA.
* **Cursor Directory (`cursor.directory`):** Repositório de *system prompts* prontos para cada tech stack.

## 🧪 3. Os 9 Ingredientes de um Bom Prompt Técnico

1. **Peça mais de uma opção (*Explore Trade-offs*):** Nunca aceite a primeira arquitetura sugerida. Peça de 3 a 4 abordagens distintas com tabelas comparativas.
2. **Atribua uma Persona Especialista:** Defina o papel e a senioridade (ex: *"Atue como um Arquiteto Cloud Sênior..."*).
3. **Defina o Estilo da Resposta:** Seja "didático para o PM" ou "direto e sem elogios para Code Review".
4. **Especifique o Formato Exato de Saída:** Peça tabelas Markdown, diagramas Mermaid ou objetos JSON com chaves estritas.
5. **Encadeamento de Prompts:** Etapa 1 (Modelagem) ➔ Etapa 2 (Contrato da API) ➔ Etapa 3 (Implementação).
6. **Dê Exemplos Práticos:** Mostre 2 a 3 exemplos de entrada e saída (ex: padrão de testes unitários do seu time).
7. **Seja Específico e Claro:** Evite *"Como melhorar a performance?"* e use *"Compare B-Tree vs. GIN no PostgreSQL para JSONB..."*.
8. **Forneça Alto Contexto:** Especifique Stack, público-alvo, volume de RPS, restrições e objetivos antes de pedir o código.
9. **Use Delimitadores Estruturados:** Separe as instruções do código usando crases triplas (\`\`\`), aspas triplas (`"""`) ou tags XML (`<codigo>`).

## 💻 4. Técnicas de Codificação (Prompts Copiáveis)

### 4.1. Gerador de Funções Isoladas
```text
Escreva uma função em [LINGUAGEM] que receba [ENTRADAS E TIPOS] e [PROPÓSITO EXATO].
Requisitos:
- Tipagem estrita e Clean Code.
- Trate entradas nulas/inválidas adequadamente.
- Adicione comentários explicando a lógica passo a passo e informe a complexidade Big-O.
```

### 4.2. O Depurador de Código (*Code Debugger*)
```text
Estou recebendo um erro no meu código em [LINGUAGEM].
Stack Trace:
<erro> [COLE O ERRO AQUI] </erro>

Código atual:
<codigo> [COLE O CÓDIGO AQUI] </codigo>

1. Explique a causa-raiz exata.
2. Forneça o código corrigido.
3. Explique o que foi alterado para evitar regressão.
```

### 4.3. Explicador de Código Legado
```text
Atue como especialista em código legado. Analise o código abaixo e:
1. Explique passo a passo o que ele faz.
2. Destaque o propósito das principais variáveis e queries.
3. Aponte possíveis armadilhas (pitfalls), edge cases não tratados ou riscos de segurança.

<codigo> [COLE O CÓDIGO AQUI] </codigo>
```

### 4.4. Code Review Sênior
```text
Simule um Code Review rigoroso feito por um Principal Engineer.
Foque em: corretude, performance, segurança (OWASP) e testabilidade. Restrição: mantenha a API atual estável.

Entregáveis:
1) Resumo (sólido vs risco).
2) Problemas concretos citando linhas exatas.
3) Correções acionáveis (diff).
4) Rótulos [P0/P1/P2] e Impacto [Baixo/Médio/Alto].
5) Plano de refatoração de 10-15 linhas.
6) 3-5 sugestões de testes unitários.
Não me elogie e evite "bikeshedding".

<codigo> [COLE SEU PR / DIFF AQUI] </codigo>
```

### 4.5. O Fluxo de Meta-Prompting (Planejar antes de codar)
*Siga os passos abaixo na mesma janela de chat antes de pedir código:*

**Passo 1: Planejamento (The Technical Planner)**
```text
Quero construir um projeto: [DESCRIÇÃO BREVE].
Features principais: [LISTA DE FEATURES].
Atue como Arquiteto: quebre em Épicos, defina o MVP, esboce a arquitetura, faça-me perguntas de esclarecimento e valide suposições. NÃO ESCREVA CÓDIGO AINDA.
```

**Passo 2: Definição da Stack**
```text
Ajude-me a fechar a Tech Stack através de uma conversa iterativa. Faça-me até 10 perguntas de alto impacto para fechar arquitetura, banco e escalabilidade. Proponha de 3 a 5 opções. NÃO ESCREVA CÓDIGO AINDA.
```

**Passo 3: Geração do Mega-Prompt**
```text
Levando em consideração as definições de escopo e stack que fechamos acima, gere 2 versões de prompts técnicos altamente detalhados para eu usar em um Agente de IA para gerar o código estruturado. NÃO ESCREVA O CÓDIGO AINDA.
```

## 🎸 5. Os 10 Mandamentos do Vibe Coding

O **Vibe Coding** exige rigor de engenharia de software para não gerar repositórios caóticos:

1. **Planeje o primeiro prompt com precisão:** Use modelos *Thinking* antes de abrir a IDE.
2. **Combata a Bajulação (Sycophancy):** Peça sempre 2 opções de arquitetura ou mande a IA criticar o próprio plano.
3. **Não use Stacks obscuras:** Mantenha-se em tecnologias consolidadas (React, Node, Python, Postgres).
4. **Domine o Git:** Faça commits pequenos e atômicos. Use o Git como sua "máquina do tempo", nunca mande a IA simplesmente "desfazer o que fez".
5. **Decomponha os problemas:** Não peça um sistema inteiro de uma vez; limite o prompt a uma única rota ou componente.
6. **Evite a poluição de contexto (*Context Bloat*):** Feche chats longos, abra sessões novas e referencie arquivos via `@` ao invés de colar código.
7. **Force o raciocínio em voz alta:** Termine prompts complexos com: *"Explique sua abordagem passo a passo antes de escrever código. DO NOT CHANGE ANYTHING I DID NOT ASK FOR."*
8. **Regra das 3 Chances para Bugs:** Se a IA não arrumar um erro em 3 tentativas, dê um `git reset` e tente outra abordagem com um novo prompt.
9. **Defina Restrições Globalmente:** Mantenha um `.cursorrules` ou `CLAUDE.md` atualizado na raiz do projeto.
10. **Use Alloying antes do Deploy:** Faça o Claude codificar, o Gemini revisar a arquitetura/segurança e o ChatGPT refinar testes.

## 🛡️ 6. Padronização Permanente de Repositório (`GEMINI.md` / `CLAUDE.md`)

Copie o conteúdo abaixo e salve como `GEMINI.md` ou `CLAUDE.md` (Claude Code) na raiz de seus repositórios para garantir que a IA siga as regras do seu projeto:

```markdown
# Diretrizes de Engenharia e Contexto do Projeto

## 1. Stack Tecnológica Oficial
- Linguagens: [EX: TypeScript 5+, Node.js]
- Frameworks: [EX: NestJS, Next.js, Tailwind]
- Banco de Dados: [EX: PostgreSQL, Prisma ORM]
- Testes: [EX: Jest]

## 2. Padrões de Arquitetura e Código
- Siga rigorosamente os princípios SOLID, Clean Architecture e DRY.
- Funções devem ser pequenas e com responsabilidade única.
- Tipagem estrita é obrigatória: nunca utilize `any`. Crie interfaces explícitas.
- Tratamento de erros: utilize logs estruturados e retorne erros HTTP semânticos (não engula exceções).

## 3. Regras de Segurança Inegociáveis
- NUNCA escreva chaves de API, senhas ou credenciais hardcoded. Utilize `.env`.
- Valide e sanitize todas as entradas de usuário (ex: `Zod`, `class-validator`).
- Garanta parametrização contra SQL Injection em queries cruas.

## 4. Comportamento Esperado da IA (Regras de Vibe Coding)
1. Antes de codificar, explique seu plano em até 5 tópicos curtos apontando quais arquivos serão tocados.
2. DO NOT CHANGE ANYTHING I DID NOT ASK FOR. JUST DO ONLY WHAT I TOLD YOU.
3. Não remova comentários relevantes nem altere contratos de APIs públicas sem autorização.
4. Se um requisito estiver ambíguo, pergunte antes de codificar.
```

---
*Este material é destinado a estudo, consulta diária e evolução contínua das práticas de engenharia de software apoiadas por Inteligência Artificial.*
