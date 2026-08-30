# Registro da issue no GitHub

Executar **somente com o plano aprovado** (Fase 4). A issue é o registro rastreável do plano e a base da execução.

## 1. Pré-requisitos

```bash
gh --version          # GitHub CLI instalado
gh auth status        # autenticado
gh repo view --json nameWithOwner -q .nameWithOwner   # repositorio alvo
gh project list --owner guiareze                      # projects visiveis (exige escopo project)
```

O `gh` pode existir na máquina e ainda assim não estar no `PATH` desta sessão — quando o Claude Code foi aberto antes da instalação, ele herda um ambiente antigo. Se `gh` não for encontrado, checar o caminho direto (`C:Program FilesGitHub CLIgh.exe`) antes de concluir que falta instalar; a correção definitiva é reabrir o Claude Code.

Se o `gh` realmente não estiver instalado ou autenticado, **não travar o refinamento**: entregar o corpo da issue pronto em um arquivo e informar o usuário, para que ele cole no GitHub ou instale o `gh`. Nunca fingir que a issue foi criada.

Confirmar com o usuário o repositório alvo antes de criar. Se a demanda impacta mais de um repositório: criar uma issue por repositório, com o mesmo plano recortado para o escopo daquele repo, e referenciar as demais no corpo (`owner/repo#123`), indicando qual é a principal e a ordem de implantação.

## 2. Label por tipo de demanda

| Tipo de demanda | Label |
|---|---|
| Feature, funcionalidade nova, endpoint/consumidor/produtor/integração novos | `enhancement` |
| Falha, comportamento errado, correção | `bug` |
| Documentação | `documentation` |
| Refatoração e dívida técnica sem efeito funcional | `refactor` |
| Manutenção, configuração, build, CI | `chore` |
| Atualização de biblioteca ou versão | `dependencies` |

Labels cumulativas, quando se aplicarem: `breaking-change`, `database`, `security`.

Regras:
- Listar as labels existentes antes (`gh label list`) e **usar apenas as que existem**.
- Se a label ideal não existir, propor a criação ao usuário e só criar após o OK:
  `gh label create refactor --description "Refatoracao sem efeito funcional" --color BFD4F2`
- Nunca criar label por conta própria.

## 3. Vincular ao GitHub Project e marcar como *todo*

Toda issue criada nesta etapa é vinculada ao Project e nasce com Status **Todo**.

**Projeto padrão deste usuário:**

| | |
|---|---|
| Project | `ia_project` — https://github.com/users/guiareze/projects/1 |
| Número / owner | `1` / `guiareze` (project de usuário, não de organização) |
| Campo de status | `Status` (single select) — opções: `Todo`, `In Progress`, `Done` |

Requer o escopo `project` no token (`gh auth refresh -s project`).

**Passo a passo, após criar a issue:**

```bash
# 1. ids do project, do campo Status e da opcao Todo (gh project view/field-list aceitam --jq)
PROJECT_ID=$(gh project view 1 --owner guiareze --format json --jq .id)
FIELD_ID=$(gh project field-list 1 --owner guiareze --format json \
  --jq '.fields[] | select(.name=="Status") | .id')
TODO_ID=$(gh project field-list 1 --owner guiareze --format json \
  --jq '.fields[] | select(.name=="Status") | .options[] | select(.name=="Todo") | .id')

# 2. adicionar a issue ao project e capturar o id do item
#    ATENCAO: item-add aceita --format json, mas NAO aceita --jq. Extrair o id do JSON retornado.
ITEM_ID=$(gh project item-add 1 --owner guiareze --url <url-da-issue> --format json \
  | node -e "process.stdin.on('data',d=>console.log(JSON.parse(d).id))")

# 3. definir o status como Todo
gh project item-edit --id "$ITEM_ID" --project-id "$PROJECT_ID" \
  --field-id "$FIELD_ID" --single-select-option-id "$TODO_ID"
```

Notas de uso verificadas nesta máquina:
- `gh project view` e `gh project field-list` aceitam `--jq`; `gh project item-add` e `item-edit` **não** aceitam — só `--format json`.
- `jq` não está instalado; usar `node -e` para ler o JSON, ou capturar o id manualmente da saída.

Descobrir os ids em tempo de execução, como acima — **não fixar os valores no comando**, porque eles mudam se o project ou o campo for recriado. Para conferência, os valores vigentes são `PVT_kwHOA1dJJ84Bh4Cl` (project), `PVTSSF_lAHOA1dJJ84Bh4ClzhgyH58` (Status) e `f75ad846` (Todo).

**Fallbacks**, em ordem, se o vínculo com o Project não for possível (outro repositório, sem escopo `project`, project inexistente):

1. Label de status, se o repositório usar (`todo`, `status:todo`, `backlog`).
2. O checklist do corpo — todo item desmarcado — como status *todo*.

Em qualquer fallback, dizer ao usuário que o vínculo com o Project não foi feito e por quê. Nunca dar como vinculado o que não foi.

## 4. Criar a issue

Escrever o corpo em arquivo e usar `--body-file` — evita problema de escape, acento e quebra de linha no shell.

```bash
gh issue create \
  --repo <owner>/<repo> \
  --title "<tipo>: <resumo objetivo em uma linha>" \
  --body-file <caminho-do-arquivo.md> \
  --label enhancement
```

Título: prefixo do tipo e resumo direto — `feat: endpoint de consulta de pedidos por cliente`, `fix: cobranca duplicada ao reprocessar evento`.

Em seguida, vincular ao Project e marcar como *Todo* (seção 3).

Ao final, devolver ao usuário a **URL da issue criada** e confirmar o vínculo com o Project.

## 5. Template do corpo

```markdown
## Contexto
<problema de negocio e resultado esperado, 1-3 frases>

## Escopo
**Entra:** <itens>
**Nao entra:** <itens explicitamente fora>

## Mudancas por repositorio
### <owner/repo>
| Arquivo / area | Acao | O que muda |
|---|---|---|
| `caminho/Arquivo.java` | criar / alterar | <descricao> |

## Contratos e exemplos
<request, response de sucesso, responses de falha, payload, headers — com exemplo real>

## Impactos em fluxos existentes
- <fluxo afetado e como>
- **Breaking change:** sim/nao — <qual e qual a migracao>
- **Reversao:** <como desfazer>

## Riscos e pendencias
- [ ] <pendencia em aberto e default assumido>
- <risco e mitigacao>

## Validacao
- <testes a criar/rodar, cenarios manuais, o que observar em log/metrica>

## Passos de implementacao
- [ ] 1. <passo>
- [ ] 2. <passo>

## Dimensionamento
- **Complexidade:** Baixa | Media | Alta
- **Confiabilidade do plano:** Alta | Media | Baixa — <por que>
- **Modelo recomendado:** <modelo> — <justificativa; por etapa, se diferirem>
- **Modelo usado no refinamento:** <modelo atual> <e o risco aceito, se o usuario optou por seguir com um modelo abaixo do recomendado>

---
Plano aprovado pelo usuario em <data>. Gerado pela skill `dev-standards:refinement`.
```

## 6. Durante a execução

- Marcar os itens do checklist conforme forem concluídos (`gh issue edit --body-file` com o corpo atualizado).
- Comentar divergências relevantes entre o plano e o que foi feito (`gh issue comment`).
- Referenciar a issue nos commits e no PR (`Refs #123`) — sem fechar automaticamente, a menos que o usuário peça.
- Ao iniciar a implementação, mover o Status do item no Project para `In Progress`; ao concluir e validar, para `Done` — usando o mesmo `item-edit` da seção 3 com o id da opção correspondente.
- Não fechar a issue por conta própria.
