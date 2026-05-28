---
inclusion: always
---

# Template — DENA | Card de Engenharia de Dados

> **Quando usar:** quando o épico envolve criação/alteração de **views, pipelines, indicadores ou modelagem** no Data Lake.
> **Output destino:** card no projeto DENA do Jira.
> **Insumo principal:** épico recém-criado + transcrição original.

---

## Estrutura obrigatória do output

```markdown
# [DENA] [Verbo] + [Objeto] + [Frente]

**Épico vinculado:** DAPL-XXXX  
**Camada destino:** [dp_*_trusted | cdl_*_business | gold]  
**Frequência de atualização:** [D-1 | semanal | por ciclo | tempo real]  

---

## Descrição

[1 parágrafo curto: o que vai ser desenvolvido e qual o produto final entregue. Inclua a origem dos dados e o destino esperado. Evite repetir contexto de negócio do épico — foque no técnico.]

---

## Regras de negócio

- [Regra 1 — específica, testável]
- [Regra 2]
- [Regra 3]
- ...

> Inclua aqui: frequência de atualização, regras de versionamento, regras de quebra/filtro obrigatórias, mapeamentos de domínio (ex: consenso → box), histórico exigido.

---

## Pontos importantes

- **Origem:** [sistema/tabela de origem]
- **Destino:** [schema + nome da view/tabela]
- **Frequência:** [D-1 | outra]
- **Escopo de dados:** [universo de registros — ex: "colaboradores avaliados na Elofy"]
- **Cruzamentos necessários:** [outras bases que precisam ser joinadas]
- **Mapeamentos a versionar:** [se houver de-para, listar e marcar para validação]

---

## Indicadores

> **Esta tabela é o coração do DENA. Sempre gere completa.**
> Se faltar informação de uma célula, use `[?]` na célula específica — não omita a linha.

| Frente | Indicador | Resumo | Quebras / Variações |
|---|---|---|---|
| [nome da frente] | [nome do indicador] | [o que mede em 1-2 frases] | [dimensões de análise: VP, raça, gênero, PCD, tempo de casa, etc.] |
| ... | ... | ... | ... |

### Filtros gerais aplicáveis a todos os indicadores
- [filtro 1 — ex: elegibilidade ao PRV]
- [filtro 2]

---

## Exemplo de estrutura da view final

```sql
-- Nome sugerido: vw_[dominio]_[indicador]_v1
-- Schema destino: dp_[area]_trusted
```

| Coluna | Tipo | Descrição |
|---|---|---|
| nome | varchar | Nome do colaborador |
| cpf | varchar | CPF |
| vp | varchar | Vice-presidência |
| ... | ... | ... |

> Liste as colunas que devem aparecer na view final. Marque `[?]` se faltar definir tipo ou descrição.

---

## Critérios de aceite técnicos

- [ ] View materializada no schema correto
- [ ] Atualização rodando na frequência acordada
- [ ] Dados conferidos contra fonte (Elofy / origem) em amostragem
- [ ] Histórico carregado conforme regra de negócio
- [ ] Documentação técnica no repositório
- [ ] [? outros critérios específicos da demanda]
```

---

## Instruções de preenchimento (para o agente)

### Geração da tabela de indicadores

A tabela é **obrigatória** e deve ser gerada sempre. Regras:

1. **Cada indicador mencionado na transcrição vira uma linha**. Não agrupe indicadores diferentes em uma linha só.
2. **Se a transcrição mencionar "métricas tipo X, Y, Z"**, gere uma linha por métrica, mesmo que precise marcar `[?]` em "Resumo" e "Quebras".
3. **Se a transcrição mencionar quebras gerais** (ex: "todos por VP, raça, PCD"), aplicar essas quebras em todas as linhas relevantes, mais as específicas de cada indicador.
4. **Mapeamentos de domínio** (ex: consenso 1 ou 4 = atenção): coloque em "Regras de negócio" e referencie na linha do indicador correspondente.

### Estrutura da view final

Se a transcrição mencionar colunas específicas que a view deve ter, liste todas. Se não mencionar, gere uma proposta razoável baseada nas quebras e marque `[? proposta — confirmar]`.

### Quando perguntar antes de gerar

Se faltar **qualquer um** destes itens críticos, faça **1 batch de perguntas** antes de gerar o DENA completo:

- Nome do schema destino
- Frequência de atualização
- Origem dos dados (sistema)
- Existência de mapeamentos de domínio

Pergunte no máximo 5 itens, numerados.

---

## Exemplo de tabela de indicadores bem preenchida

> **Use este exemplo como referência de granularidade e completude — não como modelo de conteúdo.**
> Os indicadores abaixo são de RH/Desempenho. Substitua pela frente e indicadores da demanda real.

| Frente | Indicador | Resumo | Quebras / Variações |
|---|---|---|---|
| [Frente de negócio] | [Nome do indicador] | [O que mede em 1-2 frases] | [Dimensões de análise: regional, produto, canal, período, perfil, etc.] |
| [Frente de negócio] | [Nome do indicador] | [O que mede em 1-2 frases] | [Dimensões + filtros aplicáveis] |
| [Frente de negócio] | [Nome do indicador] | [O que mede, incluindo regra de cálculo se houver] | [Dimensões + mapeamentos de domínio referenciados] |

**Como preencher cada coluna:**
- **Frente:** agrupador temático dos indicadores (ex: Captação, Financeiro, Operações, Desempenho)
- **Indicador:** nome do KPI/métrica, específico e sem ambiguidade
- **Resumo:** o que calcula + regra de negócio principal em 1-2 frases
- **Quebras / Variações:** dimensões pelas quais o indicador pode ser filtrado ou segmentado

**Filtros gerais:** se houver filtros que se aplicam a todos os indicadores da demanda (ex: período, elegibilidade, status ativo), liste aqui.

**Mapeamentos de domínio:** se houver de-para (ex: código → categoria, status → classificação), liste e sinalize para versionamento.
