# Apostila — Desenvolvimento com IA na Supridata

Esta apostila é sua. Ela não repete os slides: serve para você **consultar depois**, quando estiver
sozinho na frente do repositório e a dúvida for "como é que era mesmo o jeito certo de fazer isso?".

Você já usa o Claude Code. O que falta não é a ferramenta — é o método que faz a ferramenta acertar
num projeto grande, com legado ao lado e banco compartilhado. É isso que está aqui.

---

## 1. A ideia central

> **O gargalo não é o modelo. É o contexto que você dá a ele.**

O agente não "sabe" o projeto. Em cada passo ele lê o que cabe na janela de contexto, decide, usa uma
ferramenta, lê o resultado e decide de novo. Três consequências práticas:

1. **Contexto é orçamento.** Todo arquivo lido, todo log de build, toda resposta ocupa espaço.
2. **Erro no começo se propaga.** Por isso se investe em spec e memória, não em corrigir depois.
3. **Só é confiável o que você verifica.** Sem teste, build ou comparação com o legado, você não
   sabe se funcionou — você acha.

## 2. Os quatro artefatos

| Artefato | Onde fica | A pergunta que ele responde |
|---|---|---|
| **Memória** | `CLAUDE.md` · `AGENTS.md` | Como se trabalha *neste* repositório? |
| **Roadmap** | `ROADMAP.md` | Onde estamos e o que falta? |
| **Spec** | `.spec/NNN-tema.spec.md` | O que exatamente é "pronto" aqui? |
| **Skill** | `.claude/skills/` · `.agents/skills/` | Como se faz *esta tarefa* do jeito da casa? |

Memória é quem você é. Roadmap é onde você está. Spec é para onde vai. Skill é como se faz.

---

## 3. Memória — `CLAUDE.md` e `AGENTS.md`

Este arquivo entra em **toda** sessão. Então ele é magro e aponta para o resto.

### As seções que funcionam

- **Contexto geral** — o que é o projeto e qual é a fonte da verdade das regras (e onde ela está no
  disco).
- **Obrigatório antes de implementar** — ponteiro para os templates de referência. A instrução não
  contém o template; ela manda ler o arquivo.
- **Estrutura** — pastas, portas, package raiz.
- **Decisões já tomadas** — a decisão, o porquê e a armadilha concreta.
- **Funcionalidades prontas** — tabela de API, front e permissão.
- **Arquivos-chave** — onde mexer para cada tipo de mudança.
- **Regras que nunca podem ser quebradas** — com a razão de cada uma.

### Como escrever cada regra

| Em vez de | Escreva |
|---|---|
| "cuidado com o banco" | "coluna nova sempre `NULL` ou com `DEFAULT` — o legado insere sem conhecê-la" |
| "siga o padrão" | "leia `references/api-templates.md` antes de criar arquivo; siga exatamente" |
| "atenção às datas" | "`@Temporal(DATE)` retorna `java.sql.Date`, cujo `toInstant()` estoura; converta com o `instanceof`" |

Imperativo, verificável, com a razão do lado. Sem o porquê, a regra é negociada — por você e pelo
agente.

### O que **não** vai na memória

- Conteúdo grande (template de código, catálogo de tabelas). Isso vira arquivo apontado, ou
  `references/` de uma skill.
- Comportamento automático ("sempre rodar o lint", "barrar commit sem teste"). Isso é **hook** no
  `settings.json`: quem executa é a máquina, não o modelo.
- Coisa que muda toda semana. Se muda toda semana, é roadmap.

> Dois exemplos opostos e ambos certos: `sysenota/CLAUDE.md` tem 174 linhas, porque o projeto
> compartilha banco com dois outros sistemas e o contexto *é* o trabalho.
> `sysepesquisa/CLAUDE.md` tem 5 linhas, porque só precisa dizer: leia o `AGENTS.md`, as skills
> canônicas ficam em `.agents/skills/`, e em caso de divergência prevalece aquela pasta.

---

## 4. Roadmap

Não é quadro de tarefas. É o estado do projeto numa linguagem que você e o agente leem igual.

Uma linha de roadmap tem quatro coisas:

```
| P03 | Remessa de DAM (CNAB 240/400) | [x] | 19/10: arquivo igual ao do legado byte a byte,
                                            menos a hora do cabeçalho (E2E remessa.legado.spec.ts) |
```

| Parte | Por quê |
|---|---|
| **ID estável** (`P03`, `F02`, `D05`) | "Implemente o D06" não tem ambiguidade; "faz o PIX do BB" tem |
| **Status** `[x]` `[-]` `[ ]` `[!]` | Quatro estados, inclusive risco aberto — não três |
| **Data** | Sem data, ninguém sabe se a informação ainda vale |
| **Evidência** | O teste que rodou, o arquivo que saiu igual, a comparação feita |

**A regra:** mudou de `[ ]` para `[x]`? Cite a prova. Status sem evidência é opinião.

E divergência do legado (ou do Delphi, ou do que for a referência) só de propósito — e registrada,
com motivo, na tabela de decisões.

---

## 5. Spec

### A numeração é a narrativa

`.spec/000-baseline-inventario`, `001-modernizacao-api`, `002-modelo-usuario-permissoes`,
`003-auth-jwt`, `004-login-web`... A ordem conta a história do projeto e é ordem de dependência.
Quem chega depois lê na sequência. **Nunca se renumera.**

### A §0 é a parte que importa

Antes de qualquer código, a spec diz o que é "pronto", em condições que **um terceiro consegue
conferir sem perguntar nada a você**. Exemplo real:

```
Pronto quando:
1. cada regra das tabelas §2 e §3 estiver conferida contra o fonte do legado
   (status ✅ ou divergência registrada no ROADMAP.md);
2. a suíte Playwright da §6 passar inteira na pilha local;
3. as 3 massas de teste da CAIXA forem geradas no sandbox, com barra e QR coerentes.
```

Legenda de status por regra: ⬜ a conferir · ✅ igual à referência · ⚠️ divergência · ❌ falta.

### O documento de retomada

Um por projeto, para quem chega sem o histórico da conversa — outra pessoa, outra IA, ou você mesmo
na segunda-feira. O que ele carrega:

- **hierarquia das fontes da verdade** quando elas discordam (no Syscomm: o Delphi é o árbitro
  porque é o que roda em produção; o Angular 8 é inventário de campos, não de regras);
- **armadilhas do ambiente**, escritas: o script de deploy que ecoa sucesso sem publicar, o
  timestamp do container em UTC contra o do Windows em BRT;
- **comandos prontos**: subir a pilha, publicar, pegar token, rodar o E2E.

> Toda dor que te custou meia tarde vira três linhas aqui — e nunca mais custa.

### A sequência de trabalho

```
spec (o que é pronto) → roadmap (onde encaixa) → implementação → evidência de volta na spec/roadmap
```

E duas regras que vêm do `AGENTS.md` do SysEPesquisa:

- atualize plano, especificação e roadmap do mesmo tema **antes** do código, e registre apenas os
  testes **realmente executados**;
- **não implemente código quando o pedido for somente análise, plano, spec ou roadmap** — e quando
  for você pedindo, diga qual dos dois é.

---

## 6. Skill

Uma skill é um padrão de projeto executável: o processo escrito + o código-padrão + os scripts.

### Anatomia

```
sysetemplate-migrar-jsf/
├── SKILL.md                     143 linhas  ← o processo (curto)
├── references/
│   ├── api-templates.md         348 linhas  ← código-padrão Spring
│   └── web-templates.md        1554 linhas  ← código-padrão Angular
└── scripts/                                 ← o que é determinístico
```

**Divulgação progressiva:** o `SKILL.md` é curto e os `references/` são grandes, lidos só quando a
tarefa pede. Se aquelas 1554 linhas estivessem no `CLAUDE.md`, você pagaria por elas em toda sessão
— inclusive para corrigir um typo.

### A `description` é o gatilho

Ela não descreve a skill para humano: **lista as palavras que você vai digitar**, mais a condição
objetiva que caracteriza a tarefa.

```yaml
description: >
  Use este skill para <tarefa>. Ative sempre que o usuário mencionar:
  "<frase que o dev digita>", "<outra>", "<outra>", ou qualquer pedido de <forma genérica>.
  Mesmo que o usuário não cite <padrão> explicitamente — se <condição objetiva>, use este skill.
```

Descrição vaga = skill que nunca dispara = você concluindo que "não funciona" e voltando a copiar
código à mão. **Teste de aceitação: digite uma frase do gatilho numa sessão e veja se ela dispara.**

### Script em vez de prompt

Trabalho mecânico e verificável — trocar 200 cores `#hex` por variáveis, gerar capturas de tela — é
`scripts/`, não instrução. O script faz certo, barato e igual toda vez. O modelo decide **quando**
rodar; o script garante **como**.

### Onde ela mora

| Local | Escopo |
|---|---|
| `~/.claude/skills/` | todos os seus projetos, na sua máquina |
| `<projeto>/.claude/skills/` | só aquele repositório, versionado com ele |
| `<projeto>/.agents/skills/` | canônico quando há mais de uma ferramenta de IA no repo |

Skills são **cumulativas**: uma feature que mexe em API e front usa a de evolução segura mais a de
backend mais a de frontend.

> **A regra do "três vezes":** se você já fez isso três vezes à mão, deveria existir uma skill.

---

## 7. Contexto e tokens

### Por que a sessão piora depois de uma hora

A janela enche → o histórico é resumido para caber → o resumo perde detalhe → o agente esquece a
decisão que vocês tomaram há 40 minutos.

**Sessão longa é sintoma, não troféu.** O que preserva conhecimento entre sessões não é o histórico:
é o arquivo. Roadmap, spec, memória.

### Os oito hábitos

1. **Uma tarefa, uma sessão.** Terminou? Sessão nova — o conhecimento ficou no roadmap.
2. **Aponte o arquivo, não a pasta.** `EscrituracaoResource.java`, não "o backend".
3. **Magro com ponteiros.** Memória e skill curtas; anexo grande só quando a tarefa pede.
4. **Script no que é mecânico.**
5. **Plano antes do código.** Revisar 20 linhas de plano custa centavos; revisar 600 linhas de
   código errado custa a tarde.
6. **Subagente para busca ampla.** Que volte com a conclusão, não com os arquivos.
7. **Saída de comando enxuta.** `mvn -q`, `--reporter=line`, `head`/`grep` no log. Log de build
   inteiro no contexto é dinheiro queimado sem informação nova.
8. **Não cole o que já está em disco.** Dê o caminho.

Nenhum deles é sobre "escrever prompt melhor". Todos são sobre o que entra na janela.

### Qual modelo

| Situação | Modelo |
|---|---|
| Arquitetura, regra crítica do legado, depuração difícil, revisão | **Opus 5.5** |
| Implementação rotineira dentro de uma spec já escrita | **Sonnet 5.5** |
| Mecânico em volume, classificação, busca, renomeação | **Haiku 5.5** |

O **esforço** (effort) regula quanta deliberação o modelo gasta. O erro comum não é gastar demais: é
gastar de menos onde a decisão é caríssima — desenho de arquitetura, paridade com o legado — e demais
onde não muda nada.

---

## 8. Se você for colocar IA dentro do produto

### RAG não é jogar documento no vetor

É decidir **o que o modelo pode ver** e **o que ele pode fazer com isso**. Três padrões:

| Padrão | Quando | Exemplo na casa |
|---|---|---|
| Catálogo + consulta validada | dado estruturado, pergunta analítica | catálogo semântico do SysEPesquisa |
| Busca semântica (embeddings) | texto livre, volume, pergunta vaga | respostas abertas, documentos de protocolo |
| Contexto curado à mão | poucos documentos, alto valor | os `.spec/` e o `CLAUDE.md` — RAG de desenvolvimento |

No catálogo semântico, a IA **propõe** uma consulta e o código **decide** se ela roda: allowlist de
tabelas e colunas (com sinônimos), allowlist de joins (outras combinações são rejeitadas mesmo entre
tabelas permitidas), allowlist de funções, fora-de-escopo explícito e executor read-only com limites.

### O harness obrigatório

Para qualquer feature de IA, cinco camadas, não negociáveis:

1. sanitização da entrada contra prompt injection;
2. validação rígida da saída — o modelo propõe, o código aceita ou rejeita;
3. cota e plano verificados **antes** de gastar token (o mais barato é não chamar);
4. auditoria por tenant, em migration versionada;
5. mascaramento de PII onde a resposta é aberta.

### Custo

Três alavancas, nesta ordem: **cache do prefixo estável** (e lembre: qualquer byte que mude no
prefixo invalida tudo depois — um timestamp no system prompt mata o cache), **modelo por rota**,
**lote** para o que não é interativo. Julgue custo **por tarefa concluída**, não por chamada: uma
chamada barata que precisa de três tentativas não é barata.

---

## 9. Qualidade

```
compila  <  teste unitário  <  E2E na pilha local  <  comparação com o legado, comprovada
```

No SysENota o padrão é E2E Playwright contra o legado, com o nome do arquivo dizendo o que ele prova:
`remessa.legado.spec.ts`, `livro-fiscal.legado.spec.ts`, `pix-caixa.legado.spec.ts`. E o rigor é
literal: arquivo igual byte a byte (menos a hora do cabeçalho), guia igual pixel a pixel.

Dois combinados:

- **O agente não declara pronto; o teste declara.** O que ele diz sobre o próprio trabalho é
  hipótese até alguém rodar.
- **O git é seu.** Commit e push são do time. Revise o diff antes de aceitar.

E status honesto no documento: "implementado e compilando, ainda sem teste ponta a ponta" é uma frase
melhor que um ✅ generoso.

---

## 10. Exercícios

Faça na sua máquina, no repositório de verdade. Deixe escrito aqui o que você produziu.

### 10.1 Memória (10 min)
Escolha um projeto sem `CLAUDE.md`. Escreva **só** três seções: Estrutura, Decisões já tomadas,
Regras que nunca podem ser quebradas. Use `templates/CLAUDE.md.modelo`.

```
Projeto: ______________________________
A regra mais importante que escrevi: ______________________________________________
Por que ela existe: _______________________________________________________________
```

### 10.2 Roadmap (8 min)
Pegue 5 itens de um board e reescreva como linha de roadmap, com ID, status, data e evidência.

```
| ____ | _________________________ | [ ] | ______________________________________ |
```

### 10.3 Critério de pronto (15 min)
Escreva a §0 de uma feature real do backlog. Três condições verificáveis por um terceiro.

```
Pronto quando:
1. ________________________________________________________________________________
2. ________________________________________________________________________________
3. ________________________________________________________________________________
```

Agora o teste: **um colega consegue conferir essas três coisas sem perguntar nada a você?**
Se a resposta for "não", a condição ainda é desejo, não critério.

### 10.4 Skill (20 min)
Escolha algo que você já repete à mão. Escreva a skill com `templates/SKILL.md.modelo`:
frontmatter com gatilhos, passos numerados, **um** arquivo em `references/` com o código-padrão.

```
Nome da skill: ____________________________________
Três frases de gatilho: ___________________________________________________________
Disparou no teste?  [ ] sim   [ ] não — ajustei a description e: ___________________
```

### 10.5 Laboratório final (35 min)
1. Escolha um item `[ ]` de um `ROADMAP.md` real.
2. Escreva a spec mínima: §0 e o mapa referência → nova versão.
3. Implemente com a skill apropriada.
4. Rode o teste.
5. Atualize o roadmap com status, data e evidência.

Critério de aprovação: **outra pessoa consegue retomar isso amanhã lendo só os arquivos?**

---

## 11. Cola de uma página

```
OS QUATRO ARTEFATOS
  CLAUDE.md / AGENTS.md   como se trabalha neste repo (magro, com ponteiros)
  ROADMAP.md              onde estamos — ID, status, data, EVIDÊNCIA
  .spec/NNN-*.spec.md     o que é "pronto" — verificável por um terceiro
  .claude/skills/         como se faz esta tarefa — gatilho + passos + references/

ANTES DE PEDIR CÓDIGO
  [ ] a spec diz o que é "pronto"?
  [ ] a regra da referência foi conferida na fonte?
  [ ] a skill certa existe? (fiz isso 3x à mão? então deveria existir)
  [ ] o pedido é análise ou implementação? diga qual.

ECONOMIA
  uma tarefa, uma sessão          plano antes de código
  aponte o arquivo, não a pasta   script para o mecânico
  saída de comando enxuta         subagente traz conclusão, não arquivos

MODELO
  Opus 5.5    arquitetura, regra crítica, depuração difícil, revisão
  Sonnet 5.5  implementação dentro de spec pronta
  Haiku 5.5   mecânico, volume, busca

PRONTO É
  compila < unitário < E2E na pilha local < igual à referência, comprovado
  o agente não declara pronto; o teste declara.
  status sem evidência é opinião.
```

---

## 12. Depois do treinamento

Três compromissos para a semana seguinte — escreva os seus:

```
1. O CLAUDE.md de __________________ é meu: vou escrever/revisar até ____/____.
2. A skill que vou criar primeiro: ________________________________________________
3. O que paro de fazer: ___________________________________________________________
```

Revisão do time em duas semanas, sobre as skills que foram escritas de verdade.
