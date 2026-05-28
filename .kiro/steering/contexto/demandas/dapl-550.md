---
inclusion: manual
---

# Contexto de Sessão — DAPL-550 Treinamentos UniCo

> **Como usar:** chame com `#dapl-550` para retomar o contexto operacional desta demanda. Para o contexto completo do produto, use também `#treinamentos-unico`.

**Última atualização:** 2026-05-27
**Status atual:** DENA-813 preenchido. View analítica especificada. Aguardando validação de 6 pontos técnicos antes do dev finalizar.

---

## O que já foi discutido

- Mapeamento completo das tabelas disponíveis no trusted para UniCo
- Confirmação de `fieldid=7` para carga horária com Ricardo
- Confirmação de `category=3` para trilhas
- Decisão de usar `certificate_issues` (não `course_completions`) para analítico de conclusão
- Ind. 10 entregue via `business_gec.tb_vw_treinamento_unico` — confirmado na reunião de 27/05, não refazer
- Geração das 10 queries de indicadores
- Reunião 27/05 (ESG): alinhamento sobre tipo de usuário, escopo do ranking (ind. 5) e treinamento presencial
- DENA-813 preenchido com estratégia de 1 view analítica base + indicadores derivados no PBI

---

## Decisões tomadas

- `unico_moodle_mdl_course_categories` — não ingerida, não usar
- `unico_moodle_mdl_course_completions` — não confirmada, não usar
- `timecreated` tratado como epoch em segundos: `dateadd(second, timecreated, timestamp '1970-01-01 00:00:00')`
- Regional vem do RM, campo amarrado à coligada — disponível em `aux_ch2_funcionario.idfilial` → `dim_ch_filial`
- **Estratégia de entrega:** 1 view analítica base (`vw_treinamento_unico_analitico` em `business_gec`) — todos os indicadores derivados no PBI. Sem view por indicador.
- **Tipo de usuário:** puxar interno e externo. Report de área → filtrar só internos. Utilização da plataforma → mostrar todos. Lógica: `tipo_usuario = 'externo'` quando `funccpf IS NULL`
- **Ind. 5 — Ranking:** entregar qtd de cursos por pessoa (mês e ano), não o ranking visual — ranking fica no PBI. Escopo: todos os cursos (obrigatórios + livres)
- **Analítico nominal:** toda view de gente deve permitir drill-down por pessoa. Exceção: indicadores ESG
- **Trilhas (ind. 7):** `mdl_course.category = 3` → `flag_trilha = 'S'`
- **Carga horária (ind. 2):** parser de formatos variados (`HH:MM`, `Xh`, `X min`, número puro) → resultado em minutos em `carga_horaria_minutos`

---

## DENA-813 — Estrutura da view

**View:** `business_gec.vw_treinamento_unico_analitico`
**Engine:** Redshift | **Frequência:** D-1

| Coluna | Tipo | Descrição |
|---|---|---|
| userid | VARCHAR | ID do usuário no Moodle |
| username | VARCHAR | Username no Moodle |
| tipo_usuario | VARCHAR | 'interno' ou 'externo' |
| funccpf | VARCHAR | CPF (NULL para externos) |
| funcnome | VARCHAR | Nome do colaborador |
| funcemailcorp | VARCHAR | E-mail corporativo |
| funcfuncaonome | VARCHAR | Nome da função |
| funcfuncaocod | VARCHAR | Código da função |
| funcsituacao | VARCHAR | Situação no RM |
| courseid | BIGINT | ID do curso no Moodle |
| nom_curso | VARCHAR | Nome do curso |
| flag_trilha | VARCHAR | 'S' se category=3 |
| carga_horaria_minutos | DOUBLE PRECISION | Carga horária em minutos (fieldid=7) |
| flag_concluido | VARCHAR | 'S' se certificação emitida |
| dat_certificacao | TIMESTAMP | Data da certificação (epoch convertido) |
| ano_certificacao | VARCHAR | Ano (para YOY) |
| mes_certificacao | VARCHAR | Mês com zero à esquerda (para MoM) |
| dsc_estrutura_n1 | VARCHAR | VP (N1) |
| dsc_estrutura_n2 | VARCHAR | Diretoria (N2) |
| nom_regional | VARCHAR | Regional via filial do RM |
| [? FALTA] | VARCHAR | Flag de usuário externo Prisma |

---

## Lacunas em aberto

1. **Join int_colaborador → estrutura organizacional:** não há chave direta confirmada entre `unico_moodle_int_colaborador` e `aux_ch2_funcionario`/`dim_ch2_hist_estrutura_n1`. Confirmar com Engenharia qual campo usar para obter VP e Diretoria a partir do CPF.
2. **Regional (filial):** confirmar nome da tabela e coluna em `business_gec` — candidata `dim_ch_filial`, DDL não confirmado no repo.
3. **Usuário externo (Prisma):** confirmar com Ricardo se há flag ou campo no Moodle. Se não houver, usar `funccpf IS NULL` como proxy.
4. **Cursos presenciais:** confirmar com Ricardo se aparecem em `mdl_course` ou tabela separada. Se separada, precisará de UNION.
5. **Ind. 9 — Cursos obrigatórios:** lista de `courseids` não fornecida pela área. Filtro `AND curso.id IN (...)` comentado na query, aguardando lista.
6. **timecreated:** se datas saírem erradas após deploy, testar `cast(cert.timecreated as timestamp)` como alternativa.
7. **Ind. 5 — Regra do ranking:** Alexandre disse "considera tudo", mas levantou dúvida sobre excluir obrigatórios — confirmar com área antes de fechar.

---

## Próximos passos

1. Confirmar com Ricardo: chave de join `int_colaborador → estrutura` para VP/Diretoria
2. Confirmar com Ricardo: identificação de usuários externos (Prisma) no Moodle
3. Confirmar com Ricardo: tabela de treinamentos presenciais (separada ou `mdl_course`)
4. Confirmar DDL de `dim_ch_filial` em `business_gec` para campo de regional
5. Obter lista de IDs de cursos obrigatórios para ind. 9
6. Confirmar com área regra final do ind. 5 (incluir ou excluir obrigatórios do ranking)
7. Validar conversão de `timecreated` ao rodar as queries em homologação
