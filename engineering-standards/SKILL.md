---
name: engineering-standards
description: Use ao escrever, revisar ou planejar código Java/Spring Boot/AWS neste projeto — aplica padrões de clean code, SOLID, design patterns e versões de linguagem/framework aprovadas por este time.
---

# Padrões de Engenharia — Java + Spring Boot + AWS

Regras de desenvolvimento a seguir em qualquer código Java/Spring Boot/AWS neste projeto. Não cobre testes, Definition of Done ou critérios de entrega — isso está em outra skill.

## Planejamento obrigatório antes de codificar

Nunca implementar direto. Toda solicitação de desenvolvimento passa primeiro por um plano mínimo, apresentado ao usuário antes de qualquer alteração de código.

- O plano deve ser enxuto e proporcional à complexidade do pedido — o que será alterado/criado, arquivos/áreas impactadas, e a abordagem escolhida. Sem sobre-detalhar tarefas simples.
- O plano deve indicar o agente mais apropriado para executar a tarefa, com base na complexidade real do pedido:
  - **Sonnet**: tarefas do dia a dia, simples e diretas (ex.: ajustar um parâmetro, um `if/else`, um bugfix pontual).
  - **Opus**: tarefas intermediárias (ex.: nova feature de porte médio, refatoração localizada, integração com um serviço já conhecido no projeto).
  - **Fable**: tarefas complexas (ex.: mudança arquitetural, migração de versão, desenho de um novo módulo do zero).
- Nunca escolher um agente acima do necessário para a complexidade real da tarefa — ex.: não usar Fable para alterar um parâmetro ou um `if/else` simples.
- Sempre confirmar com o usuário se o plano e o agente indicado estão corretos antes de iniciar a implementação. Só prosseguir após a confirmação.

## Disciplina de execução

Regras de como a IA deve trabalhar ao implementar — valem em toda alteração de código.

- **Escopo fechado**: alterar apenas o que o pedido exige. Não refatorar código não relacionado, não reformatar arquivos inteiros, não "melhorar de passagem". Se identificar algo que merece mudança fora do escopo, relatar ao usuário em vez de alterar.
- **Não inventar API**: antes de usar um método, anotação ou recurso de biblioteca, validar que ele existe na versão declarada no `pom.xml`/`build.gradle`. Se a versão do projeto não suporta, dizer isso — não improvisar assinatura.
- **Sem placeholder**: não entregar `// TODO implementar`, stub vazio ou método retornando `null` para completar depois. Se algo não puder ser implementado, declarar explicitamente em vez de deixar buraco no código.
- **Verificar que compila** antes de declarar a tarefa concluída.
- **Relatar com honestidade**: se algo do plano foi pulado, não funcionou ou ficou incompleto, dizer claramente. Não entregar como pronto o que não foi verificado.

## Versões e releases

- Projeto novo: usar Java 25 (LTS) com Spring Boot 4.x (que traz Spring Framework 7). Declarar sempre a versão do Spring Boot no `pom.xml` — é ela que governa o Framework.
- Baseline: Spring Boot 4 / Framework 7 aceitam Java 17+, mas o padrão deste time é Java 25.
- Projeto existente: manter a versão de Java/Spring já usada pelo projeto — não forçar upgrade sem necessidade.
- Se o projeto existente estiver em Java < 17: recomendar upgrade para no mínimo Java 17, mas nunca fazer isso silenciosamente.
  - Sempre perguntar ao usuário antes de migrar, explicando os efeitos colaterais possíveis (dependências incompatíveis, comportamento de libs, build quebrando) e deixando claro que a responsabilidade pela decisão é dele.
  - Se o usuário aceitar migrar, fazer uma varredura no projeto para identificar o que pode ser modernizado e propor como uma lista de sugestões (não aplicar tudo de forma automática). Exemplos do que verificar: uso de records em DTOs, virtual threads como alternativa a Reactor/outras formas de concorrência, versões de dependências no `pom.xml`/`build.gradle` (parent do Spring Boot, Spring Framework, libs principais).
- Ao atualizar dependências de framework em projeto existente, preferir a versão estável mais recente da linha já usada no projeto, não a última major, salvo decisão explícita do usuário.

## Idioma do projeto

- Seguir sempre o padrão de idioma já usado no projeto (nomes de classes, métodos, comentários, mensagens). Analisar os principais fluxos antes de escrever código novo.
- Se o projeto for majoritariamente pt-BR, seguir pt-BR. Se for en-US, seguir en-US.
- Na ausência de um padrão claro (projeto novo ou misto), a preferência padrão é inglês (en-US).

## Tipo de projeto: novo vs. manutenção

**Projeto novo**: perguntar ao usuário qual padrão arquitetural ele quer antes de estruturar o código — MVC em camadas ou Arquitetura Hexagonal (Ports & Adapters). Não assumir.

**Projeto existente (manutenção)**: antes de implementar, estudar o padrão real do projeto — não o que ele *deveria* ser.
- Mapear como camadas, pacotes e responsabilidades estão organizados na prática, não pelo nome que os devs deram (ex.: um projeto pode se dizer "hexagonal" sem seguir a separação de ports/adapters de verdade).
- Identificar convenções específicas do projeto antes de aplicar as suas: ex. constantes podem estar em `enum`, em classes `*Constants.java`, ou em outro formato — seguir o padrão já predominante, não introduzir um terceiro estilo.
- Fazer esse reconhecimento mínimo antes de codificar; encaixar as novas classes/alterações dentro do padrão identificado, mesmo que ele não seja o ideal — sinalizar inconsistências ao usuário, mas não refatorar por conta própria sem alinhamento.

## Configuração

- Projeto novo: usar `application.yml`.
- Projeto legado em `application.properties`: manter o formato existente — não migrar para `.yml` sem pedido explícito do usuário.
- Configuração deve sempre viver em `application.yml`/`application.properties` (com profiles), nunca hardcoded no código.
- Evitar classes de configuração com vários sets de propriedades manuais — sempre que possível, declarar as propriedades diretamente em `application.yml`/`application.properties` (ex. via `@ConfigurationProperties`), salvo exceções ou casos específicos que exijam configuração programática.

## Clean Code

- Nomes de classes, métodos e variáveis devem expressar intenção — sem abreviações obscuras.
- Métodos pequenos, com uma única responsabilidade; extrair quando um método faz mais de uma coisa.
- Evitar duplicação de lógica (DRY) — extrair para métodos/classes utilitárias quando o mesmo trecho se repete.
- Tratar erros de forma explícita: usar exceptions customizadas com significado de domínio em vez de `Exception`/`RuntimeException` genéricas, seguindo um padrão único de exceções em todo o projeto.
- Evitar magic numbers e strings soltas no código — usar constantes ou enums nomeados.
- Preferir imutabilidade sempre que possível (campos `final`, objetos de valor imutáveis).

## SOLID

- **SRP**: cada classe tem um único motivo para mudar. Se uma classe mistura regra de negócio com acesso a dados ou I/O, dividir.
- **OCP**: estender comportamento via novas implementações/interfaces em vez de alterar código existente já testado.
- **LSP**: implementações de uma interface devem ser substituíveis entre si sem quebrar o comportamento esperado pelo cliente.
- **ISP**: preferir interfaces pequenas e específicas a interfaces genéricas com métodos que nem todo implementador usa.
- **DIP**: dependências injetadas via construtor, apontando para abstrações (interfaces), não para implementações concretas.

## Design Patterns

- Usar padrões apenas quando resolvem um problema real do código atual — evitar over-engineering.
- Padrões comuns e úteis no contexto Spring Boot: Strategy (regras de negócio intercambiáveis), Builder/`toBuilder` (preferir para construir instâncias em vez de construtores com muitos parâmetros ou setters soltos), Repository (acesso a dados), DTO/Mapper (fronteira entre camadas), Adapter (integração com APIs/serviços externos).
- Se a necessidade de um padrão não estiver clara, preferir a solução mais simples primeiro.
- Ifs encadeados são um cheiro de código: antes de empilhar `if/else`, avaliar Strategy, enum com comportamento associado, ou outra alternativa que deixe o fluxo mais elegante e legível.

## Código elegante e avançado

O código entregue deve refletir nível sênior — não deve parecer júnior.

- Preferir Streams a laços `for` sempre que a legibilidade não piorar.
- Usar virtual threads (do básico ao avançado) sempre que a tarefa for elegível para concorrência leve.
- Não usar `var` — sempre declarar o tipo explícito do objeto.
- Usar `record` para DTOs e eventos.
- Classes de domínio são classes puras (não records): expõem métodos de comportamento (ex. `pedido.confirmar()`) que aplicam a regra de negócio internamente. A service orquestra o fluxo e chama esses métodos — nunca usa setters/campos para alterar o estado do domínio diretamente. Evitar domínio anêmico — regra de negócio pertence ao domínio, não à camada de serviço.
- Usar e abusar de anotações que reduzem boilerplate: Lombok, Jackson, JUnit, etc.
  - Lombok é liberado em DTOs, adapters, classes de configuração e demais classes de atributos onde fizer sentido.
  - Lombok **não** é usado em classes de domínio: `@Data`/`@Setter` geram os setters públicos que tornariam o domínio anêmico. Domínio expõe comportamento, não acessores gerados.
  - Evitar `@Data` em entidades JPA — `equals`/`hashCode` gerados quebram em entidade gerenciada e `toString` dispara lazy loading.
- Usar `Optional` sempre que possível para expressar ausência de valor, em vez de retornar `null`.
- Evitar texto solto (strings) em classes — mensagens, chaves, labels — usar enums ou classes dedicadas.

## Arquitetura Spring Boot

- Separar responsabilidades em camadas: `controller` (entrada HTTP), `service` (regra de negócio), `repository` (persistência) — ou conforme o padrão arquitetural definido para o projeto (MVC/Hexagonal).
- Nunca expor entidades JPA diretamente na API — usar DTOs (records) nas bordas (request/response).
- Injeção de dependência sempre via construtor (não `@Autowired` em campo).
- Sempre aplicar Bean Validation (`@Valid` + constraints) quando houver insumo a ser validado na entrada. Validação de formato/obrigatoriedade fica no DTO; regra de negócio fica no domínio.
- Mapeamento DTO ↔ domínio ↔ entidade: fazer manualmente, preferencialmente via builder. Evitar MapStruct — só usar se já for o padrão estabelecido no projeto.
- Convenção de pacotes: `br.com.guiareze.<nome_principal_do_projeto>` como raiz (tudo minúsculo, sem hífen ou caractere especial).
- Tratamento de exceções centralizado com `@ControllerAdvice`/`@ExceptionHandler` — um único handler por aplicação, com resposta de erro padronizada. Se o usuário não tiver definido um padrão de resposta de erro, perguntar antes de criar um novo.

## Logs e rastreabilidade

- Logs devem ser enxutos: lançar apenas nos pontos principais do fluxo, não em cada passo.
- Mensagens de log seguem o padrão do projeto e não ficam soltas como string no código — usar enum ou classe dedicada de mensagens.
- Todo fluxo deve ter um UUID de correlação:
  - Se a requisição já trouxer um id de correlação, reutilizá-lo — mas confirmar com o usuário que esse é o comportamento esperado antes de assumir.
  - Se não vier na requisição, gerar automaticamente no início do fluxo.
  - O id de correlação deve acompanhar o fluxo inteiro, inclusive nas chamadas a serviços externos.
- Nunca logar credenciais, tokens ou dados sensíveis/pessoais.

## Integrações externas e resiliência

- Para integração com outras aplicações/APIs, usar `RestClient` (ou `WebClient` em fluxo reativo) ou OpenFeign. Nunca usar `RestTemplate` ou outras ferramentas depreciadas.
- Chamadas externas **idempotentes** (GET, PUT, DELETE, ou POST com chave de idempotência) devem ter retry: no mínimo 3 tentativas, com intervalo de 500ms entre elas.
  - Spring Boot 4+ / Framework 7: usar o retry nativo (`@Retryable` do pacote `org.springframework.resilience.annotation`, com `@EnableResilientMethods`) — não é mais necessário o `spring-retry` como dependência externa.
  - Versões anteriores: usar Resilience4j.
- Chamadas **não-idempotentes** (ex.: POST que cria pagamento/pedido) não recebem retry automático — repetir pode duplicar o efeito. Ou se garante idempotência na origem (chave de idempotência), ou a falha sobe para tratamento explícito. Nunca anotar `@Retryable` sem verificar isso.
- Toda chamada externa deve ter timeout explícito de conexão e leitura — retry sem timeout empilha threads presas.
- Em integrações com falha sustentada, usar circuit breaker além do retry.

## Persistência

- Usar JPA quando fizer sentido para o caso de uso; JDBC Template ou similares não são proibidos — usar quando trouxer clareza ou performance.
- Consultas complexas: sempre usar query nativa em vez de tentar forçar em JPQL/Criteria.
- Query nativa sempre com bind de parâmetros (nomeados ou posicionais). **Nunca** concatenar valor de variável na string SQL — é onde SQL injection nasce.
- Inserções em massa: usar batch insert (ou mecanismo equivalente) sempre que possível e que valer a pena para o volume de dados.

## AWS

- Aplicar least privilege em políticas IAM — nunca usar permissões amplas (`*`) por conveniência.
- Nunca hardcodar credenciais ou segredos no código; usar Secrets Manager ou Parameter Store.
- Configuração de recursos AWS (região, endpoints, nomes de bucket/fila) via variáveis de ambiente ou profiles, nunca fixa no código.
- Usar AWS SDK v2 para Java em integrações novas.

## Comentários

- Evitar comentários que só repetem o que o código já diz.
- Comentários (incluindo os gerados por IA) só devem existir em pontos extremamente importantes ou onde houve uma decisão crítica de negócio que não é óbvia pelo código.
