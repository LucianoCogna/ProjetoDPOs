---
inclusion: always
---

# Padrões SQL — Cogna Educação

Convenções da casa, baseadas em queries validadas em produção. **Este documento é normativo** — quando gerar SQL, siga estas regras.

---

## 1. Engine: Athena vs Redshift

Os 2 engines são usados na casa. **Sintaxe diverge em pontos críticos.** Nunca misture.

### Quando usar cada um

| Sinal no DENA | Engine provável |
|---|---|
| Schema `dp_*_trusted` | Athena (Presto/Trino) |
| Schema `cdl_*_business` | Athena ou Redshift (perguntar) |
| Schema `business_*` (sem prefixo `cdl_`) | Redshift |
| DENA menciona "Presto" ou "Trino" | Athena |
| DENA menciona "Spectrum" ou colunas com `WITH NO SCHEMA BINDING` | Redshift |

Se ambíguo, **perguntar ao DPO antes de gerar.**

### Diferenças críticas de sintaxe

| Operação | Athena (Presto/Trino) | Redshift (PostgreSQL-like) |
|---|---|---|
| Data atual | `current_date` | `current_date` |
| Adicionar dias | `current_date - interval '1' day` | `dateadd(day, -1, current_date)` |
| Epoch → timestamp | `from_unixtime(coluna)` | `timestamp 'epoch' + cast(coluna as bigint) * interval '1 second'` |
| Extrair ano | `year(coluna_data)` | `extract(year from coluna_data)` |
| Concatenar | `concat(a, b)` ou `a \|\| b` | `concat(a, b)` ou `a \|\| b` |
| Regex contém | `regexp_like(coluna, padrão)` | `regexp_instr(coluna, padrão) > 0` |
| Extrair regex | `regexp_extract(coluna, padrão)` | `regexp_substr(coluna, padrão)` |
| Replace string | `replace(str, from, to)` | `replace(str, from, to)` |
| Split | `split_part(str, delim, n)` | `split_part(str, delim, n)` |
| Cast pra numérico | `cast(x as double)` | `cast(x as double precision)` |
| Null seguro | `coalesce(x, valor)` | `coalesce(x, valor)` |
| Uppercase | `upper(x)` | `upper(x)` |
| Trim | `trim(x)` | `trim(x)` |
| Aspas em identificador | `"schema"."tabela"` | `"schema"."tabela"` |

### Funções que NÃO existem (não invente)

- ❌ `GETDATE()` (T-SQL) — usar `current_date` ou `current_timestamp`
- ❌ `TOP N` (T-SQL) — usar `LIMIT N`
- ❌ `[brackets]` (T-SQL) — usar `"aspas duplas"`
- ❌ `IIF(cond, x, y)` — usar `CASE WHEN cond THEN x ELSE y END`

---

## 2. Naming patterns no Lake

### Schemas observados

- `dp_*_trusted` — camada Trusted (raw + tipagem mínima). Ex: `dp_gente_cultura_trusted`
- `cdl_*_business` — camada Business (modelagem dimensional). Ex: `cdl_gente_business`
- `business_*` — camada Business sem prefixo cdl (Redshift). Ex: `business_gec`

### Tabelas e views

- `fat_*` — tabelas de fato (eventos, métricas). Ex: `fat_funcionario_analitico`
- `dim_*` — tabelas de dimensão (entidades). Ex: `dim_funcionario`, `dim_tempo`
- `vw_*` — views analíticas. Ex: `vw_tempo_de_casa_funcao`
- `hst_*_v1`, `hst_*_v2` — tabelas de histórico, versionadas. Ex: `hst_ch1_estrutura_v1`
- `source_*` — tabelas de origem trusted. Ex: `source_blend_categoria_projeto`
- `tb_aux_*` — tabelas auxiliares. Ex: `tb_aux_bd_categoria_projeto`

### Colunas frequentes

- `cod_*` — códigos (chaves dimensionais). Ex: `cod_funcionario`, `cod_tempo`
- `nom_*` — nomes/descrições. Ex: `nom_funcionario`, `nom_situacao_funcionario`
- `num_*` — números (CPF, matrícula). Ex: `num_cpf`, `num_pis`
- `dat_*` — datas. Ex: `dat_admissao`, `dat_demissao`
- `qtd_*` — quantidades. Ex: `qtd_cpf_ativo`
- `vlr_*` — valores monetários. Ex: `vlr_salario`
- `eml_*` — emails. Ex: `eml_funcionario`
- `cs`, `n1`, `n2`, `n3` — níveis hierárquicos (estrutura organizacional)

---

## 3. Estilo de query

### Padrão de aliases

Escolha **um** padrão por query e mantenha consistência:

- **Aliases descritivos curtos** (preferível pra queries simples): `fat`, `func`, `funcao`, `dim`, `cert`
- **Aliases numerados** (aceitável pra queries muito complexas com muitos joins): `T00`, `T13`, `T22`

Nunca misture os dois no mesmo SELECT.

### Joins

- **LEFT JOIN é o default.** Use quando o match pode não existir.
- **INNER JOIN só quando regra de negócio exige obrigatoriedade.**
- **Schema qualificado entre aspas duplas** quando cross-schema:
  ```sql
  LEFT JOIN "[schema_destino]"."[tabela_dimensao]" alias ON ...
  ```

### CTEs (WITH)

Use CTEs quando:

- A lógica tem **etapas claras** (limpeza → enriquecimento → agregação)
- Algo é **referenciado mais de uma vez**
- Você precisa **deduplicar antes de juntar** (ROW_NUMBER + filtro `rn = 1`)
- Existe **classificação hierárquica** (ex: mapeamento consenso → box)

Nomeie em snake_case descritivo: `usuarios_validos`, `cargos_classificados`, `agregacao_por_genero`. **Não use** `cte1`, `cte2`, `tmp`.

---

## 4. Padrões defensivos da casa

### Validação de CPF

CPFs vêm sujos de várias fontes (Moodle, RM, Elofy). Padrão da casa:

```sql
WHERE num_cpf IS NOT NULL
  AND regexp_instr(num_cpf, '^[0-9]{11}$') > 0   -- Redshift
-- ou
  AND regexp_like(num_cpf, '^[0-9]{11}$')        -- Athena
```

### Tratamento de NULL em strings

Padrão defensivo antes de comparar strings:

```sql
upper(coalesce(nom_campo, '')) like '%PADRAO%'
upper(coalesce(nom_campo, '')) = 'VALOR_EXATO'
```

### Demissão / data sentinel

⚠️ **Importante:** No Data Warehouse, datas "null" são representadas como `'1900-01-01'`, não como NULL real.

```sql
-- Errado:
WHERE dat_demissao IS NULL                           -- não retorna ativos!

-- Certo:
WHERE cast(dat_demissao as date) <= date '1900-01-01'  -- ativos
WHERE cast(dat_demissao as date) > date '1900-01-01'   -- demitidos
```

### Cast em joins de tipos divergentes

Se as PKs são tipos diferentes (comum em joins entre `cod_tempo` integer e `id_dim_tempo` varchar):

```sql
LEFT JOIN dim_tempo dt ON cast(ffa.cod_tempo as varchar) = cast(dt.id_dim_tempo as varchar)
```

---

## 5. Padrão correto para "último registro de uma entidade"

**Use `ROW_NUMBER()` + filtro `rn = 1`.** Não use subquery correlacionada com `LIMIT 1`.

```sql
-- Padrão genérico — substitua [schema], [tabela], [chave] e [campo_data] pelos valores reais
WITH registros_deduplicados AS (
    SELECT [colunas_necessarias]
    FROM (
        SELECT
            t.*,
            row_number() OVER (
                PARTITION BY t.[chave_unica]
                ORDER BY coalesce(t.[campo_data], timestamp '1900-01-01 00:00:00') DESC
            ) AS rn
        FROM [schema].[tabela] t
        WHERE t.[chave_unica] IS NOT NULL
    ) x
    WHERE rn = 1
)
```

Exemplo aplicado (domínio RH — adapte para seu contexto):

```sql
WITH dim_entidade_deduplicada AS (
    SELECT cod_entidade, chave_negocio, nom_entidade
    FROM (
        SELECT
            t.cod_entidade,
            t.chave_negocio,
            t.nom_entidade,
            t.dat_inclusao_registro,
            row_number() OVER (
                PARTITION BY t.cod_entidade, t.chave_negocio
                ORDER BY coalesce(t.dat_inclusao_registro, timestamp '1900-01-01 00:00:00') DESC
            ) AS rn
        FROM [schema].[tabela_dimensao] t
        WHERE t.chave_negocio IS NOT NULL
    ) x
    WHERE rn = 1
)
```

---

## 6. Classificação hierárquica em cascata

Quando o DENA pede mapeamento de domínio com regras de prioridade (ex: consenso → box, função → categoria de cargo), use **CASE em cascata** com a ordem certa:

1. **Exclusões** primeiro (NAO INFORMADO, APRENDIZ, ESTAGIÁRIO)
2. **Regras específicas** (códigos de função pontuais)
3. **Regras estruturais** (níveis hierárquicos N1/N2/N3)
4. **Regras por grupo** (campo `nom_grupo_cargos`)
5. **Fallbacks por padrão textual** (LIKE em `nom_funcao`)
6. **Else final**

A ordem importa — regra mais específica vem primeiro.

---

## 7. Quando inferir engine pelo schema destino (regra geral)

- Destino `business_*` (sem prefixo `cdl_`) → **Redshift** (default razoável)
- Destino `cdl_*_business` → pode ser **Athena ou Redshift** (perguntar)
- Destino `dp_*_trusted` → **Athena** (default razoável)
- Destino `gold_*` → perguntar

Se inferir, sempre **explicitar a inferência** ao DPO: *"Vou gerar pra Redshift por causa do destino `business_gec`. Se for Athena, me avisa."*
