---
inclusion: manual
---

# Exemplos Validados — Queries Reais em Produção

Estas queries rodam em produção e servem como **referência de estilo, padrões e estrutura SQL**. Use como modelo quando gerar SQL novo.

> ⚠️ **Contexto:** os exemplos abaixo são do domínio de RH/Gente & Cultura. Os **padrões de código são universais** — joins, CTEs, ROW_NUMBER, tratamento de datas, validação de campos. As **tabelas e schemas são específicos desse domínio** — confirme sempre no repo do catálogo aberto antes de usar em outro contexto.

---

## Exemplo 1 — Listagem com lookups em dimensões (Athena)

**Objetivo:** Listar registros de fato com joins em múltiplas dimensões, incluindo tratamento de status e data sentinel.

**Engine:** Athena (Presto/Trino)

**Domínio original:** RH/Gente & Cultura — tabelas de referência abaixo são desse domínio. Para outros domínios, confirme equivalentes no repo.

**Tabelas usadas (domínio RH — confirmar equivalente no seu domínio):**
- `cdl_gente_business.fat_funcionario_analitico`
- `cdl_gente_business.dim_funcionario`
- `cdl_gente_business.dim_situacao_funcionario`
- `cdl_gente_business.dim_tipo_demissao`
- `cdl_gente_business.dim_recebimento`
- `cdl_gente_business.dim_funcao`

**Padrões demonstrados (universais):**
- Schema qualificado entre aspas duplas em joins cross-schema
- Tratamento de data sentinel `1900-01-01` para registros sem data de encerramento
- LEFT JOIN como default para dimensões opcionais

```sql
SELECT DISTINCT
    fat.cod_coligada,
    func.cod_chapa_funcionario,
    func.nom_funcionario,
    func.num_cpf,
    func.num_pis,
    func.eml_funcionario,
    fat.cod_funcao_seq,
    funcao.cod_funcao,
    funcao.nom_funcao,
    CASE
        WHEN cast(fat.dat_demissao as date) > date '1900-01-01' THEN 'Demitido'
        ELSE sit.nom_situacao_funcionario
    END AS situacao,
    receb.dsc_recebimento,
    demissao.cod_tipo_demissao,
    demissao.tip_demissao,
    fat.dat_admissao,
    fat.dat_demissao,
    fat.dat_transferencia
FROM "cdl_gente_business"."fat_funcionario_analitico" fat
LEFT JOIN "cdl_gente_business"."dim_funcionario" func
    ON fat.cod_funcionario = func.cod_funcionario
LEFT JOIN "cdl_gente_business"."dim_situacao_funcionario" sit
    ON fat.cod_situacao_funcionario = sit.cod_situacao_funcionario
LEFT JOIN "cdl_gente_business"."dim_tipo_demissao" demissao
    ON fat.cod_tipo_demissao = demissao.cod_tipo_demissao
LEFT JOIN "cdl_gente_business"."dim_recebimento" receb
    ON fat.cod_recebimento = receb.cod_recebimento
LEFT JOIN "cdl_gente_business"."dim_funcao" funcao
    ON funcao.cod_funcao_seq = fat.cod_funcao_seq
ORDER BY fat.dat_demissao ASC, fat.dat_transferencia ASC
```

> **Observação técnica:** a versão original em produção tem uma subquery correlacionada na coluna `situacao` que apresenta comportamento incorreto (tautologia `fat.cod_funcionario = fat.cod_funcionario`). O exemplo acima foi corrigido pra usar JOIN direto. Quando precisar do "último registro do colaborador" em outras queries, **use o padrão ROW_NUMBER documentado em `padroes-sql-cogna.md`**, não subquery correlacionada com LIMIT 1.

---

## Exemplo 2 — Métricas derivadas com flags e CASE (Athena)

**Objetivo:** Histórico mensal com flags calculados por colaborador/mês.

**Engine:** Athena (Presto/Trino)

**Domínio original:** RH — substitua tabelas pelo equivalente do seu domínio.

**Padrões demonstrados (universais):**
- Aliases numerados (T00, T13, T22) — aceitável em queries muito complexas com muitos joins
- CAST AS VARCHAR em join de tipos divergentes (`cod_tempo` vs `id_dim_tempo`)
- INNER JOIN quando regra de negócio exige obrigatoriedade
- Filtro composto com NOT (...) para excluir cenários específicos
- CASE no SELECT para derivar flags a partir de campos numéricos

```sql
SELECT
    T13.num_cpf AS cpf,
    T13.cod_chapa_funcionario AS matricula,
    concat(T22.ano, '/', T22.mes) AS ano_mes,
    T22.ano,
    T22.mes,
    T00.vlr_diferenca_salario AS diferenca_vlr,
    T00.vlr_salario AS salario,
    T00.qtd_total_meritos,
    T00.qtd_total_promocoes,
    CASE WHEN T00.qtd_total_meritos = 1 THEN T00.vlr_diferenca_salario END AS valor_merito,
    CASE WHEN T00.qtd_total_promocoes = 1 THEN T00.vlr_diferenca_salario END AS valor_promocao,
    CASE WHEN upper(coalesce(T14.dsc_recebimento, '')) = 'MENSALISTA' THEN 1 ELSE 0 END AS mensalista
FROM fat_funcionario_analitico T00
LEFT JOIN dim_funcionario T13
    ON T00.cod_funcionario = T13.cod_funcionario
LEFT JOIN dim_tempo T22
    ON cast(T00.cod_tempo as varchar) = cast(T22.id_dim_tempo as varchar)
LEFT JOIN dim_funcao_cargo CARGO
    ON T00.cod_funcao_cargo = CARGO.cod_funcao_cargo
LEFT JOIN dim_vp VP
    ON T00.cod_vp = VP.cod_vp
LEFT JOIN dim_diretor DIR
    ON T00.cod_dir = DIR.cod_dir
LEFT JOIN dim_recebimento T14
    ON T00.cod_recebimento = T14.cod_recebimento
LEFT JOIN dim_nivel_hierarquico T15
    ON T15.cod_nivel_hierarquico = T00.cod_nivel_hierarquico
INNER JOIN dim_situacao_funcionario situacao
    ON T00.cod_situacao_funcionario = situacao.cod_situacao_funcionario
INNER JOIN dim_tipo_demissao tipo_demissao
    ON tipo_demissao.cod_tipo_demissao = T00.cod_tipo_demissao
INNER JOIN dim_motivo_demissao motivo_demissao
    ON motivo_demissao.cod_motivo_demissao = T00.cod_motivo_demissao
WHERE T22.ano >= '2024'
  AND NOT (
        situacao.nom_situacao_funcionario = 'Demitido'
        AND tipo_demissao.cod_tipo_demissao = 13
        AND motivo_demissao.cod_motivo_demissao = 34
        AND (T00.qtd_total_meritos = 1 OR T00.qtd_total_promocoes = 1)
  )
```

---

## Exemplo 3 — Agregação com CTEs encadeadas e múltiplas fontes (Redshift)

**Objetivo:** Calcular média de uma métrica por categoria, com classificação hierárquica e cruzamento entre sistemas.

**Engine:** Redshift

**Domínio original:** RH — tabelas `dp_gente_cultura_trusted.*` e `cdl_gente_business.*` são específicas desse domínio. Os padrões de código são universais.

**Padrões demonstrados (universais):**
- CTEs encadeadas para etapas claras (parsing → validação → agregação → classificação → resultado)
- Parsing defensivo de campo livre com regex (valores em formatos variados)
- Validação de chave de negócio (CPF) em múltiplas fontes
- Conversão epoch → timestamp (sintaxe Redshift)
- `ROW_NUMBER` para deduplicação (padrão correto — use sempre este, não subquery com LIMIT 1)
- Classificação hierárquica em cascata com prioridade explícita
- UNION ALL como "dimensão virtual" para garantir presença de categorias
- Enumeração explícita de status ativos (em vez de negação)

```sql
-- (query completa, ver arquivo original — usar como referência de estrutura CTE)
WITH carga_horaria_minutos_raw AS (
    SELECT
        data.instanceid AS courseid,
        trim(data.value) AS valor_original_carga,
        -- Parser de carga horária em formatos variados ("1:30", "1h30min", "90 min", "1.5h")
        CASE
            WHEN data.value IS NULL OR trim(data.value) = '' THEN 0
            WHEN regexp_instr(lower(trim(data.value)), '^[0-9]{1,3}:[0-9]{2}$') > 0
                THEN cast(split_part(trim(data.value), ':', 1) AS double precision) * 60
                   + cast(split_part(trim(data.value), ':', 2) AS double precision)
            -- ... outras variações
            ELSE 0
        END AS carga_horaria_minutos
    FROM dp_gente_cultura_trusted.unico_moodle_mdl_customfield_data data
    WHERE data.fieldid = 7
),
usuarios_moodle AS (
    -- Validação de CPF em múltiplas fontes
    SELECT DISTINCT
        u.id AS userid,
        CASE
            WHEN c.funccpf IS NOT NULL AND regexp_instr(c.funccpf, '^[0-9]{11}$') > 0 THEN c.funccpf
            WHEN u.idnumber IS NOT NULL AND regexp_instr(u.idnumber, '^[0-9]{11}$') > 0 THEN u.idnumber
            WHEN u.username IS NOT NULL AND regexp_instr(u.username, '^[0-9]{11}$') > 0 THEN u.username
            ELSE NULL
        END AS num_cpf
    FROM dp_gente_cultura_trusted.unico_moodle_mdl_user u
    LEFT JOIN dp_gente_cultura_trusted.unico_moodle_int_colaborador c
        ON u.idnumber = c.funccpf
    WHERE u.username NOT IN ('guest', 'aluna', 'admin', 'aluno')
),
dim_funcionario_chapas AS (
    -- Deduplicação: 1 registro mais recente por (funcionario, cpf, chapa)
    SELECT cod_funcionario, num_cpf, nom_funcionario, nom_sexo, nom_chapa_fn
    FROM (
        SELECT
            df.*,
            row_number() OVER (
                PARTITION BY df.cod_funcionario, df.num_cpf, df.nom_chapa_fn
                ORDER BY coalesce(df.dat_inclusao_registro, timestamp '1900-01-01 00:00:00') DESC
            ) AS rn
        FROM cdl_gente_business.dim_funcionario df
        WHERE df.num_cpf IS NOT NULL
          AND regexp_instr(df.num_cpf, '^[0-9]{11}$') > 0
    ) x
    WHERE rn = 1
)
-- ... seguir com CTEs de classificação hierárquica de cargos, agregação por gênero, resultado final
SELECT ... FROM media_horas_capacitacao
ORDER BY ...
```

**Sobre números hardcoded (`+ 960` ou totais fixos):** A versão original em prod tem alguns valores hardcoded (ajustes pontuais que não foram explicados no código). **Não replicar esse padrão em queries novas** — sempre calcular dinâmico. Se o DPO pedir valor fixo, ele explica o motivo e você comenta no SQL gerado.

---

## Resumo: quando usar cada estilo

| Complexidade do indicador | Estilo recomendado | Exemplo de referência |
|---|---|---|
| Listagem simples com lookups | Joins encadeados, sem CTE | Exemplo 1 |
| Métricas derivadas com flags/CASEs | Joins + CASEs no SELECT | Exemplo 2 |
| Agregação + classificação hierárquica + múltiplas fontes | CTEs encadeadas | Exemplo 3 |

Escolha o estilo proporcional à complexidade. **Não use CTE pra query que cabe em 10 linhas. Não tente fazer query de 100 linhas sem CTE.**
