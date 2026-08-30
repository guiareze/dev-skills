---
name: execution-discipline
description: Guardrails de execução para desenvolvimento assistido por IA — escopo fechado, sem código placeholder, não inventar API de biblioteca, verificar que compila e relatar o resultado com honestidade. Use durante qualquer implementação, alteração ou refatoração de código, em qualquer linguagem ou projeto.
---

# Disciplina de Execução

Regras de como a IA deve trabalhar ao implementar — valem em toda alteração de código, independente de linguagem ou projeto.

- **Escopo fechado**: alterar apenas o que o pedido exige. Não refatorar código não relacionado, não reformatar arquivos inteiros, não "melhorar de passagem". Se identificar algo que merece mudança fora do escopo, relatar ao usuário em vez de alterar.
- **Não inventar API**: antes de usar um método, anotação ou recurso de biblioteca, validar que ele existe na versão declarada no manifesto de dependências (`pom.xml`, `build.gradle`, `package.json`). Se a versão do projeto não suporta, dizer isso — não improvisar assinatura.
- **Sem placeholder**: não entregar `// TODO implementar`, stub vazio ou método retornando `null` para completar depois. Se algo não puder ser implementado, declarar explicitamente em vez de deixar buraco no código.
- **Verificar que compila** antes de declarar a tarefa concluída.
- **Relatar com honestidade**: se algo do plano foi pulado, não funcionou ou ficou incompleto, dizer claramente. Não entregar como pronto o que não foi verificado.

> Estas regras são de comportamento permanente. Para que valham em toda sessão — e não apenas quando esta skill for acionada — replicá-las também no `CLAUDE.md` do projeto ou em `~/.claude/CLAUDE.md`.
