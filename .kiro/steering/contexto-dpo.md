---
inclusion: manual
---

# Contexto do DPO — Gente & Cultura | Cogna

> **Quando usar:** chame este arquivo com `#contexto-dpo` no início de uma sessão nova quando precisar que o agente já saiba o contexto da sua área sem reexplicar do zero.

---

## Quem sou eu

- **Papel:** Data Product Owner (DPO)
- **Frentes de atuação:** Indicadores de Funcionários, ESG e Financeiro
- **Domínio no Lake:** `gente_cultura`
- **Repositório de catálogo:** `datax-gcc` (e `gcc-v1` para fontes legadas)

---

## Fontes de dados que trabalho

| Sistema | O que contém | Versão no repo |
|---|---|---|
| Capital Humano (RM) | Estrutura organizacional, funcionários, cargos, admissões, demissões, folha | `capital_humano/` (v2) + `hestrutura/` |
| Capital Humano v1 | Estrutura legada (hestrutura v1) | `capital_humano_v1_hestrutura` no trusted |
| Blend | Pesquisas de engajamento (Pulso, Engajamento), favorabilidade LGBTQIA+ | `blend/` |
| Elofy | Avaliações de desempenho, Liga de Gente, feedbacks, PDIs | `elofy/` |
| Gupy | Recrutamento e seleção, vagas, candidatos, Valoriza | `gupy/` |
| UniCo (Moodle) | Treinamentos, certificações, trilhas, cursos obrigatórios | Sem pasta própria no repo — tabelas ingeridas diretamente em `dp_gente_cultura_trusted` com prefixo `unico_moodle_*` |
| Service Now | Desligamentos voluntários (motivos de turnover) | `service_now/` |
| Hyperion | Orçamento (valores orçados) | [? confirmar pasta no repo] |

### Tabelas-chave que já conheço

| Tabela | Schema | O que é |
|---|---|---|
| `capital_humano_rh_prd_hst_estrutura` | `dp_gente_cultura_trusted` | Histórico de estrutura organizacional v2 (fonte principal para hierarquia) |
| `capital_humano_v1_hestrutura` | `dp_gente_cultura_trusted` | Estrutura organizacional v1 (legada, complementar) |
| `tb_rh_hestrutura` | `business_gec` | Snapshot atual da estrutura (sem histórico — limitação conhecida) |
| `dim_ch_hst_estrutura` | `business_gec` | Dimensão de histórico de estrutura processada |
| `dim_ch_estrutura` | `business_gec` | Dimensão de estrutura vigente |
| `unico_moodle_mdl_user` | `dp_gente_cultura_trusted` | Usuários cadastrados na plataforma UniCo/Moodle |
| `unico_moodle_int_colaborador` | `dp_gente_cultura_trusted` | Integração UniCo ↔ RM — tabela central de join. Campos: `funccpf` (CPF), `funcnome`, `funcemailcorp`, `funcfuncaonome`, `funcfuncaocod`, `funcsituacao` |
| `unico_moodle_mdl_course` | `dp_gente_cultura_trusted` | Catálogo de cursos disponíveis na plataforma UniCo |
| `unico_moodle_mdl_tool_certificate_issues` | `dp_gente_cultura_trusted` | Certificações emitidas — 1 registro por (usuário × curso certificado). Chave: `userid` + `courseid` |
| `tb_vw_treinamento_unico` | `business_gec` | View processada de treinamento UniCo — cruza usuário Moodle com dados do colaborador e flag de conclusão por curso. Referência: `views/query/vw_treinamento_unico.sql` |
| `dim_ch2_hist_estrutura_n1` a `n7` | `business_gec` | Hierarquia histórica por nível (N1=VP … N7) — usadas para quebras de VP/Diretoria/Regional nos indicadores |
| `fat_funcionario_analitico` ⚠️ DUAS VERSÕES | ver abaixo | **Atenção: existem duas tabelas com o mesmo nome.** (1) `business_gec.fat_funcionario_analitico` — versão simples em `people/`, apenas 7 colunas (id, cd_funcionario, ds_cargo, vl_salario, dt_admissao, dt_base, dt_execucao), **sem CPF**. (2) `cdl_gente_business.fat_funcionario_analitico` — versão completa, com `num_cpf`, `cod_estrutura_n1`, `cod_tempo`, etc., usada em todas as queries do repo. **✅ Para DAPL-550 usar: `cdl_gente_business.fat_funcionario_analitico`** |
| [? completar com tabelas de ESG e Financeiro] | | |

---

## Backlog de demandas — Prioridade 2026

> Prazo: final de julho de 2026. Ordenado por prioridade (1 = mais alta).

### Prioridade 1

| Frente | Indicador | Status | Sistemas Origem | Observação |
|---|---|---|---|---|
| Health Metrics | Engajamento | Concluído | Blend | Pesquisas Pulso/Engajamento — favorabilidades, eNPS, lNPS |
| Health Metrics | Turnover | Concluído | Service Now / Manual | Desligamentos voluntários por motivo |
| Health Metrics | Diversidade | Concluído | Blend / Manual | Favorabilidade LGBTQIA+ |
| ESG / Deep ESG | Colaboradores por raça/cor, gênero, PCD | Em desenvolvimento (divergência) | RM / Manual | Integração Deep ESG |
| ESG / Deep ESG | GRI 401-1 — Taxa novos empregados e rotatividade | Em desenvolvimento (divergência) | RM / Manual | Integração Deep ESG |
| ESG / Deep ESG | GRI 401-3 — Licença maternidade e paternidade | [? status] | RM / Manual | Integração Deep ESG |
| ESG / Deep ESG | GRI 404-1 — Média horas treinamento/ano | Concluído | RM / Manual | Integração Deep ESG |
| ESG / Deep ESG | GRI 405-2 — Razão salário mulheres/homens | Em desenvolvimento (Engenharia) | RM / Manual | Integração Deep ESG |
| ESG / Deep ESG | GRI 202-1 — Proporção salário mais baixo / mínimo por gênero | Concluído | RM / Manual | Integração Deep ESG |
| ESG / Deep ESG | eNPS Colaboradores | Concluído | RM / Manual | Integração Deep ESG |
| ESG / Deep ESG | Diversidade — % mulheres em gestão (todos os níveis) | Em homologação | RM / Manual | Coordenação para cima |
| ESG / Deep ESG | Diversidade — % mulheres em gestão júnior | Em homologação | RM / Manual | Apenas coordenação |
| ESG / Deep ESG | Diversidade — % mulheres em gestão de topo | Em homologação | RM / Manual | Apenas COMEX |
| ESG / Deep ESG | Diversidade — % mulheres em cargos STEM | Em homologação | RM / Manual | Apenas VP de Tecnologia |
| ESG / Deep ESG | Diversidade — % mulheres em funções geradoras de receita | Em homologação | RM / Manual | Por estrutura N1 |
| ESG / Deep ESG | Novas contratações — faixa etária | [? status] | RM / Manual | <30, 30-50, >50 anos |
| ESG / Deep ESG | Novas contratações — gênero | [? status] | RM / Manual | |
| ESG / Deep ESG | Novas contratações — nível de gerenciamento | [? status] | RM / Manual | Coordenação para cima |
| ESG / Deep ESG | Novas contratações — nacionalidade | [? status] | RM / Manual | Solicitado por região |
| ESG / Deep ESG | Bolsas de estudo — bolsas ativas | Em desenvolvimento (Refinamento) | RM / Manual | Graduação e pós |
| ESG / Deep ESG | Bolsas de estudo — colaboradores formados | Em desenvolvimento (Refinamento) | RM / Manual | Graduação e pós |
| ESG / Deep ESG | Metas diversidade — equidade liderança 50% | Em desenvolvimento (Refinamento) | RM / Manual | Meta 2025 |
| ESG / Deep ESG | Metas diversidade — 40% posições negros/pardos | Em desenvolvimento (Refinamento) | RM / Manual | Meta 2025 |
| ESG / Deep ESG | Metas diversidade — 40% liderança negros/pardos | Em desenvolvimento (Refinamento) | RM / Manual | Meta 2025 |
| ESG / Deep ESG | Metas diversidade — favorabilidade LGBTQIA+ ≥ 93 | Em desenvolvimento (Refinamento) | Blend | Meta 2025 |
| ESG / Deep ESG | Metas diversidade — 100% liderança capacitada em diversidade | Em desenvolvimento (Refinamento) | UniCo | Meta 2025 |
| ESG / Deep ESG | GRI 2-7/2-8 — Colaboradores e contratação por região, faixa etária, função | Homologado | RM / Manual | |
| Gestão de Desempenho | Colaboradores avaliados | Em desenvolvimento (Refinamento) | Elofy / RM | Revisar painel |
| Gestão de Desempenho | Avaliações de Liga | Em desenvolvimento (Refinamento) | Elofy / RM | Por consenso, raça, PCD, YoY |
| Gestão de Desempenho | Feedbacks | Em desenvolvimento (Refinamento) | Elofy / RM | Total enviados e recebidos |
| Gestão de Desempenho | PDIs | Em desenvolvimento (Refinamento) | Elofy / RM | Total cadastrados |
| Gestão de Desempenho | Pessoas no L Superior | Em desenvolvimento (Refinamento) | Elofy / RM | Box L Superior (talento) |
| Gestão de Desempenho | Pessoas no L Inferior | Em desenvolvimento (Refinamento) | Elofy / RM | Box L Inferior (atenção) |
| Gestão de Desempenho | Avaliação de competências | Em desenvolvimento (Refinamento) | Elofy / RM | Média por competência |
| Treinamento | Cursos pendentes | Homologado | UniCo / RM | Integração Service Now para cobrança |
| Geral | Tempo de casa | Em homologação | RM | Tempo sequencial independente de múltiplos vínculos |
| Geral | Tempo na função | Em homologação | RM | Tempo no cargo atual |
| Orçamento | Realizado | Homologado | RM / Hyperion / Capital Humano | VP, diretoria, regional, CAPEX, OPEX |
| Orçamento | Orçado | Homologado | RM / Hyperion / Capital Humano | VP, diretoria, regional, CAPEX, OPEX |
| Orçamento | Real vs Orçado | Em desenvolvimento | RM / Hyperion / Capital Humano | |
| Orçamento | Forecast | [? status] | RM / Hyperion / Capital Humano | |
| Orçamento | Real vs Forecast | [? status] | RM / Hyperion / Capital Humano | |
| Orçamento | Folha / ROL | [? status] | RM / Hyperion / Capital Humano | |

### Prioridade 3

| Frente | Indicador | Status | Sistemas Origem | Observação |
|---|---|---|---|---|
| Treinamento | Horas de capacitação | Concluído | Gupy | |
| Treinamento | Certificações emitidas (total) | Backlog (revisar painel) | UniCo / RM | DAPL-550 — épico pai DAPL-543 |
| Treinamento | Taxa de pessoas certificadas | Backlog (revisar painel) | UniCo / RM | DAPL-550 |
| Treinamento | Quantidade de pessoas certificadas | Backlog (revisar painel) | UniCo / RM | DAPL-550 |
| Treinamento | Ranking usuários | Backlog (revisar painel) | UniCo / RM | DAPL-550 |
| Treinamento | Ranking cursos | Backlog (revisar painel) | UniCo / RM | DAPL-550 |
| Treinamento | Conclusão de trilhas | Backlog (revisar painel) | UniCo / RM | DAPL-550 |
| Treinamento | Analítico de Conclusão | Backlog (revisar painel) | UniCo / RM | DAPL-550 — dados gerais de curso e colaborador |
| Treinamento | Certificações por estrutura | Backlog (revisar painel) | UniCo / RM | DAPL-550 — % conclusão por VP e Diretoria |
| Treinamento | Analítico de certificações pendentes | Backlog (revisar painel) | UniCo / RM | DAPL-550 — cursos obrigatórios pendentes |

**Observação DAPL-550:** Card de refinamento vinculado ao épico DAPL-543 (*G&C - Treinamento com treinamento presencial*). Todos os indicadores são de **revisão de painel existente**, prioridade Alta, fonte UniCo / RM. Quebras comuns: tipo de usuário (colaborador ou externo), VP, Diretoria, Regional, MoM, YOY. Sprint SP 26.2.4 (25/05–05/06/2026). Responsável: Luciano De Oliveira Santos.

### Prioridade 4

| Frente | Indicador | Status | Sistemas Origem |
|---|---|---|---|
| Health Metrics / Recrutamento e Seleção | Gestão de Vagas (total, abertas, rascunho, canceladas, admissão, encerradas) | Em desenvolvimento (Ingestão) | Gupy / RM |
| Recrutamento e Seleção | SLA por etapa do processo | Em desenvolvimento (Ingestão) | Gupy / RM |
| Recrutamento e Seleção | Candidatos por vaga / funil | Em desenvolvimento (Ingestão) | Gupy / RM |
| Recrutamento e Seleção | Vagas afirmativas (PCD, Racial, Gênero) | Em desenvolvimento (Ingestão) | Gupy / RM |
| Recrutamento e Seleção | Contratados por source / função | Em desenvolvimento (Ingestão) | Gupy / RM |
| Recrutamento e Seleção | Valoriza (vagas, entrevistados, contratados) | Em desenvolvimento (Ingestão) | Gupy / RM |
| Recrutamento e Seleção | Migração de contrato | Em desenvolvimento (Ingestão) | Gupy / RM |

## Projetos e demandas em andamento

### Revisão do Produto de Dados de Treinamento UniCo — DAPL-543
- **Status:** Backlog (refinamento concluído, aguardando desenvolvimento) — Sprint SP 26.2.4
- **Épico pai:** DAPL-543 | **Card de refinamento:** DAPL-550
- **Tipo:** 🆕 Novo Produto de Dados — criação de views analíticas novas (não alteração do produto existente `tb_vw_treinamento_unico`, que serve apenas como referência)
- **Stakeholders:** Karla Ynonye Zucchini (DPO/reportante), Luciano De Oliveira Santos (responsável técnico)
- **Escopo:** Criar views em `business_gec` para 10 indicadores de treinamento UniCo. Join principal: `unico_moodle_int_colaborador.funccpf = cdl_gente_business.fat_funcionario_analitico.num_cpf`. Quebras por VP (`cod_vp → dim_vp`), Diretoria (`cod_dir → dim_diretor`), Regional, tipo de usuário, MoM (`cod_tempo → dim_tempo`), YOY.
- **Indicadores:**
  1. Certificações emitidas — fonte: `unico_moodle_mdl_tool_certificate_issues`
  2. Horas de capacitação — [? confirmar campo de carga horária em `unico_moodle_mdl_course`]
  3. Taxa de pessoas certificadas — % sobre colaboradores ativos (denominador = `qtd_cpf_ativo > 0`)
  4. Quantidade de pessoas certificadas — COUNT DISTINCT por CPF
  5. Ranking usuários — certificados por colaborador
  6. Ranking cursos — certificações por curso
  7. Conclusão de trilhas — [? confirmar estrutura de trilhas: `unico_moodle_mdl_course_categories`?]
  8. Analítico de conclusão — listagem curso × colaborador certificado
  9. Certificações por estrutura — % conclusão de cursos específicos por VP/Diretoria
  10. Analítico de pendências — [? depende de `unico_moodle_mdl_course_completions` — confirmar se ingerida]
- **Pendências em aberto:**
  - Confirmar se treinamento presencial entra neste épico ou é fase separada
  - Confirmar campo de carga horária no Moodle (indicador 2)
  - Confirmar estrutura de trilhas no Moodle (indicador 7)
  - Confirmar disponibilidade de `unico_moodle_mdl_course_completions` no trusted (indicador 10)
  - Confirmar frequência de atualização (presume-se D-1)
  - Identificar contato técnico responsável pela ingestão UniCo na Plataforma

### Histórico de Estrutura Organizacional
- **Status:** Em desenvolvimento
- **Problema:** A tabela atual (`tb_rh_hestrutura`) só carrega o snapshot mais recente, sem histórico real. Dois problemas identificados: (1) descrição muda mas código não — hoje propaga a última descrição para todo histórico; (2) mesmo código com múltiplos pais no mesmo dia — gera duplicação ao montar a árvore hierárquica N1→N6.
- **Solução em curso:** View com UNION ALL das fontes v1 + v2 (`capital_humano_v1_hestrutura` + `capital_humano_rh_prd_hst_estrutura`), deduplicação por `ROW_NUMBER` por `(idestrutura, competência)`, normalização de descrição e preenchimento de competências fantasmas.
- **Impacto secundário:** Grupo de segurança do Power BI depende da estrutura correta para controle de acesso por N2/diretoria.
- **Raiz do problema (fora do escopo de dados):** Capital Humano não tem trava para impedir múltiplos registros da mesma estrutura no mesmo dia — precisa ser resolvido no sistema de origem.
- **Stakeholders:** Luciano (analista/DPO da demanda), Paulo Denilson (engenharia — autor da lógica de normalização), Alexandre/Alê (time de arquitetura — agenda pendente para alinhar grupo de segurança)
- **Decisões técnicas tomadas:**
  - Hierarquia montada via campo `codigo` (string hierárquica tipo `00.09.00.12.02`) — mais confiável que `idestruturapai`
  - Descrição: propaga sempre o **último nome conhecido** para todo o histórico do código — alinhado com time de orçamento, comportamento esperado
  - Deduplicação: `ROW_NUMBER` por `(idestrutura, competência)` ordenado por `sysdatainicio DESC` + hora para desempate
  - Competências fantasmas: criadas via `DATEDIFF` entre última data de lançamento e data atual (`current_date`) para cobrir meses sem registro na estrutura mas com lançamentos no RM
- **Próximo passo:** agendar com Alexandre (arquitetura) para alinhar impacto no grupo de segurança do PBI
- **Épico DAPL:** [? criar]

---

## Contexto técnico do ambiente

- **Engines:** Athena (Presto/Trino) para `dp_*_trusted` | Redshift para `business_gec`
- **Hierarquia organizacional:** montada via campo `codigo` (ex: `00.09.00.12.02`) — mais confiável que `idestruturapai` que pode vir sujo
- **Competência:** campo derivado de `TRUNC(sysdatainicio, 'MM')` — usado para agrupar registros por mês/ano e evitar ambiguidade de múltiplos registros no mesmo dia
- **Data sentinel:** datas "nulas" representadas como `1900-01-01` no DW — nunca filtrar por `IS NULL` em campos de data
- **Grupo de segurança PBI:** usa código de estrutura (sessão) para controle de acesso linha a linha. Cruzamento: e-mail logado no PBI → base de estrutura → código de sessão → filtra o que a pessoa pode ver. Hoje funciona só para N1 (VP), chumbado na mão via regra de contagem de caracteres do código. Do N2 para baixo não funciona por causa da sujeira na tabela de estrutura.
- **Scripts de referência no repo:**
  - `gente_cultura/estrutura_unificada.sql` — rascunho do UNION v1+v2 (tem filtro `WHERE idestrutura = 8828` — apenas teste)
  - `gente_cultura/estrutura_ancestrais.sql` — lógica de ancestrais via campo `codigo` (UNION v1+v2 + ROW_NUMBER)
  - `gente_cultura/estrutura_descendentes.sql` — lógica de descendentes via campo `codigo`
  - `hestrutura/query/dim_ch_estrutura.sql` — pipeline atual: carrega só snapshot mais recente (origem: `cdl_gente_raw.rh_hestrutura`)
  - `views/query/vw_treinamento_unico.sql` — join UniCo × RM em produção: `mdl_user` → `int_colaborador` (via `idnumber = funccpf`) → CROSS JOIN `mdl_course` → LEFT JOIN `mdl_tool_certificate_issues`

**Padrão de join UniCo ↔ RM:**
- Chave de integração: **CPF** (`funccpf` na tabela `unico_moodle_int_colaborador`)
- Join direto: `unico_moodle_mdl_user.idnumber = unico_moodle_int_colaborador.funccpf`
- A `int_colaborador` já traz os campos básicos do RM (nome, email corp, função, situação) — não precisa de join adicional com `capital_humano_rh_prd_funcionario` para dados cadastrais
- **Para indicadores do DAPL-550:** join é `unico_moodle_int_colaborador.funccpf = cdl_gente_business.fat_funcionario_analitico.num_cpf`
- Para quebras por VP/Diretoria/Regional: `fat_funcionario_analitico.cod_vp` → `dim_vp` | `cod_dir` → `dim_diretor` | `cod_tempo` → `dim_tempo` (MoM/YOY)
- Filtros padrão estabelecidos em produção: excluir `username IN ('guest','aluna','admin','aluno')`, manter `funcsituacao IN ('Ativo','Férias')`, excluir tutores e docentes (`funcfuncaonome LIKE '%tutor%'` ou `'%docente%'`)
- **Limitação conhecida:** `unico_moodle_mdl_tool_certificate_issues` registra apenas certificações **emitidas**. Para indicador de pendências (cursos obrigatórios não concluídos), pode ser necessária tabela `unico_moodle_mdl_course_completions` — confirmar com Plataforma se já está ingerida

**Colunas relevantes da `cdl_gente_business.fat_funcionario_analitico`** (confirmadas pelas queries em produção):
- `num_cpf` — chave de join com UniCo
- `cod_funcionario` — chave para joins com dims
- `cod_tempo` — join com `dim_tempo` → ano/mês para MoM/YOY
- `cod_funcao_seq` — join com `dim_funcao` → nome da função
- `cod_funcao_cargo` — join com `dim_funcao_cargo` → grupo de cargos
- `cod_situacao_funcionario` — join com `dim_situacao_funcionario`
- `cod_vp` — join direto com `dim_vp` → quebra por VP
- `cod_dir` — join com `dim_diretor` → quebra por Diretoria
- `qtd_cpf_ativo` — flag de ativo no mês (filtrar `> 0`)
- `cs`, `n1`, `n2`, `n3` — níveis hierárquicos da estrutura
- `dat_admissao`, `dat_demissao` — datas com sentinel `1900-01-01`

---

## Glossário da área

| Termo | Significado |
|---|---|
| Competência | Mês/ano de referência de um registro (derivado de sysdatainicio) |
| Competência fantasma | Competência criada artificialmente para preencher lacunas de meses onde a estrutura sumiu da tabela mas ainda tem lançamentos no RM |
| Estrutura pai (IDPai) | Código do nó hierárquico acima — campo problemático quando há múltiplos registros no mesmo dia |
| Liga de Gente | Processo de avaliação de desempenho da Cogna — colaboradores são avaliados e alocados em boxes (L Superior = talento, L Inferior = atenção, Sólido = intermediário) com base em consenso entre gestores |
| PRV | [? completar — programa de remuneração variável] |
| N1 a N6 | Níveis hierárquicos da estrutura organizacional (N1 = VP, descendo até coordenação/analista) |
| GRI | Global Reporting Initiative — padrão internacional de relatório de sustentabilidade. Indicadores GRI 401, 404, 405 etc. são metas de ESG que a Cogna precisa reportar |
| Deep ESG | Plataforma externa onde a Cogna publica seus indicadores de ESG para o mercado — requer integração com o Lake |
| Valoriza | Programa interno de recrutamento/seleção de colaboradores para vagas internas |
| Consenso | Resultado da avaliação de Liga de Gente — valores 1 a 9 mapeados em boxes: Atenção (1,4), Sólido (2,3,5,7), Talento (6,8,9) |
| L Superior | Box de talento na Liga de Gente (consenso 8, 6 ou 9) |
| L Inferior | Box de atenção na Liga de Gente (consenso 1 ou 4) |
| COMEX | Comitê Executivo da Cogna — nível mais alto da hierarquia |
| CAPEX / OPEX | Capital Expenditure / Operational Expenditure — classificação de gastos no orçamento |
| RM | Sistema de origem do Capital Humano (Totvs RM) — onde ficam os lançamentos de pagamento e estrutura |
| Código de sessão | Código que identifica a estrutura organizacional de uma pessoa no RM — usado como chave no grupo de segurança do PBI |
| Snapshot | Foto do estado atual de uma tabela — sem histórico de alterações |

---

## O que NÃO está no meu escopo

- Dados de captação de alunos
- Dados acadêmicos (matrículas, evasão, desempenho de alunos)
- Financeiro corporativo fora do contexto de folha/orçamento de pessoas
- [? confirmar outros limites com o DPO]

## Como atualizar este arquivo

Sempre que uma nova demanda for refinada ou um novo contexto relevante surgir, atualize as seções:
- **Projetos em andamento** — adicione o novo épico com status e stakeholders
- **Tabelas-chave** — adicione tabelas novas que entraram no radar
- **Glossário** — adicione termos novos que surgirem nas calls

Os campos `[? completar]` são lacunas intencionais — preencha conforme o contexto for ficando mais claro.
