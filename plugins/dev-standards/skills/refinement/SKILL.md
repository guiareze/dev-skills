---
name: refinement
description: Refinamento e planejamento obrigatório antes de codificar — elabora um plano mínimo, dimensiona a complexidade da tarefa e indica o modelo/agente mais apropriado (Sonnet, Opus ou Fable), exigindo confirmação do usuário antes de implementar. Use ao receber qualquer pedido de desenvolvimento, nova feature, refatoração ou correção de bug, antes de alterar qualquer código.
---

# Refinamento e Planejamento

Etapa obrigatória antes de escrever código. Define como a tarefa é entendida, dimensionada e confirmada com o usuário.

## Planejamento obrigatório antes de codificar

Nunca implementar direto. Toda solicitação de desenvolvimento passa primeiro por um plano mínimo, apresentado ao usuário antes de qualquer alteração de código.

- O plano deve ser enxuto e proporcional à complexidade do pedido — o que será alterado/criado, arquivos/áreas impactadas, e a abordagem escolhida. Sem sobre-detalhar tarefas simples.
- O plano deve indicar o agente mais apropriado para executar a tarefa, com base na complexidade real do pedido:
  - **Sonnet**: tarefas do dia a dia, simples e diretas (ex.: ajustar um parâmetro, um `if/else`, um bugfix pontual).
  - **Opus**: tarefas intermediárias (ex.: nova feature de porte médio, refatoração localizada, integração com um serviço já conhecido no projeto).
  - **Fable**: tarefas complexas (ex.: mudança arquitetural, migração de versão, desenho de um novo módulo do zero).
- Nunca escolher um agente acima do necessário para a complexidade real da tarefa — ex.: não usar Fable para alterar um parâmetro ou um `if/else` simples.
- Sempre confirmar com o usuário se o plano e o agente indicado estão corretos antes de iniciar a implementação. Só prosseguir após a confirmação.

## Em aberto (a desenvolver)

- Mecanismo da troca de modelo: a IA não troca o próprio modelo em sessão — a recomendação é para o usuário aplicar via `/model`, ou para delegar a um subagente com modelo específico. Definir a redação.
- Se a IA deve alertar quando o modelo atual está superdimensionado para a tarefa.
- Se a IA deve parar e pedir troca, ou seguir avisando do risco, quando o modelo atual for fraco demais.
