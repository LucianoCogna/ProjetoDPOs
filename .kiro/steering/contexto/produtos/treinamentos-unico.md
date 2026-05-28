---
inclusion: manual
---

# Produto — Treinamentos UniCo (DAPL-543 / DAPL-550)

> **Quando usar:** chame com `#treinamentos-unico` ao trabalhar em qualquer demanda que envolva dados de treinamento da plataforma UniCo/Moodle.

---

## Visão geral

- **Épico pai:** DAPL-543 — G&C Treinamento com treinamento presencial
- **Card de refinamento:** DAPL-550
- **Schema destino:** `business_gec`
- **Engine:** Redshift
- **Status:** Queries geradas — em validação
- **Responsável técnico:** Luciano De Oliveira Santos

---

## Tabelas confirmadas

| Tabela | Schema | Status | Observação |
|---|---|---|---|
| `unico_moodle_mdl_user` | `dp_gente_cultura_trusted` | ✅ confirmada | Usuários Moodle |
| `unico_moodle_int_colaborador` | `dp_gente_cultura_trusted` | ✅ confirmada | Join UniCo ↔ RM via CPF |
| `unico_moodle_mdl_course` | `dp_gente_cultura_trusted` | ✅ confirmada | Catálogo de cursos |
| `unico_moodle_mdl_tool_certificate_issues` | `dp_gente_cultura_trusted` | ✅ confirmada | Certificações emitidas |
| `unico_moodle_mdl_customfield_data` | `dp_gente_cultura_trusted` | ✅ confirmada | fieldid=7 = carga horária |
| `unico_moodle_mdl_course_categories` | `dp_gente_cultura_trusted` | ❌ não ingerida | Não usar |
| `unico_moodle_mdl_course_completions` | `dp_gente_cultura_trusted` | ❌ não confirmada | Não usar |
| `tb_vw_treinamento_unico` | `business_gec` | ✅ confirmada | Ind. 10 entregue — não refazer |
| `fat_funcionario_analitico` | `cdl_gente_business` | ✅ confirmada | Quebras VP/Dir/tempo — usar versão `cdl_gente_business`, não `business_gec` |
| `dim_vp` | `cdl_gente_business` | ✅ confirmada | Quebra VP |
| `dim_diretor` | `cdl_gente_business` | ✅ confirmada | Quebra Diretoria |
| `dim_tempo` | `cdl_gente_business` | ✅ confirmada | MoM / YOY |

---

## Decisões técnicas confirmadas

- **Carga horária (ind. 2):** `unico_moodle_mdl_customfield_data`, `fieldid = 7`
- **Trilhas (ind. 7):** `mdl_course.category = 3` identifica cursos de trilha
- **Analítico de conclusão (ind. 8):** usar `certificate_issues`, não `course_completions`
- **Pendências (ind. 10):** ✅ **entregue via `business_gec.tb_vw_treinamento_unico`** — confirmado na reunião de 27/05. Não refazer.
- **Regional:** campo amarrado à coligada no RM — já está no `fat_funcionario_analitico`
- **timecreated:** epoch em segundos. Conversão Redshift: `dateadd(second, timecreated, timestamp '1970-01-01 00:00:00')`
- **Tipo de usuário:** puxar interno e externo. Usuários externos entram via Prisma — Ricardo pode ajudar a identificar a marcação no Moodle
  - Report de área (quem fez / pendente) → filtrar só internos
  - Utilização da plataforma → mostrar todos
- **Ranking de usuários (ind. 5):** não entregar o ranking em si — entregar as informações que o compõem (qtd de cursos por pessoa, no mês e no ano). Visual de ranqueamento fica no PBI
  - Escopo: considerar **todos os cursos** (obrigatórios + livres)
- **Analítico nominal:** toda view de gente precisa permitir chegar no analítico por pessoa. Exceção: indicadores de ESG seguem só as premissas do reporte

---

## Indicadores e status das queries

| # | Indicador | Status | Observação |
|---|---|---|---|
| 1 | Certificações emitidas | ✅ query gerada | |
| 2 | Horas de capacitação | ✅ query gerada | fieldid=7 confirmado |
| 3 | Taxa de pessoas certificadas | ✅ query gerada | |
| 4 | Qtd pessoas certificadas | ✅ query gerada | |
| 5 | Ranking usuários | ✅ query gerada | Entregar qtd cursos/pessoa (mês e ano) — ranking visual fica no PBI |
| 6 | Ranking cursos | ✅ query gerada | |
| 7 | Conclusão de trilhas | ✅ query gerada (rev.) | category=3 confirmado |
| 8 | Analítico de conclusão | ✅ query gerada | |
| 9 | Certificações por estrutura | ⚠️ query gerada sem filtro | lista de IDs obrigatórios pendente com área |
| 10 | Analítico de pendências | ✅ entregue | `tb_vw_treinamento_unico` — confirmado na reunião 27/05, não refazer |

---

## Lacunas em aberto

1. **Ind. 9 — Lista de cursos obrigatórios:** área ainda não forneceu os IDs. Descomentar `AND cert.courseid IN (...)` quando lista chegar.
2. **timecreated:** confirmar com Ricardo/Plataforma se é epoch em segundos. Se datas saírem erradas, trocar por `cast(cert.timecreated as timestamp)`.
3. **Ind. 7 — Definição de trilha concluída:** assumido "certificado em todos os cursos de category=3". Validar com área.
4. **Queries de referência do desenvolvedor:** DPO coletando queries do dev responsável — em andamento.
5. **Ind. 5 — Regra do ranking:** Alexandre disse "considera tudo", mas levantou a dúvida de excluir os obrigatórios. Confirmar com área antes de fechar a query.
6. **Treinamento presencial (DAPL-543):** feature já construída na UniCo — cursos presenciais aparecem como cursos normais na plataforma. Confirmar com **Ricardo** se ficam numa tabela separada ou junto com os demais cursos no Moodle.
7. **Tipo de usuário externo:** confirmar com **Ricardo** como identificar usuários externos (Prisma) nas tabelas do Moodle.
