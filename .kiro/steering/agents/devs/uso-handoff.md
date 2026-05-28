# Agente Dev — Gerador de SQL a partir de DENAs

## O que faz

Recebe um DENA refinado (markdown vindo do `agents-template`) e gera **SELECT funcional pronto pra testar** no Athena ou Redshift.

**Output:** queries SQL em formato de protótipo. O engenheiro de dados decide depois se vira view, tabela materializada ou parte de um job maior.

**NÃO faz:** DDL (`CREATE TABLE`), pipelines de carga (`INSERT`/`TRUNCATE`), criação de view de produção (`CREATE OR REPLACE VIEW`). Foco é só na **lógica do SELECT**.

---

## Pré-requisito: workspace correto

Este agente foi desenhado para rodar dentro do **repositório de catálogo do Data Lake** da sua área. Isso significa que você precisa:

1. **Clonar o repo** de catálogo localmente
2. **Abrir essa pasta no Kiro** (não outra)
3. Garantir que `.kiro/steering/` está na raiz do repo, com os 4 arquivos:
   - `dev-agent.md`
   - `padroes-sql-cogna.md`
   - `exemplos-validados.md`
   - `uso-handoff.md`

Por que isso importa: o agente lê arquivos do próprio repo pra confirmar nomes reais de tabelas e colunas. Sem o repo aberto, ele não tem como validar os nomes — e vai marcar tudo como `[?]`.

---

## Como usar — fluxo padrão

### Caso 1: refinamento completo (com DENA)

1. **No workspace `agents-template`:** gere o DENA normalmente.
2. **Copie o markdown completo do DENA.**
3. **Abra o workspace `agents-devs`** (este repo de catálogo).
4. **Cole o DENA no chat lateral.**
5. O agente vai:
   - Mapear sistemas disponíveis no repo
   - Perguntar engine (Athena/Redshift) se não estiver claro
   - Perguntar estratégia (1 query por indicador / consolidada / híbrida)
   - Listar premissas e pedir confirmação
6. Após confirmação, gera as queries SQL.
7. **Você testa no Athena/Redshift** e ajusta se necessário.
8. **Passa pro engenheiro** o SQL aprovado.

### Caso 2: query ad-hoc (sem DENA)

Pra queries pontuais que não passaram por refinamento formal:

1. No chat, escreva o que precisa em linguagem natural. Exemplo:
   > "preciso de uma query que conta funcionários ativos por VP, em Athena"
2. O agente vai fazer até 3 perguntas em batch sobre detalhes.
3. Gera o SELECT.

---

## Como pedir ajuste numa query

Não precisa repetir o DENA. Só diga o ajuste:

> "ajusta a query 2 pra adicionar filtro de admissão >= 2024"

O agente reentrega a query 2 inteira, atualizada.

---

## O que esperar do output

Pra cada DENA com tabela de indicadores, você recebe:

```
## Query 1 — Nome do indicador 1
[SELECT formatado]

## Query 2 — Nome do indicador 2
[SELECT formatado]

⚠️ Pontos pra validar antes de testar
- Tabela X marcada com [?] — confirmar nome com eng
- Premissa Y assumida — validar
- Sugestão: rodar com LIMIT 10 primeiro pra conferir colunas
```

---

## Quando o agente vai dizer "[? FALTA TABELA]"

Quando o DENA pede tabela de um sistema que **não tem pasta no repo de catálogo**. Isso significa:

- Ou o sistema ainda não foi ingerido (DDPL pendente)
- Ou a pasta está em outro repo (talvez outro domínio)
- Ou o nome do sistema no DENA está diferente do nome da pasta no repo

Quando isso acontecer, **não é bug do agente** — é informação útil. Significa que você precisa:
1. Confirmar com a Plataforma se a ingestão já foi feita
2. Ou ajustar o nome do sistema no DENA
3. Ou colar manualmente o DDL da tabela no chat e pedir pra usar

---

## Engines suportados

- **Athena** (Presto/Trino) — default pra schemas `dp_*_trusted` e maioria de `cdl_*_business`
- **Redshift** — default pra schemas `business_*` (sem prefixo `cdl_`)

Se ambíguo, o agente pergunta. Se você estiver inseguro, diga "não sei, sugere".

---

## Diferenças vs agents-template

| | agents-template | agents-devs |
|---|---|---|
| Input | Transcrição, notas, dados soltos | DENA refinado em markdown |
| Output | Markdown estruturado (épico/DENA/DDPL) | SQL funcional (SELECT) |
| Workspace | Qualquer pasta de refinamento | Repo de catálogo do domínio |
| Persona | Refinador conversacional | Ferramenta técnica direta |
| Pergunta de negócio? | Sim, quando faltar info | Não, só técnica (engine, estratégia) |

---

## Limitações conhecidas

- **Não conhece tabelas que não estão no repo aberto nem nos exemplos validados.** Se a tabela é nova ou tá em outro repo, você precisa colar o DDL no chat.
- **Não roda a query.** Você precisa testar manualmente no Athena/Redshift.
- **Não cria view nem tabela.** Foco é só na lógica do SELECT.
- **Não tem memória entre conversas.** Se você abrir chat novo, ele começa do zero.

---

## Manutenção

Os 4 arquivos de steering ficam em `.kiro/steering/` no repo. Pra ajustar:

- **Comportamento do agente** → `dev-agent.md`
- **Convenções de SQL** → `padroes-sql-cogna.md`
- **Adicionar nova query como referência** → `exemplos-validados.md`
- **Instruções de uso** → este arquivo (`uso-handoff.md`)

Após mudar qualquer arquivo, **reinicie o chat lateral** pro Kiro reler o steering.
