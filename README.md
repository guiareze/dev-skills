# dev-skills

Coleção de _skills_ para o [Claude Code](https://code.claude.com) com os padrões de desenvolvimento usados em projetos **Java + Spring Boot + AWS**.

O objetivo é simples: em vez de repetir as mesmas orientações em toda sessão ("use record em DTO", "não use `var`", "faça um plano antes"), essas regras ficam versionadas aqui e são carregadas automaticamente pelo Claude quando a tarefa exige.

Este repositório é também um **marketplace de plugins** do Claude Code — qualquer pessoa pode instalar as skills na própria máquina com dois comandos (ver [Instalação](#instalação)).

## Como funciona

O Claude Code carrega apenas o **nome e a descrição** de cada skill no início da sessão (~100 tokens). O conteúdo completo só entra em contexto quando a tarefa realmente casa com a descrição — mecanismo chamado _progressive disclosure_. Na prática: as regras de Java não ocupam espaço quando você está mexendo em outra coisa.

## Skills disponíveis

O plugin `dev-standards` reúne três skills complementares, que cobrem etapas diferentes do trabalho:

| Skill | Quando dispara | O que faz |
|---|---|---|
| **`refinement`** | Ao receber um pedido de desenvolvimento, **antes** de qualquer alteração de código | Exige um plano mínimo antes de codificar, dimensiona a complexidade da tarefa e indica o modelo mais apropriado (Sonnet / Opus / Fable), pedindo confirmação antes de implementar |
| **`engineering-standards`** | Ao escrever, revisar ou refatorar código Java/Spring Boot | Padrões de código: versões de Java e Spring Boot, clean code, SOLID, design patterns, arquitetura, configuração, logs e rastreabilidade, resiliência em integrações, persistência e AWS |
| **`execution-discipline`** | Durante qualquer implementação, em qualquer linguagem | Guardrails de comportamento da IA: escopo fechado, sem código placeholder, não inventar API de biblioteca, verificar que compila e relatar o resultado com honestidade |

A ordem natural de uso é: **refinamento → padrões de código → disciplina na execução.**

## Instalação

Requisito: Claude Code instalado e atualizado.

**1. Adicionar este repositório como marketplace**

```
/plugin marketplace add guiareze/dev-skills
```

**2. Instalar o plugin**

```
/plugin install dev-standards@guiareze-dev-skills
```

Pronto. As skills passam a ser carregadas automaticamente quando a tarefa combinar com a descrição de cada uma — não é preciso invocá-las manualmente.

### Verificando a instalação

```
/plugin marketplace list     # confirma que o marketplace foi registrado
```

Para checar se as skills estão ativas, peça algo como _"quais skills você tem disponíveis?"_ em uma sessão nova.

### Atualizando

As skills evoluem. Para puxar a versão mais recente:

```
/plugin marketplace update guiareze-dev-skills
```

### Gerenciando ou removendo

```
/plugin                                     # menu interativo: habilitar, desabilitar ou remover plugins
/plugin marketplace remove guiareze-dev-skills
```

## Uso local (desenvolvimento das skills)

Para testar alterações antes de publicar, aponte o marketplace para o clone local em vez do GitHub:

```
/plugin marketplace add ./caminho/para/dev-skills
```

## Recomendação adicional

As regras da skill `execution-discipline` são de comportamento permanente — idealmente valem em **toda** sessão, não só quando a skill é acionada. Para isso, vale replicar o conteúdo dela no seu `CLAUDE.md` global (`~/.claude/CLAUDE.md`), que é carregado sempre.

## Estrutura do repositório

```
dev-skills/
├── .claude-plugin/
│   └── marketplace.json          # registro do marketplace
├── plugins/
│   └── dev-standards/
│       ├── .claude-plugin/
│       │   └── plugin.json       # manifesto do plugin
│       └── skills/
│           ├── refinement/SKILL.md
│           ├── engineering-standards/SKILL.md
│           └── execution-discipline/SKILL.md
└── README.md
```

## Roadmap

- [ ] Fechar as decisões em aberto da skill `refinement` (mecanismo de troca de modelo)
- [ ] Skill de testes, Definition of Done e critérios de entrega

## Licença

[MIT](LICENSE) — uso livre, inclusive comercial, mantendo o aviso de copyright.
