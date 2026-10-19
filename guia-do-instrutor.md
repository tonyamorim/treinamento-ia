# Treinamento — Desenvolvimento com IA (Claude Code) na Supridata

Guia do instrutor. Montado a partir do que você **já usa** nos projetos
`sysenota`, `syscomm`, `sysepesquisa`, `syseprotocolo`, `syseassociacao` e nas skills
`sysetemplate-*`. Nada aqui é teoria genérica: cada módulo tem um artefato real para abrir na tela.

> **Turma: 3 devs** Java/Angular da Supridata que **já usam o Claude Code** no dia a dia. Com três
> pessoas não existe plateia: isto é workshop de teclado — os três com o repositório clonado e
> rodando, e cada bloco terminando com eles executando, não com você falando. Duração ainda em
> aberto: a grade abaixo é modular, com três montagens possíveis.
>
> Como eles já usam a ferramenta, o módulo 0 é curto e a conversa não é "o que é o Claude Code": é
> *por que a sua sessão azeda depois de uma hora e a dele não*. Espere resistência boa nos módulos de
> spec e evidência — é onde o método cobra disciplina de quem já tem velocidade.

---

## 1. A tese do treinamento (diga isso no primeiro slide)

> **O gargalo não é o modelo. É o contexto que você dá a ele.**

A mensagem central, que amarra tudo: você não "pede código para a IA". Você **constrói um ambiente**
(`harness`) em que a IA só consegue acertar — e o barato sai caro quando esse ambiente não existe.

Os quatro pilares, na ordem em que você vai ensinar:

| Pilar | Artefato | Pergunta que ele responde |
|---|---|---|
| **Memória** | `CLAUDE.md` / `AGENTS.md` | Como se trabalha *neste* repositório? |
| **Roadmap** | `ROADMAP.md` | Onde estamos e o que falta? |
| **Spec** | `.spec/NNN-*.spec.md` | O que exatamente é "pronto" para esta feature? |
| **Skill** | `.claude/skills/*/SKILL.md` | Como se faz *esta tarefa* do jeito da casa? |

Frase de efeito que funciona bem com dev sênior:
*"Memória é quem você é. Roadmap é onde você está. Spec é para onde vai. Skill é como se faz."*

---

## 2. Grade modular

Dez blocos. A coluna **peso** diz o que você corta primeiro quando o tempo apertar: `núcleo` não sai
de jeito nenhum; `apoio` sai se precisar; `extra` é bônus para turma animada.

| # | Bloco | Tempo | Peso | Slide inicial |
|---|---|---|---|---|
| 0 | Modelo mental: contexto, laço, verificação | 15 min | núcleo | `tese` |
| 1 | **Memória** — `CLAUDE.md` e `AGENTS.md` | 30 min | núcleo | `memoria` |
| 2 | **Roadmap** — estado compartilhado com evidência | 25 min | núcleo | `roadmap` |
| 3 | **Spec** — numeração, critério de pronto, retomada | 40 min | núcleo | `spec-acervo` |
| 4 | **Skill** — anatomia, gatilho, references, scripts | 45 min | núcleo | `skill-anatomia` |
| 5 | Exercício: cada um escreve uma skill pequena | 30 min | núcleo | — |
| 6 | **Tokens e contexto** — por que a sessão degrada | 35 min | núcleo | `tokens-sessao` |
| 7 | **Modelos** — qual usar para quê, e o custo em produto | 25 min | apoio | `modelos` |
| 8 | **RAG** — o catálogo semântico do SysEPesquisa | 40 min | apoio | `rag-catalogo` |
| 9 | **Qualidade** — E2E como prova, não enfeite | 25 min | núcleo | `qualidade` |
| 10 | Laboratório final + combinados escritos | 35 min | núcleo | `combinados` |

Soma: **5h45** com tudo, **4h20** só com o núcleo. Três montagens que funcionam:

| Montagem | Como fica | Quando escolher |
|---|---|---|
| **Dois encontros de 3h** (recomendado) | 0–5 no primeiro, 6–10 no segundo | Eles voltam na semana seguinte tendo usado o padrão — e você corrige o que não pegou |
| **Um dia (9h–16h)** | Núcleo + 7 e 8, dois intervalos e almoço | Agenda fecha de uma vez; cansa mais e rende menos no laboratório final |
| **Quatro encontros de 90 min** | 0–2 · 3–4 · 5–6 · 7–10 | Melhor absorção, pior logística. Bom se eles estiverem tocando entrega |

Com 3 pessoas, "dupla" nos exercícios vira **trio num teclado** (um digita, dois revisam, trocam a
cada exercício) ou **três sessões em paralelo** com você circulando. O trio funciona melhor nos
módulos 3 e 5, onde a discussão é o conteúdo; o paralelo funciona melhor no laboratório final.

---

## 3. Módulo 0 — Modelo mental (20 min)

Três ideias, nada mais:

1. **Tudo é contexto.** O modelo não "sabe" o projeto: ele lê o que cabe na janela. Todo arquivo
   lido, todo comando rodado, toda resposta ocupa espaço. Contexto é orçamento.
2. **O agente age em loop**: lê → decide → usa ferramenta → lê o resultado → decide de novo. Erro
   em passo 1 se propaga. Por isso investimos no *começo* (spec, memória) e não em corrigir depois.
3. **O modelo é previsível no que você verifica.** Se não existe teste, build ou comparação com o
   legado, você não sabe se funcionou — você *acha*.

Demonstração de abertura (3 min, impacto garantido): abra o `syscomm` e peça algo numa sessão
**sem** `AGENTS.md`; depois a mesma coisa com as skills no lugar. A diferença no resultado é o
treinamento inteiro em miniatura.

---

## 4. Módulo 1 — Memória: `CLAUDE.md` e `AGENTS.md` (30 min)

### O que mostrar

Abra `sysenota/CLAUDE.md` (174 linhas) e percorra as seções — elas são o gabarito:

- **Contexto geral** — o que é o projeto e qual é a fonte da verdade
  ("o legado é a referência de regras de negócio").
- **OBRIGATÓRIO antes de qualquer implementação** — ponteiro para os templates de referência.
  Repare: a instrução não *contém* os templates, ela **manda ler** o arquivo certo.
- **Estrutura do projeto** — pastas, portas, package raiz.
- **Decisões técnicas já tomadas** — com o *porquê* e a armadilha concreta
  (o caso do `java.sql.Date` com `@Temporal(DATE)` que estoura em `toInstant()`).
- **Funcionalidades já migradas** — tabela com API / Angular / permissão.
- **Arquivos-chave para novas funcionalidades** — onde mexer para cada tipo de mudança.
- **Regras que nunca podem ser quebradas** — o banco compartilhado entre legado, nova versão e
  importador nacional; coluna nova sempre `NULL`; nunca encurtar coluna; caixa dos nomes de tabela.

Depois abra `sysepesquisa/CLAUDE.md` — **5 linhas** apontando para o `AGENTS.md`. Esse contraste é a
aula: o `CLAUDE.md` não precisa ser grande, precisa ser **o endereço certo**.

E o `sysepesquisa/AGENTS.md`, com as 7 regras numeradas do domínio. Destaque duas:

> 6. Atualize plano, especificação e roadmap do mesmo tema **antes do código** e registre testes
>    realmente executados.
> 7. **Não implemente código quando o pedido for somente análise, plano, spec ou roadmap.**

A regra 7 é ouro em treinamento: ela existe porque o agente, sem ela, implementa quando você só
queria pensar. Mostre que a instrução nasceu de uma dor real.

### Regras que o time leva para casa

- `CLAUDE.md` tem **dono e revisão**: quando uma decisão muda, o arquivo muda no mesmo commit.
- Escreva **imperativo e verificável**: "coluna nova sempre `NULL` ou com `DEFAULT`", não
  "cuidado com o banco".
- Toda regra carrega a **razão** — sem o porquê, o agente (e o dev novo) negocia a regra.
- `AGENTS.md` como entrada canônica quando há mais de uma ferramenta de IA no repo
  (`.github/copilot-instructions.md` existe no `sysepesquisa`: mesma fonte, ferramentas diferentes).
- O que é **comportamento automático** (rodar lint, barrar commit) não vai em memória: vai em
  **hook** no `settings.json`. Memória é instrução para o modelo; hook é regra da máquina.

### Exercício (10 min)
Cada dupla abre um projeto da Supridata sem `CLAUDE.md` e escreve **só** as seções
"Estrutura", "Decisões já tomadas" e "Regras que nunca podem ser quebradas". Tempo curto de
propósito: obriga a escolher o que importa.

---

## 5. Módulo 2 — Roadmap (30 min)

Abra `sysenota/ROADMAP.md`. Mostre que ele não é um quadro de tarefas, é **o estado do projeto em
linguagem que humano e IA leem igual**:

- **Origem e alvo** em tabela (legado → nova versão: backend, frontend, banco).
- **Legenda explícita**: `[x]` concluído · `[-]` em andamento · `[ ]` pendente · `[!]` bloqueio/risco.
- **IDs estáveis** (`P01`, `F03`, `D05`) — é assim que você conversa com o agente sem ambiguidade:
  *"implemente o D06"* é melhor que *"faz o PIX do BB"*.
- **Coluna "Evidência/nota"** com data e prova: *"19/10: as guias dos dois sistemas saem iguais
  pixel a pixel (E2E `pix-caixa.legado.spec.ts`)"*. Isso é o que impede o roadmap de virar ficção.
- **Divergência registrada**: "divergência só de propósito, e registrada na tabela de decisões".

### O ponto didático
Status sem evidência é opinião. O padrão da casa é: **mudou de `[ ]` para `[x]` → cita a prova**
(teste que rodou, arquivo gerado, comparação feita). É isso que permite outra pessoa — ou outra
sessão de IA — retomar o trabalho sem reabrir tudo.

### Exercício (8 min)
Pegar 5 itens de um board (Jira/Trello/planilha) da Supridata e reescrever como linha de roadmap
com ID, status, data e evidência.

---

## 6. Módulo 3 — Specs (40 min)

O acervo do `syscomm` é a melhor sala de aula que você tem: **24 specs numeradas**, de
`000-baseline-inventario` a `023-usabilidade`.

### O que mostrar

1. **A numeração é a narrativa.** `000` inventário → `001` API → `002` usuário/permissões →
   `003` JWT → `004` login → `005` guards... A ordem conta a história da modernização. Quem chega
   depois lê na sequência.
2. **`sysenota/.spec/001`** — a anatomia de uma spec boa:
   - cabeçalho honesto: *"Documento para implementação, escrito em 2026-10-16. **Ainda não implementado.**"*
   - **§0 Objetivo e critério de pronto** numerado: 3 condições objetivas (regra conferida contra o
     legado, suíte Playwright passando, 3 massas de teste geradas no sandbox).
   - **legenda de status por regra**: ⬜ a conferir · ✅ igual ao legado · ⚠️ divergência · ❌ falta.
   - **§1 Mapa legado → nova versão** em tabela, peça por peça (bean, serviço, telas, relatório).
     É literalmente o mapa que o agente usa para navegar.
3. **`syscomm/.spec/017-continuidade-do-trabalho`** — pare nesse. É o truque mais transferível do
   seu método: um documento de retomada que diz, na primeira linha,
   *"serve para quem chega sem o histórico da conversa — outra pessoa, outra IA, ou a mesma sessão
   depois de perder o contexto"*. Ele contém:
   - **hierarquia de fontes da verdade** quando elas discordam: Delphi é o árbitro (é o que roda em
     produção), Angular 8 é inventário de campos e **não** de regras, banco confirma uso real;
   - **armadilhas de ambiente** nomeadas: o `deploy-web.sh` sem `set -e` que "ecoa sucesso sem
     publicar", e o falso alarme de comparar timestamp de container (UTC) com Windows (BRT);
   - comandos prontos de subir, publicar, pegar token e testar.

### A regra de ouro das specs

> **Nenhuma linha de código antes de a spec dizer o que é "pronto".**

E a sequência de trabalho que você já pratica (vale como combinado do time):

```
spec (o que é pronto) → roadmap (onde encaixa) → implementação → evidência de volta na spec/roadmap
```

No `sysepesquisa` isso aparece como par de arquivos: `docs/plano-<tema>.md` (visão e fases) +
`docs/especificacao-<tema>.md` (contrato detalhado). Mostre os dois lado a lado — plano é
negociável, especificação é contrato.

### Exercício (15 min)
Cada dupla escreve a `§0 Critério de pronto` de uma feature real do backlog da Supridata.
Três condições, todas verificáveis por alguém que não participou da conversa. Leitura em voz alta e
crítica cruzada: *"isso eu consigo conferir sem te perguntar nada?"*

---

## 7. Módulo 4 — Skills (45 min)

Suas skills são o ativo mais valioso do treinamento, porque são **padrão de projeto executável**.

### Anatomia (mostre o arquivo)

`~/.claude/skills/sysetemplate-migrar-jsf/`:

```
SKILL.md                      143 linhas  ← o processo
references/api-templates.md   348 linhas  ← o código-padrão Spring
references/web-templates.md  1554 linhas  ← o código-padrão Angular
```

Dois pontos técnicos para ensinar aqui:

**1. Divulgação progressiva.** O `SKILL.md` é curto; os `references/` são grandes e só entram no
contexto quando a tarefa pede. Se você jogasse 1554 linhas de template Angular em `CLAUDE.md`,
pagaria esse custo em *toda* sessão, inclusive para corrigir um typo.

**2. A `description` é o gatilho.** Mostre a sua, que é um exemplo exemplar:

> *"Ative sempre que o usuário mencionar: 'migrar JSF', 'converter tela JSF', 'migrar ManagedBean'...
> Mesmo que o usuário não cite 'sysetemplate' explicitamente — se há código JSF legado para
> transformar, use este skill."*

Lição: a descrição não descreve a skill para humano, ela **lista as palavras que o usuário vai
digitar**. Skill com descrição vaga nunca dispara, e o dev conclui que "não funciona".

### O conteúdo que faz a skill valer

Abra a tabela de tradução do `sysetemplate-migrar-jsf` — é conhecimento sênior destilado:

| JSF/EJB legado | Equivalente moderno |
|---|---|
| `@ManagedBean` / `@Named` | `@Service` Spring |
| `@ViewScoped` / `@SessionScoped` | Stateless — estado vai para o Angular |
| `EntityManager` direto | `JpaRepository` + JPQL query methods |
| `FacesMessage` / Growl | `ToastService` no Angular |
| `faces-config.xml` navigation | `app.routes.ts` com `loadComponent` lazy |

E a dica que resume a arquitetura: *"Em JSF o estado fica no bean. No novo modelo, o estado fica no
Angular (signals) e a API é completamente stateless. Nunca gere serviços Spring com estado."*

### Skills componíveis

O conjunto `sysetemplate-bootstrap` / `sysetemplate-novo-endpoint` / `sysetemplate-nova-feature` é
exemplo de granularidade certa — uma por **tipo de tarefa**, não uma skill gigante. E o `AGENTS.md`
do `sysepesquisa` formaliza: *"As skills são cumulativas. Uma feature transversal de API e Angular
deve usar a skill de evolução segura e as skills de backend e frontend."*

### Skill com script: o detalhe que separa amador de profissional

`sysetemplate-tema-claro-escuro/scripts/` tem `migrar-cores.cjs` e `capturas-tema.cjs`.
Trocar 200 cores `#hex` por variáveis é trabalho **determinístico**: um script faz certo, barato e
igual toda vez. Pedir para o modelo fazer isso gasta tokens e introduz variação.

> **Regra:** se a tarefa é mecânica e verificável, a skill entrega um **script**.
> O modelo decide *quando* rodar; o script garante *como*.

### Onde cada skill mora

| Local | Escopo | Exemplo seu |
|---|---|---|
| `~/.claude/skills/` | todas as suas máquinas/projetos | `sysetemplate-migrar-jsf` |
| `<projeto>/.claude/skills/` | só aquele repo, versionado com ele | `syseprotocolo-bootstrap` |
| `<projeto>/.agents/skills/` | canônico multi-ferramenta (seu padrão no `sysepesquisa`) | `sysepesquisa-evolucao-segura` |

No `sysepesquisa` você já resolveu o problema de duas ferramentas: `.agents/skills/` é a fonte,
`.claude/skills/` é espelho de compatibilidade, e o `AGENTS.md` diz quem vence em caso de
divergência. Mostre isso — é a resposta pronta para "e quem usa Copilot?".

### Exercício (20 min) — o mais importante do dia
Cada dupla escreve uma skill pequena de verdade, extraída de algo que o time **já repete à mão**:
gerar um CRUD, criar migration Flyway, montar um relatório, padronizar um endpoint.
Entregável: `SKILL.md` com frontmatter (`name`, `description` com gatilhos), passos numerados e
**um** arquivo em `references/` com o código-padrão. Ao final, testar na própria máquina: abrir uma
sessão, digitar uma frase do gatilho e ver se a skill dispara.

---

## 8. Módulo 5 — Tokens, contexto e custo (45 min)

Esse módulo é o que o time mais vai agradecer, porque é onde a frustração vive.

### Por que a sessão "piora" depois de uma hora

Explique o mecanismo, sem misticismo: a janela enche; quando enche, o histórico é resumido; o
resumo perde detalhe. O resultado é o agente esquecendo uma decisão que você tomou há 40 minutos.

**Conclusão prática:** sessão longa é um sintoma, não um troféu. O que preserva conhecimento entre
sessões não é o histórico — é o **arquivo** (`ROADMAP.md`, spec, `CLAUDE.md`). É exatamente para
isso que existe o `017-continuidade-do-trabalho`.

### Os hábitos que economizam de verdade

1. **Uma tarefa, uma sessão.** Terminou a feature? Sessão nova. Conhecimento ficou no roadmap.
2. **Peça o arquivo certo, não "o projeto".** `cat` dirigido e busca por padrão custam uma fração de
   "leia a pasta `src`". Ensine a apontar: `EscrituracaoResource.java`, não "o backend".
3. **Divulgação progressiva** em tudo: `CLAUDE.md` magro + ponteiros; skill curta + `references/`.
   O `sysepesquisa/CLAUDE.md` de 5 linhas é o modelo.
4. **Script em vez de prompt** para trabalho mecânico (ver `migrar-cores.cjs`).
5. **Plano antes de código.** Revisar um plano de 20 linhas custa centavos; revisar 600 linhas de
   código errado custa a tarde. É também o que a regra 7 do `AGENTS.md` protege.
6. **Subagente para busca ampla.** "Procure onde isso é usado em 8 repos" deve voltar com a
   conclusão, não com os arquivos despejados na sua janela.
7. **Cuidado com saída de comando.** `mvn -q`, `--reporter=line` no Playwright, `head`/`grep` em log
   grande. Log de build inteiro no contexto é dinheiro queimado sem informação nova.
8. **Não cole o que já está em disco.** Dê o caminho.

### Dentro do Claude Code: qual modelo usar

| Situação | Modelo |
|---|---|
| Arquitetura, migração de regra crítica, depuração difícil, revisão | **Opus 5.5** (padrão) |
| Implementação rotineira dentro de uma spec já escrita, CRUD, refactor guiado | **Sonnet 5.5** |
| Tarefa mecânica em volume, classificação, busca, renomeação | **Haiku 5.5** |

Ensine também o **esforço (effort)**: a mesma qualidade de modelo com mais ou menos deliberação.
Rotina com spec pronta não precisa de esforço máximo; migração de cálculo fiscal precisa.
O erro comum é o oposto do que o time espera: **gastar pouco onde a decisão é caríssima** (desenho
de arquitetura, paridade com o legado) e muito onde não muda nada.

### Fora do Claude Code: IA dentro do produto

Como o `sysepesquisa` já chama LLM em produção (hoje OpenAI `gpt-4o-mini` via `RestClient`), vale
mostrar a tabela de preços da API da Claude por milhão de tokens — útil para a conta de custo por
tenant (valores de 2026-10-06; confira antes de citar em proposta comercial):

| Modelo | Entrada / 1M | Saída / 1M |
|---|---|---|
| Claude Haiku 5.5 | US$ 0,10 | US$ 0,50 |
| Claude Sonnet 5.5 | US$ 2,00 | US$ 10,00 |
| Claude Opus 5.5 | US$ 4,00 | US$ 20,00 |

E as três alavancas de custo em produção, na ordem:

1. **Prompt caching** — o prefixo estável (system prompt, catálogo de tabelas, lista de ferramentas)
   é cobrado como leitura de cache (US$ 0,20/1M nos modelos atuais) em vez de entrada cheia.
   A regra que ninguém lembra: **qualquer byte que mude no prefixo invalida tudo depois dele**.
   Timestamp no system prompt é o assassino clássico de cache.
2. **Modelo e esforço por rota** — extração e classificação em Haiku; conselho e análise em Sonnet
   ou Opus. Uma rota, um modelo, medido.
3. **Batch** para o que não é interativo (relatório noturno, reprocessamento) — metade do preço.

Antes de tudo isso, o que o `plano-ia.md` do `sysepesquisa` já faz certo e merece destaque:
**`LimitePlanoService.verificarPodeGerarIa` — enforcement de plano *antes* de gastar tokens**, e
`GeracaoIaLog` por tenant para auditoria e cota. Barato é não chamar.

---

## 9. Módulo 6 — RAG de verdade (45 min)

Aqui você tem o diferencial do treinamento: um exemplo **seu**, em produção, que contraria a receita
de blog.

### Abra `sysepesquisa/docs/catalogo-semantico-consultas-ia.md`

O objetivo, nas suas palavras: *"oferecer perguntas flexíveis sobre dados agregados de pesquisas sem
entregar à IA acesso direto ao banco. A IA propõe uma consulta; parser, catálogo, executor read-only
e limites do backend decidem se ela pode ser executada."*

O desenho:

- **Allowlist de tabelas e colunas**, com sinônimos por tabela (`pesquisa` ← "questionário",
  "levantamento") — é o "embedding" do pobre, e funciona melhor em domínio fechado.
- **Allowlist de joins**: seis caminhos permitidos. *"Outras combinações devem ser rejeitadas, mesmo
  quando as duas tabelas forem permitidas isoladamente."*
- **Allowlist de funções** (`COUNT`, `SUM`, `AVG`...).
- **Fora de escopo explícito**: `valorTexto` e qualquer resposta aberta individual, usuários, login,
  tokens, faturamento, logs de IA, tabela não listada.
- **Executor read-only** com limites do backend.

### A aula que isso dá

> **RAG não é "jogar documento no vetor". RAG é decidir o que o modelo pode ver e o que ele pode
> fazer com isso.**

Três padrões para o time distinguir:

| Padrão | Quando | Exemplo na casa |
|---|---|---|
| **Catálogo + consulta validada** | dado estruturado, pergunta analítica | catálogo semântico do SysEPesquisa |
| **Busca semântica (embeddings)** | texto livre, muito volume, pergunta vaga | respostas abertas de pesquisa, documentos de protocolo |
| **Contexto curado à mão** | poucos documentos, alto valor | os próprios `.spec/` e `CLAUDE.md` — RAG de desenvolvimento |

Repare no terceiro: **o que vocês já fazem com `CLAUDE.md` e `.spec/` é RAG manual**. Essa conexão
costuma ser o momento "ah, entendi" do treinamento.

### O harness de IA em produto (do `plano-ia.md`)

Cinco camadas, todas já implementadas por você — apresente como checklist obrigatório para qualquer
feature de IA na Supridata:

1. **Sanitização de entrada** contra prompt injection.
2. **Validação rígida da saída** — tipos permitidos, alternativas obrigatórias por tipo de pergunta.
   O modelo propõe; o código decide se aceita.
3. **Enforcement de plano/cota antes da chamada.**
4. **Auditoria por tenant** (`GeracaoIaLog`, migration versionada).
5. **Mascaramento de PII** onde a resposta é aberta (Fase 3 do plano).

Feche o módulo com a frase do próprio documento, que é a tese do produto:
*"deixar de entregar números e passar a entregar interpretação e recomendação de ação"*.

---

## 10. Módulo 7 — Qualidade: a prova (30 min)

Sem este módulo o treinamento vira festa de produtividade. É o que fecha o laço.

O padrão `sysenota` é forte e deve ser a norma: **E2E Playwright comparando com o legado**, com nome
de arquivo que já diz o que faz — `remessa.legado.spec.ts`, `livro-fiscal.legado.spec.ts`,
`pix-caixa.legado.spec.ts`, `baixa-retorno.legado.spec.ts`, `dam-encerramento.legado.spec.ts`.

E o nível de rigor que você exige, que vale mostrar literalmente no roadmap:

- *"arquivo igual ao do legado byte a byte, menos a hora do cabeçalho"*;
- *"as guias dos dois sistemas saem iguais pixel a pixel"*;
- o E2E do livro fiscal *"encerra pela tela do legado e exige o mesmo texto em todas as páginas"*.

Isso ensina a hierarquia de confiança que o time precisa internalizar:

```
compila  <  teste unitário  <  E2E na pilha local  <  comparação byte a byte com o legado
```

E o combinado: **o agente não declara pronto; o teste declara.** O que ele diz sobre o próprio
trabalho é hipótese até alguém rodar. No `plano-ia.md` você já escreve assim —
*"código concluído = implementado e compilando; ainda sem teste ponta a ponta com banco e chave
reais"* — status honesto em vez de ✅ generoso.

Mencione também os dois hábitos de controle: **`git` é seu** (*"nenhum commit feito pelo
assistente — o controle de versão é seu"*, do `plano-ia.md`) e **revisão antes de aceitar diff**.

---

## 11. Laboratório final (30 min)

Tarefa única, em duplas, usando o repo de verdade. Sequência obrigatória:

1. Escolher um item `[ ]` pendente de um `ROADMAP.md` real.
2. Escrever a spec mínima — só a `§0 Critério de pronto` e o mapa legado → novo.
3. Implementar com a skill apropriada (`sysetemplate-novo-endpoint` ou `nova-feature`).
4. Rodar o teste.
5. Atualizar roadmap com status, data e **evidência**.

Avaliação não é "funcionou?", é: **outra pessoa consegue retomar isso amanhã lendo só os arquivos?**

---

## 12. One-pager para distribuir (imprima)

```
OS QUATRO ARTEFATOS
  CLAUDE.md / AGENTS.md   como se trabalha neste repo (magro, com ponteiros)
  ROADMAP.md              onde estamos — ID, status, data, EVIDÊNCIA
  .spec/NNN-*.spec.md     o que é "pronto" — critério verificável por terceiro
  .claude/skills/         como se faz esta tarefa — gatilho + passos + references/

ANTES DE PEDIR CÓDIGO
  [ ] a spec diz o que é "pronto"?
  [ ] a regra do legado foi conferida na fonte?
  [ ] a skill certa existe? (se eu já fiz isso 3x à mão, ela deveria existir)
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
  compila < unitário < E2E na pilha local < igual ao legado, comprovado
  o agente não declara pronto; o teste declara.
  status sem evidência é opinião.
```

---

## 13. Sugestões de execução (minhas, para você decidir)

- **Não comece por skills.** A tentação é mostrar o brinquedo. Mas skill sem spec e sem roadmap vira
  gerador de código rápido e errado. A ordem memória → roadmap → spec → skill é pedagógica.
- **Use um repo só como fio condutor.** Sugiro o `sysenota`: tem os quatro artefatos, o legado ao
  lado para comparar e os E2E de paridade. O `syscomm` entra como caso avançado (as 24 specs e o
  documento de retomada).
- **Mostre o erro.** Rode uma sessão deliberadamente sem contexto e deixe falhar na frente deles.
  A memória do treinamento vai ser essa, não o slide.
- **Termine com combinados escritos**, não com aplausos: quem é dono do `CLAUDE.md` de cada repo,
  quais 3 skills o time cria no primeiro mês, qual é a definição de pronto na Supridata. Sem isso o
  treinamento evapora em duas semanas.
- **Material de apoio**: entregue o one-pager impresso e os três templates em branco
  (`CLAUDE.md`, `spec`, `SKILL.md`) no repositório deles, não por e-mail.
- **Pós-treinamento**: proponha uma sessão de 1h duas semanas depois, revisando as skills que o time
  escreveu de verdade. É onde o padrão pega ou morre.
