# Checklists de coleta por tipo de demanda

Use o checklist do tipo identificado na Fase 1. Se a demanda combina tipos, aplicar todos.
Em todos os casos: **pedir exemplo real**, não descrição em prosa. Campo sem resposta vira PENDÊNCIA, nunca um chute.

## Comum a toda demanda

- Qual problema de negócio resolve e qual o resultado esperado.
- Quem consome / quem é impactado.
- Como funciona hoje (ou por que hoje não funciona).
- O que **não** pode quebrar.
- Repositório(s) envolvido(s) e ambiente(s) alvo.
- Prazo, dependência de terceiro, ou restrição conhecida.

## Endpoint REST novo

- Verbo e path completo, incluindo versionamento (`POST /v1/pedidos`).
- Autenticação e autorização exigidas; perfis/escopos com acesso.
- Headers obrigatórios e opcionais, com exemplo (`Content-Type`, correlação/trace, idempotência).
- Path variables e query params: nome, tipo, obrigatoriedade, valor de exemplo, default.
- **Request**: JSON de exemplo completo, com campos obrigatórios, tipos, formatos (data, decimal) e regras de validação.
- **Response de sucesso**: status HTTP e JSON de exemplo.
- **Responses de falha**: um exemplo por cenário — validação (400), autenticação (401/403), não encontrado (404), conflito (409), erro de dependência (502/503) — com o formato de erro padrão do projeto.
- Idempotência: a chamada pode ser repetida? Como é detectada a repetição?
- Paginação e ordenação, quando for listagem.
- Efeitos colaterais: grava em banco, publica evento, chama outro serviço?
- Volume esperado, tempo de resposta aceitável e comportamento sob timeout.

## Alteração em endpoint existente

- Tudo do bloco acima, para o estado **atual** e o **desejado**.
- Quem consome hoje esse endpoint.
- É breaking change? Se sim: versionar, manter compatibilidade ou coordenar a migração dos consumidores?
- Campo removido/renomeado tem período de convivência?

## Consumidor novo (fila, tópico, stream)

- Quem produz a mensagem e em que situação ela é publicada.
- Origem: nome da fila/tópico, tecnologia (SQS, SNS, Kafka, RabbitMQ), região/cluster, ambiente.
- **Payload de exemplo completo**, com tipos e campos obrigatórios.
- Atributos de mensagem e headers, com exemplo.
- Contrato versionado? Schema registry? Como evolui?
- Ordenação e agrupamento importam? Há chave de partição/`MessageGroupId`?
- Entrega duplicada é possível? Qual a chave de idempotência do lado do consumidor?
- Volume e pico esperados; concorrência de consumo.
- Política de erro: retry, backoff, DLQ, e o que fazer com mensagem inválida (descartar ou reter).
- O consumo é o gatilho de qual efeito? Grava, chama outro serviço, publica outro evento?

## Produtor novo

- Destino: nome da fila/tópico, tecnologia, região/cluster, ambiente.
- Quem consome (ou consumirá) e o que esse consumidor espera.
- **Estrutura da mensagem**: exemplo de payload, atributos e headers.
- Gatilho da publicação: qual evento de negócio dispara.
- Garantia exigida: pode perder? pode duplicar? precisa de ordenação?
- Publicação é transacional com a gravação em banco? (outbox, commit ordering)
- Volume esperado.
- O que fazer quando a publicação falha.

## Integração nova com aplicação/serviço existente

- Qual serviço, quem é o dono e onde está a documentação/contrato.
- Base URL por ambiente e como é feita a autenticação (credencial, token, mTLS) e onde ela é guardada.
- Endpoints consumidos: tudo do bloco *Endpoint REST novo*, do ponto de vista de cliente.
- SLA/latência esperada, limite de requisições (rate limit) e política de retry aceita pelo dono.
- Timeout, circuit breaker e comportamento em indisponibilidade (falhar, degradar, enfileirar).
- Ambiente de teste/sandbox disponível? Como validar sem afetar produção?

## Job agendado / processamento em lote

- Periodicidade e janela de execução; fuso.
- Origem dos dados e volume por execução.
- É idempotente? O que acontece se rodar duas vezes ou se a execução anterior falhou?
- Como é acionado (cron, scheduler, evento) e como é observado.
- Critério de parada e tratamento de item com erro no meio do lote.

## Mudança de modelo de dados / migration

- Tabelas e colunas afetadas; tipos e constraints.
- Volume atual dos dados e tempo estimado da migração.
- É compatível para trás? A aplicação antiga funciona com o schema novo durante o deploy?
- Precisa de backfill? Qual a estratégia e como reverter.
- Impacto em índices, performance de consulta e locks.

## Correção de bug

- Comportamento observado x comportamento esperado.
- Passos de reprodução, ambiente e data/hora da ocorrência.
- Evidência: log, stack trace, ID de correlação, payload que causou a falha.
- Abrangência: quantos casos, desde quando, há dado corrompido a corrigir?
- Existe workaround em uso hoje?
- É regressão? Qual mudança recente pode ter causado?

## Refatoração / mudança técnica sem efeito funcional

- Motivação concreta (dívida, performance, preparação para outra mudança).
- Garantia de comportamento inalterado: qual cobertura de teste existe hoje na área?
- Fronteira do refactor: onde ele começa e, sobretudo, onde termina.
- Como validar que nada mudou do ponto de vista de quem consome.
