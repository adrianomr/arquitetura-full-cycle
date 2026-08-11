# Arquitetura Modular em Serviços Modernos — Estudo Prático

> Como a arquitetura modular é aplicada na prática em serviços modernos: **modular monolith**, modularização de **backend (Java/Spring)** e **frontend (Vue)**, e **screaming architecture**. Foco em evidência concreta — estrutura de pastas, repositórios de referência, empresas nomeadas.
>
> **Metodologia:** busca em múltiplas fontes decomposta em 6 ângulos → 27 fontes coletadas → 133 alegações extraídas → 25 verificadas por votação adversarial (3 votos; 2/3 refutam para descartar) → **24 confirmadas, 1 refutada**. A seção "Confiança" em cada resposta reflete o resultado da verificação.
>
> Data da pesquisa: 27/07/2026. Preferência por conteúdo 2022-2026 (exceções sinalizadas nas ressalvas).

---

## Resumo executivo

O **monólito modular** é uma arquitetura prática e ativamente adotada: entrega modularidade por domínio mantendo **uma única unidade de deploy**, evitando o overhead operacional dos microserviços. Roda em produção na **Shopify** (Rails, ~37 componentes organizados por conceito de negócio) e é citado academicamente em **Appsmith, Gusto** (time de Time Tracking) e **PlayTech** (Casino Backend).

No **backend Java**, a norma é **package-by-feature** (módulos de domínio como sub-pacotes da raiz, não por camada técnica) — exatamente o que screaming architecture prega. O **Spring Modulith** é a ferramenta oficial canônica: verifica fronteiras em tempo de teste e privilegia **eventos de domínio in-process** (`@ApplicationModuleListener` + Event Publication Registry, entrega *at-least-once*) em vez de chamadas diretas entre beans. **ArchUnit** e a **Packwerk** (Shopify) complementam a imposição estática, bloqueando PRs em minutos.

No **frontend**, a imposição concreta de fronteiras aparece no **Nx** (regra ESLint `@nx/enforce-module-boundaries` com `depConstraints` por tags); no ecossistema Vue, o padrão de organização mais citado é **Feature-Sliced Design (FSD)**.

A **migração corre nos dois sentidos**: o modular monolith tanto como alternativa a microserviços quanto como etapa intermediária (extração seletiva estilo *strangler*), e a **consolidação reversa** (Amazon Prime Video, corte de ~90% de custo).

**Onde a evidência é fraca** (sinalizado ao longo do texto): estrutura de pastas *específica* de Vue, micro-frontends/Module Federation, ownership por time (Lei de Conway) e dados quantitativos de CI/build. O recorte de IA repousa em um único preprint.

---

## Cluster A — Modular monolith na prática

### Q2 — Prós e contras: monólito modular vs microserviços
**Confiança: alta (3-0).**

O ponto central: o monólito modular busca **modularidade sem aumentar unidades de deploy**. A Shopify é explícita — *"a solution that increased modularity without increasing the number of deployment units, allowing us to get the advantages of both monoliths and microservices without so many of the downsides"*.

| | Monólito modular | Microserviços |
|---|---|---|
| **Deploy** | 1 unidade | N unidades independentes |
| **Complexidade operacional** | Baixa (sem rede/infra distribuída) | Alta — *"splitting a single monolithic application into a distributed system of services increases the overall complexity considerably"* (Shopify) |
| **Fronteiras** | Lógicas (impostas por ferramenta, não pela rede) | Físicas (processo/rede) |
| **Transação** | Local (ACID dentro do módulo) | Distribuída (saga, eventual) |
| **Escala independente** | Não (escala o monólito todo) | Sim (por serviço) |
| **Quando extrair serviço** | Só "por bons motivos" pontuais | Default |

A Shopify extrai serviços **seletivamente** — exemplos reais: renderização *read-only* de storefront (throughput altíssimo) e *vaulting* de cartão de crédito (dados sensíveis). O paper acadêmico reforça que *"monoliths and microservices are not perfect and they have their own different drawbacks"* — não há bala de prata.

### Q4 — Quais empresas usam modular monolith
**Confiança: alta (3-0).**

- **Shopify** — caso-farol. Monólito Rails com ~37 componentes internos, cada um com *public entrypoints*. (Ver ressalva sobre a data: número de ~2020.)
- Um **SLR acadêmico de 64 estudos** (arXiv:2401.11867, 2024) identifica quatro casos que escolheram modular monolith **em vez de** microserviços: **Shopify, Appsmith, Gusto** (time de Time Tracking) e **PlayTech** (Casino Backend Team).
- Corroboração adicional (não-primária): InfoQ, GitLab handbook, Milan Milanović.

---

## Cluster B — Estrutura de pastas / organização de código

### Q1a — Backend Java: package-by-feature (screaming architecture)
**Confiança: alta (3-0).**

Organizar **por conceito de negócio** (orders, shipping, inventory, billing), **não** por camada técnica (models/views/controllers). É isso que "screaming architecture" significa na prática: a árvore de diretórios grita o domínio, não o framework.

No **Spring Modulith** essa é a convenção *default* — cada sub-pacote direto do pacote-raiz é um módulo de aplicação:

```
com.example                      ← @SpringBootApplication (raiz)
├── order/                       ← módulo (API pública no topo do pacote)
│   ├── OrderService.java        ← tipo público (cross-module só por aqui)
│   └── internal/                ← encapsulado (invisível a outros módulos)
├── inventory/                   ← módulo
│   └── ...
└── billing/                     ← módulo
```

Exemplos de repositórios de referência (Spring Modulith + DDD):
- **`xsreality/spring-modulith-with-ddd`** — bounded contexts `Catalog` e `Borrow` (`example.catalog.*`, `example.borrow.*`).
- **`anton-liauchuk/educational-platform`** — 5 módulos de negócio (`administration`, `courses`, `course-enrollments`, `course-reviews`, `users`) + módulos de suporte (`common`, `configuration`, `security`, `web`). Cada módulo de negócio tem 3 sub-módulos: `application`, `integration-events`, `web`.

> Nota: hexagonal/clean e package-by-feature são **ortogonais** — você pode ter camadas (application/domain/infra) *dentro* de cada módulo de feature. As fontes verificadas confirmam a divisão por feature na raiz; a organização interna varia por repo.

### Q1b — Frontend Vue: organização por feature
**Confiança: baixa (sem evidência primária verificada; ver Perguntas em Aberto).**

Não emergiu evidência primária adversarialmente verificada de estrutura de pastas *específica de Vue em produção*. O padrão mais citado nas fontes (não verificado no top-25, mas de origem oficial) é o **Feature-Sliced Design (FSD)**, com camadas hierárquicas:

```
src/
├── app/        ← inicialização, providers, roteador raiz
├── pages/      ← telas (composição de widgets/features)
├── widgets/    ← blocos de UI compostos
├── features/   ← interações do usuário (casos de uso)
├── entities/   ← entidades de negócio
└── shared/     ← UI kit, libs, config (sem regra de negócio)
```

No FSD, dentro de um *slice* de feature convivem componente, composables, store Pinia e client de API — exatamente o espelho do package-by-feature do back. **Trate como leitura recomendada, não como achado verificado.**

---

## Cluster C — Fronteiras, comunicação e dados

### Q5 — Como as fronteiras entre módulos são impostas e verificadas
**Confiança: alta (3-0).**

Fronteiras são impostas **estaticamente / em tempo de teste** — o compilador não basta, então usa-se *fitness functions*:

**Backend (Java):**
- **Spring Modulith** — um `@Test` com `ApplicationModules.of(App.class).verify()` rejeita ciclos, acesso a tipos internos de outro módulo e dependências não declaradas (`@ApplicationModule(allowedDependencies = …)`). O encapsulamento vem da estrutura de sub-pacotes: *"it restricts type visibility between modules"*. Acesso cross-module só via interface pública no topo do pacote.
- **ArchUnit** — regras de camada/convenção (ex.: `LayerTest`, `CommandHandlerTest`, `IntegrationEventTest` com `@AnalyzeClasses`).
- **Packwerk (Shopify)** — análise estática de referências de constante; *"runs a full analysis on our largest codebase in a few minutes"* e *"reject changes that break the dependency graph or component encapsulation before they get merged"* — integrada ao fluxo de PR.

**Frontend:**
- **Nx** — regra ESLint `@nx/enforce-module-boundaries`, que checa imports TypeScript e dependências de `package.json` no lint. Fronteiras declaradas como `depConstraints` por **tags**:

```jsonc
// .eslintrc — @nx/enforce-module-boundaries
"depConstraints": [
  { "sourceTag": "scope:client", "onlyDependOnLibsWithTags": ["scope:client", "scope:shared"] },
  { "sourceTag": "scope:shared", "onlyDependOnLibsWithTags": ["scope:shared"] }
]
```
Aplica-se a projetos JS/TS, **incluindo Vue via `@nx/vue`** *(inferência: Vue é projeto JS/TS suportado; a doc do Nx não cita Vue literalmente)*.

### Q6 — Como os módulos se comunicam e como os dados são particionados
**Confiança: alta (3-0).**

**Comunicação — eventos de domínio in-process, não chamadas diretas.** O Spring Modulith prescreve: *"their primary means of interaction should be event publication and consumption. This avoids the originating module to know about all potentially interested parties"*. O atalho canônico é `@ApplicationModuleListener`, que combina `@Async` + `@Transactional(REQUIRES_NEW)` + `@TransactionalEventListener`.

Na prática (repo `xsreality`): o módulo consumidor reage ao evento *"with absolutely no changes needed in the Catalog module even though they are part of the same monolith"* — zero import cruzado.

**Refactor típico** — de dependência direta para evento:
```java
// ANTES: acoplado — precisa instanciar bean de outro módulo pra testar
inventory.updateStockFor(order);

// DEPOIS: desacoplado — publica evento, sem conhecer o consumidor
events.publishEvent(new OrderCompleted(order.getId()));
```

**Dados — ownership exclusivo por módulo.** Cada módulo é dono dos seus dados; *"it's forbidden to create a transaction which spans more than one bounded context"* (ADR do educational-platform). A entrega in-process é **at-least-once** via **Event Publication Registry**: a entrada de evento é escrita **na mesma transação de negócio**, marcada como completa só em sucesso; em falha, *"the log entry stays untouched so that retry mechanisms can be deployed"* — na prática, um **outbox transacional**. (Schema-por-módulo vs. banco compartilhado não teve fonte quantitativa; o princípio verificado é o *ownership* de dados por módulo e a proibição de transação cross-context.)

---

## Cluster D — Operação e evolução

### Q3 — Tempo de pipeline, build e testes
**Confiança: média (fatos 3-0, mas cobertura quantitativa limitada).**

Não há **benchmark head-to-head** (modular monolith vs microserviços) de tempo de CI nas fontes verificadas — apenas evidência anedótica, que favorece o monólito modular na velocidade de verificação:
- **Packwerk (Shopify)**: análise completa da maior base *"in a few minutes"*, integrada ao PR.
- **Spring Modulith**: `@ApplicationModuleTest` permite **seleção flexível de módulos** para testes de integração e observabilidade por módulo — isolar teste por módulo sem os custos de rede/infra de N serviços.

O argumento estrutural: no monólito modular os testes de integração rodam **in-process** (sem subir N serviços/redes), o que tende a dar CI mais rápido e determinístico. Mas isso é dedução a partir dos fatos, **não** um número medido. → Ver Perguntas em Aberto.

### Q7 — Migração nos dois sentidos
**Confiança: alta (3-0).**

- **Monólito → microserviços:** o modular monolith é apresentado como **alternativa** a microserviços **e** como **etapa intermediária** antes de migrar (*"it also could be a previous step before systems migrate to microservices"*). Martin Fowler (**MonolithFirst**) recomenda começar como monólito modular e extrair só quando o monólito vira problema. A Shopify extrai serviços em estilo *strangler*, "só por bons motivos".
- **Microserviços → monólito (consolidação reversa):** o time de monitoramento de qualidade de áudio/vídeo (VQA) do **Amazon Prime Video** cortou **~90% de custo de infra** ao consolidar uma arquitetura serverless distribuída (Step Functions/Lambda/S3) de volta em um serviço único.
  - ⚠ **Ressalva de enquadramento:** foi a consolidação de **uma ferramenta de um time** (orquestração serverless → serviço ECS), não uma reversão *company-wide* de microserviços clássicos. Adrian Cockcroft aceita os 90% mas critica o rótulo "microservices → monolith".

### Q8 — Lei de Conway e ownership por módulo
**Confiança: baixa (só tangencial; ver Perguntas em Aberto).**

Não emergiu evidência direta de *team topologies* mapeadas por módulo. O que há é indireto: a Shopify amarra **ownership de dados** e **API pública** ao componente (cada componente é dono dos seus dados e expõe entrypoints), o que é pré-condição para ownership por time — mas as fontes não descrevem a organização de times em si.

---

## Cluster E — Frontend e IA (seção secundária)

### Q9 — Vue: modular monolith vs micro-frontends
**Confiança: baixa (sem evidência primária de Vue; ver Perguntas em Aberto).**

O mecanismo genérico de fronteiras (Nx, acima) aplica-se a Vue via `@nx/vue`. Micro-frontends e Module Federation apareceram nas buscas (fontes FSD), mas **sem** estudo de caso primário verificado em Vue. Orientação prática destilada das fontes (não verificada): micro-frontend só compensa quando há **times independentes** que precisam **deploy independente**; para um time único, o SPA Vue **modular** (FSD/Nx) costuma bastar e evita o custo de orquestração. O front espelhar o back por feature é desejável, mas não obrigatório.

### Q10 — Arquitetura modular ajuda ou atrapalha o desenvolvimento apoiado por IA
**Confiança: baixa (voto 2-1, preprint único).**

Evidência dividida — a modularidade **ajuda** mas também pode ser **erodida**:
- **Ajuda:** módulos com fronteiras e API claras deixam o agente carregar **uma preocupação por vez** no contexto, sem puxar o sistema inteiro (argumento de praticante; fonte blog).
- **Erode / "modular mirage":** agentes conseguem **separação estrutural de arquivos** mas falham em **coesão semântica** — *"file separation does not equate to logical separation"*. Ou seja, código gerado por IA pode **parecer** modular sem ser (arXiv:2605.02741v1, Concordia, mai/2026).
- ⚠ Base fraca: experimento único (framework MetaGPT + Qwen-Coder 480B, 20 repos), preprint não revisado. A claim quantitativa correlata (volume de código ↔ *smells* arquiteturais, ρ=0.94) **foi refutada 0-3** e está excluída.

**Leitura para a esteira:** fronteiras impostas por ferramenta (ArchUnit/Spring Modulith/Nx) são o *guardrail* que impede o "modular mirage" — a IA gera, a *fitness function* reprova o que cruza fronteira indevida. É o mesmo princípio de imposição estática do Cluster C, agora como rede de proteção contra código gerado por IA.

---

## Ressalvas (o que relativizar)

- **Datas da Shopify:** os posts-farol são de **2019-2020**, fora da janela preferida 2022-2026. A arquitetura segue atual (Packwerk mantido, v3.x), mas o "37 componentes" é um retrato de ~2020 e pode ter mudado.
- **Amazon Prime Video:** enquadramento contestado (consolidação de *uma* ferramenta serverless de um time, não reversão *company-wide*). Fonte primária = blog Prime Video Tech (mar/2023); devclass rastreia a ela.
- **Rótulos do analista:** "package-by-feature" e "screaming architecture" são etiquetas mapeadas sobre as fontes, nem sempre *verbatim*.
- **Repos de referência** (`xsreality`, `educational-platform` etc.) são **demos educacionais de autor único**, não sistemas de produção.
- **Nx:** as claims são gerais de JS/TS; "inclui Vue" é inferência razoável (`@nx/vue`), não literal na doc.
- **Sem benchmark quantitativo** direto de CI/build/testes comparando as duas arquiteturas.

## Perguntas em aberto (lacunas de evidência)

1. **Vue (Q1b/Q9):** estruturas concretas de pastas por feature em Vue e tradeoffs reais de micro-frontends/Module Federation **em produção** — só emergiu o mecanismo genérico do Nx e o padrão FSD (não verificado).
2. **Lei de Conway (Q8):** como o ownership por módulo mapeia para times, na prática — falta evidência direta de *team topologies* por módulo.
3. **CI/build/testes (Q3):** comparação quantitativa real de tempo entre modular monolith e microserviços — só o anedótico "poucos minutos" da Packwerk.
4. **IA (Q10):** em escala e através de múltiplos frameworks/LLMs, IA realmente erode a modularidade, ou fronteiras impostas ajudam confiavelmente os agentes? Fonte única e conflitante com a contra-literatura.

## Afirmação refutada (NÃO confiar)

- ❌ *"O volume de código gerado por IA é preditor quase perfeito de degradação arquitetural (LOC ↔ smells, Spearman ρ=0.94, p<0.001)."* — refutada **0-3**. Fonte: arXiv:2605.02741v1. (O achado qualitativo do "modular mirage" do mesmo paper sobrevive com confiança baixa; a correlação quantitativa, não.)

---

## Referências

Por confiabilidade: **★★★** primária/oficial · **★★** imprensa técnica / repo de referência · **★** blog / opinião.

### Oficiais e primárias (★★★)

1. **Spring Modulith — Reference Docs** (oficial). Eventos, verificação e testes:
   - Eventos: <https://docs.spring.io/spring-modulith/reference/events.html>
   - Verificação de fronteiras: <https://docs.spring.io/spring-modulith/reference/verification.html>
   - Testes por módulo: <https://docs.spring.io/spring-modulith/reference/testing.html>
2. **Shopify Engineering — "Deconstructing the Monolith"** (2019). Reorganização por conceito de negócio, ownership de dados. <https://shopify.engineering/deconstructing-monolith-designing-software-maximizes-developer-productivity>
3. **Shopify Engineering — "The Story of Shopify's Monolith"** (2020). 37 componentes, Packwerk, extração seletiva. <https://shopify.engineering/shopify-monolith>
4. **Su & Li — "A Systematic Literature Review of Modular Monolith"** — arXiv:2401.11867 (2024, ACM Workshop). SLR de 64 estudos; casos Shopify/Appsmith/Gusto/PlayTech. <https://arxiv.org/pdf/2401.11867>
5. **Nx — "Enforce Module Boundaries"** (oficial). Regra ESLint `@nx/enforce-module-boundaries`, `depConstraints` por tag. <https://nx.dev/docs/features/enforce-module-boundaries>
6. **`spring-projects/spring-modulith`** (repo oficial). <https://github.com/spring-projects/spring-modulith>
7. **`xsreality/spring-modulith-with-ddd`** — modular monolith Spring Modulith + DDD (Catalog/Borrow). <https://github.com/xsreality/spring-modulith-with-ddd>
8. **`anton-liauchuk/educational-platform`** — 5 módulos de negócio + suporte; ADR proibindo transação cross-context. <https://github.com/anton-liauchuk/educational-platform>
9. **arXiv:2605.02741v1 — "modular mirage" em código gerado por IA** (Concordia, mai/2026). *Confiança baixa; correlação quantitativa refutada.* <https://arxiv.org/html/2605.02741v1>

### Imprensa técnica e repos (★★)

10. **DevClass — Prime Video "90% cost reduction"** (2023). <https://devclass.com/2023/05/05/reduce-costs-by-90-by-moving-from-microservices-to-monolith-amazon-internal-case-study-raises-eyebrows/>
11. **LeadDev — "How AI-generated code accelerates technical debt"**. <https://leaddev.com/technical-direction/how-ai-generated-code-accelerates-technical-debt>
12. **`UKHomeOffice/java-modular-monolith-example`** — exemplo Java de modular monolith. <https://github.com/UKHomeOffice-test/java-modular-monolith-example>

### Blogs e opinião (★)

13. **Martin Fowler — "MonolithFirst"**. Começar monólito, extrair depois. <https://martinfowler.com/bliki/MonolithFirst.html>
14. **Feature-Sliced Design — "Scalable Vue Architecture"** (oficial FSD). *Principal referência de estrutura de pastas Vue; não verificada adversarialmente.* <https://feature-sliced.design/blog/vue-application-architecture>
15. **Feature-Sliced Design — "Frontend Folder Structure"**. <https://feature-sliced.design/blog/frontend-folder-structure>
16. **Feature-Sliced Design — "Micro-frontend Architecture"**. <https://feature-sliced.design/blog/micro-frontend-architecture>
17. **Baeldung — "Spring Modulith"**. Guia prático. <https://www.baeldung.com/spring-modulith>
18. **dev.to (tleipzig) — "Spring Modulith vs Multi-Module Projects"**. <https://dev.to/tleipzig/spring-modulith-vs-multi-module-projects-advantages-and-disadvantages-55l>
19. **JavaCodeGeeks — "Spring Modulith 2.0: Enforcing Module Boundaries"** (2026). <https://www.javacodegeeks.com/2026/07/spring-modulith-2-0-enforcing-module-boundaries-before-microservices.html>
20. **dev.to (x4nent) — "The Modular Monolith 2026: Spring Modulith, ArchUnit, Fitness Functions"**. <https://dev.to/x4nent/the-modular-monolith-2026-complete-guide-spring-modulith-archunit-fitness-functions-and-lessons-878>
21. **senoritadeveloper (Medium) — "Modular Monolith with Spring Boot / Spring Modulith"**. <https://senoritadeveloper.medium.com/modular-monolith-with-spring-boot-spring-modulith-6687c234daab>
22. **Metamindz — "Modular Monolith Renaissance: Shopify, Amazon, Stack Overflow (2026)"**. <https://www.metamindz.co.uk/post/modular-monolith-renaissance-shopify-amazon-stack-overflow-2026>
23. **Shopify Enterprise Blog — "Monolithic to Microservices"**. <https://www.shopify.com/enterprise/blog/monolithic-to-microservices>
24. **SoftwareSeni — "Migrating from Microservices to Monolith (Strangler Fig Playbook)"**. <https://www.softwareseni.com/migrating-from-microservices-to-monolith-a-complete-consolidation-playbook-using-strangler-fig-pattern/>
25. **dev.to (sepehr) — "From Monolith to Modular Monolith to Microservices"**. <https://dev.to/sepehr/from-monolith-to-modular-monolith-to-microservices-realistic-migration-patterns-36f2>
26. **Arjan Franzen (Medium) — "Amazon Prime Video swaps microservices for monolith"**. <https://medium.com/@ArjanFranzen/amazon-prime-video-swaps-microservices-for-monolith-90-cost-reduction-zen-software-a2b792573131>
27. **aihero.dev — "How To Make Codebases AI Agents Love"**. Fronteiras claras ajudam agentes. <https://www.aihero.dev/how-to-make-codebases-ai-agents-love>
