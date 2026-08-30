---
name: refinement
description: Refinamento tecnico obrigatorio antes de codificar — coleta os requisitos minimos da demanda (endpoint, consumidor, produtor, integracao, bugfix), analisa o impacto nos repositorios, monta um plano de implementacao estruturado com complexidade, confiabilidade e modelo recomendado, exige aprovacao do usuario e registra o plano como issue no GitHub vinculada ao Project. A execucao do plano e opcional e so ocorre se o usuario pedir. Use ao receber qualquer pedido de desenvolvimento, nova feature, refatoracao ou correcao de bug, antes de alterar qualquer codigo.
---

# Refinamento Técnico

Etapa obrigatória antes de escrever código. Transforma um pedido em um **plano de implementação aprovado e rastreado** numa issue do GitHub, que serve de base para a execução.

**Fluxo:** Coletar → Analisar impacto → Planejar → Aprovar → Registrar issue → *(opcional)* Executar.

A entrega desta skill é o plano aprovado e registrado na issue. A execução é um passo à parte, feito só se o usuário quiser seguir na mesma sessão.

Nenhuma fase é pulada e nenhuma linha de código é escrita antes da aprovação explícita do usuário.

---

## Fase 1 — Coleta

Objetivo: sair desta fase com informação suficiente para que a implementação não dependa de adivinhação.

- **Identificar o tipo da demanda** e aplicar o checklist correspondente em `references/coleta-por-tipo.md`. Se o pedido combina tipos (ex.: endpoint novo que também publica um evento), aplicar todos os checklists envolvidos.
- **Entender o negócio antes do técnico**: que problema resolve, quem consome, o que acontece hoje sem isso, o que não pode quebrar.
- **Perguntar em bloco, não em pingue-pongue**: agrupar as perguntas por assunto e numerá-las, para o usuário responder tudo de uma vez. No máximo 2 rodadas de perguntas.
- **Sempre pedir exemplo concreto**: um JSON real de request e de response vale mais que descrição em prosa. O mesmo para payload de mensagem, header e valor de configuração.
- **Não inventar o que falta.** Informação ausente vira **PENDÊNCIA** explícita no plano, nunca um chute silencioso.
- **Classificar a pendência**:
  - *Bloqueante* — impede começar (ex.: contrato do response, destino da mensagem). Com pendência bloqueante em aberto, não avançar para o plano final.
  - *Não bloqueante* — pode ser decidida durante a execução (ex.: nome de um método). Registrar com o default assumido.
- Se após 2 rodadas ainda faltar informação não bloqueante, seguir com **default explícito e declarado** no plano.

## Fase 2 — Análise de impacto

Antes de propor o plano, olhar o código de verdade. Não planejar de memória.

- **Mapear os repositórios afetados.** Se for mais de um, definir a ordem de implantação e a dependência entre eles (quem sobe primeiro, quem só pode subir depois).
- **Em cada repositório**: localizar os arquivos e camadas que serão criados e alterados, e as convenções vigentes a seguir (ver `dev-standards:engineering-standards`).
- **Levantar impacto sobre o que já existe**: contratos públicos, consumidores atuais do endpoint/tópico, schema de banco, mensageria, cache, feature flag, configuração por ambiente, jobs.
- **Marcar explicitamente**: breaking change, migração de dados, e o ponto de reversão (como desfazer se der errado).
- Se não houver acesso a um repositório impactado, **dizer isso** — não supor a estrutura dele.

## Fase 3 — Plano

O plano é o entregável desta skill. Deve ser enxuto e proporcional à demanda, mas sempre com estas seções:

1. **Contexto negocial** — 1 a 3 frases: o problema e o resultado esperado.
2. **Escopo** — o que entra e, explicitamente, **o que não entra**.
3. **Mudanças por repositório** — para cada repo: arquivos criados/alterados e o que muda em cada um. Prévia concreta, não genérica.
4. **Contratos e exemplos** — request/response, payload, headers, params, path variables, com exemplo real de cada caso (sucesso e falhas).
5. **Impactos em fluxos existentes** — o que passa a se comportar diferente, quem é afetado, se há breaking change e qual a migração.
6. **Riscos e pendências** — o que pode dar errado, o que ficou em aberto e qual default foi assumido.
7. **Validação** — como provar que funciona: testes a criar/rodar, cenários manuais, o que observar em log/métrica.
8. **Passos de implementação** — lista ordenada e verificável, na ordem de execução. Vira o checklist da issue.
9. **Dimensionamento** — complexidade, confiabilidade e modelo recomendado (abaixo).

### Dimensionamento

**Complexidade da mudança**
- *Baixa* — um repositório, sem mudança de contrato, sem migração, caminho conhecido.
- *Média* — um a dois repositórios, contrato novo ou alterado, integração já existente no projeto.
- *Alta* — múltiplos repositórios, mudança arquitetural, migração de dados, breaking change, ou área sem cobertura de teste.

**Confiabilidade do plano** — o quanto se pode confiar neste plano, não o quanto a feature é difícil.
- *Alta* — requisitos completos, código impactado lido, sem pendência bloqueante.
- *Média* — plano sólido, mas com default assumido ou área não inspecionada.
- *Baixa* — requisito ambíguo, repositório inacessível ou pendência bloqueante em aberto. **Com confiabilidade baixa não se implementa**: voltar para a Fase 1.

**Modelo recomendado** — recomendar por etapa quando as etapas diferem entre si (ex.: desenhar o módulo em Fable, aplicar os ajustes repetitivos em Sonnet).

| Modelo | Quando |
|---|---|
| **Haiku** | Trivial e mecânico: ajustar texto, constante, rename, bump de versão. |
| **Sonnet** | Dia a dia, simples e direta: um `if/else`, bugfix pontual, endpoint espelhado num já existente. |
| **Opus** | Intermediária: feature de porte médio, refatoração localizada, integração com serviço já conhecido no projeto. |
| **Fable** | Complexa: mudança arquitetural, migração de versão, novo módulo do zero, múltiplos repositórios acoplados. |

Nunca recomendar um modelo acima do necessário para a complexidade real.

**Divergência com o modelo em uso** — a IA não troca o próprio modelo em sessão. Ao final do plano, comparar o modelo recomendado com o que está executando o refinamento:
- *Modelo em uso abaixo do recomendado* — avisar antes de implementar e convidar o usuário a trocar com `/model`, ou a delegar a etapa a um subagente com o modelo adequado. Se o usuário optar por seguir assim mesmo, seguir — e registrar o risco na issue.
- *Modelo em uso acima do recomendado* — informar que dá para trocar para um modelo menor, uma vez, sem insistir.
- *Modelos iguais* — não comentar.

## Fase 4 — Aprovação

Apresentar o plano e **parar**. Só avançar após aprovação explícita do usuário. Ajuste pedido pelo usuário volta para a Fase 3 e é reapresentado.

## Fase 5 — Registrar a issue no GitHub

Com o plano aprovado, criar a issue **antes** de começar a implementar, seguindo `references/issue-github.md`: repositório alvo, label por tipo de demanda, corpo estruturado com o plano aprovado, vínculo com o GitHub Project e Status inicial *Todo*.

Confirmar o repositório alvo com o usuário antes de criar. Nunca criar issue sem plano aprovado.

## Fase 6 — Execução (opcional)

**O refinamento termina na Fase 5.** Com a issue criada, a entrega desta skill está completa: o plano está aprovado e registrado, pronto para ser executado agora, depois ou por outra pessoa.

Implementar **somente** se o usuário pedir ou confirmar que quer seguir na mesma sessão. Nunca emendar da issue direto para o código por conta própria — nem quando o plano parecer óbvio ou a mudança pequena.

Ao encerrar a Fase 5, apresentar a URL da issue e perguntar se deve seguir para a implementação agora, oferecendo também parar por aqui.

Se o usuário optar por seguir:

- Implementar conforme `dev-standards:execution-discipline`.
- Mover o Status do item no Project para `In Progress` ao iniciar.
- Manter a issue viva: marcar os passos concluídos no checklist e comentar as divergências relevantes entre o plano e o que foi feito.
- Se durante a execução a mudança se revelar materialmente diferente do plano aprovado, **parar** e voltar à Fase 3 com o replanejamento — não expandir o escopo por conta própria.

Se o usuário optar por não seguir, a issue fica em `Todo` e a sessão encerra aí — sem deixar alteração pendente no working tree.
