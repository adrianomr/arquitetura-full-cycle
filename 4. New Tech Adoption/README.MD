# Avaliação de Tecnologia: [Nome da Tecnologia]

> **Status:** [Rascunho / Em Revisão / Piloto / Aprovado / Rejeitado]  
> **Responsável:** @[SeuNome/Equipe]  
> **Data:** DD/MM/AAAA

## 1. Resumo Executivo
Uma breve descrição do que é a tecnologia e por que ela entrou no nosso radar agora. Qual o "elevador pitch" dessa solução?

## 2. Definição do Problema
* **Gargalos Atuais:** O que hoje é lento, caro ou difícil de manter?
* **Objetivo de Negócio:** (Ex: Reduzir tempo de build em 30%, diminuir custos de infra, melhorar a experiência do desenvolvedor).
* **Alternativas:** Quais outras opções foram consideradas? Por que esta se destacou?

## 3. Scorecard de Avaliação
Atribua uma nota de 1 a 5 (1 = Ruim, 5 = Excelente).

| Categoria | Nota | Observações |
| :--- | :--- | :--- |
| **Aderência Estratégica** | | Combina com nossa stack atual e visão de longo prazo? |
| **Maturidade** | | Estabilidade das versões, frequência de atualizações. |
| **Ecossistema/Comunidade** | | Qualidade da documentação, fóruns, bibliotecas de apoio, profissionais qualificados. |
| **Curva de Aprendizado** | | Quão rápido o time consegue entregar algo real? |
| **Operacionalização** | | Facilidade de monitoramento, logs e deploy. |
| **Segurança** | | Histórico de vulnerabilidades e conformidade. |

## 4. Checklist de Prontidão para Produção (Validation Gate)
*Requisitos obrigatórios para sair do modo experimental.*

### 🛠 Prontidão Técnica
- [ ] **PoC (Prova de Conceito):** Um fluxo crítico foi replicado com sucesso.
- [ ] **Performance:** Testes de carga confirmam que atende aos requisitos de latência.
- [ ] **Escalabilidade:** Comportamento verificado sob alta demanda.
- [ ] **Observabilidade:** Integrado com nossas ferramentas de logs/métricas (ex: ELK, Grafano).

### 🛡 Segurança e Compliance
- [ ] **Licenciamento:** Licença de uso compatível (ex: MIT, Apache 2.0).
- [ ] **Varredura de Vulnerabilidades:** Verificado com Snyk/Trivy sem riscos críticos.
- [ ] **Segurança de Dados:** Atende aos requisitos da LGPD/GDPR.

### 👥 Prontidão do Time
- [ ] **Documentação Interna:** Guia de "Primeiros Passos" criado no Wiki/Confluence.
- [ ] **Plano de Suporte:** Quem acionar em caso de incidentes críticos?
- [ ] **Treinamento:** Pelo menos 2 membros do time dominam a ferramenta.

## 5. Matriz de Riscos
* **Risco:** [Ex: Dependência de um único fornecedor] -> **Mitigação:** [Ex: Usar camadas de abstração].
* **Risco:** [Ex: Tecnologia muito nova/hype] -> **Mitigação:** [Ex: Limitar o uso a microsserviços não-críticos inicialmente].

## 6. Recomendação Final
**Decisão:** [✅ Adotar / ⚠️ Apenas Testes / ❌ Rejeitar]

**Justificativa:**
Resumo dos motivos que levaram à decisão final.