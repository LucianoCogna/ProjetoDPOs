---
inclusion: manual
---

# Projeto — Gente & Cultura (G&C)

> **Quando usar:** chame com `#gcc` no início de qualquer sessão de G&C para carregar o contexto estável do projeto sem reexplicar do zero.

---

## Visão geral

- **DPO:** Luciano de Oliveira Santos
- **Domínio no Lake:** `gente_cultura`
- **Repositório principal:** `datax-gcc`
- **Repositório legado:** `gcc-v1` (alguns pipelines ainda rodam aqui — ver pendências)
- **Frentes de atuação:** Indicadores de Funcionários, Treinamento, Gestão de Desempenho, Recrutamento & Seleção, Orçamento

---

## Sistemas ingeridos

| Sistema | Pasta no repo | O que contém |
|---|---|---|
| Capital Humano (RM) v2 | `capital_humano/` | Estrutura organizacional, funcionários, cargos, admissões, demissões, folha |
| Capital Humano v1 (legado) | `hestrutura/` | Estrutura legada — complementar ao v2 |
| Blend | `blend/` | Pesquisas de engajamento (Pulso, Engajamento), favorabilidade LGBTQIA+ |
| Elofy | `elofy/` | Avaliações de desempenho, Liga de Gente, feedbacks, PDIs |
| Gupy | `gupy/` | Recrutamento e seleção, vagas, candidatos, Valoriza |
| Service Now | `service_now/` | Desligamentos voluntários (motivos de turnover) |
| UniCo (Moodle) | sem pasta própria | Tabelas ingeridas diretamente em `dp_gente_cultura_trusted` com prefixo `unico_moodle_*` |
| Hyperion | [? confirmar pasta] | Orçamento (valores orçados) |
| AESabi / SAS | [? confirmar pasta] | Dados de alunos — usado em Colaborador x Aluno |
| Platos | [? confirmar pasta] | Dados de pós-graduação — usado em Colaborador x Aluno |
| Colaborar / Olimpo | [? confirmar pasta] | Dados acadêmicos — usado em Colaborador x Aluno |
| SharePoint | [? confirmar pasta] | Lançamento manual de horas — usado em Folha de Pagamento |

---

## Schemas e engines

| Schema | Engine | Uso |
|---|---|---|
| `dp_gente_cultura_trusted` | Athena | Camada trusted — dados brutos tipados |
| `cdl_gente_business` | Athena ou Redshift | Camada business — modelagem dimensional (dims + fatos) |
| `cdl_gente_integration` | Redshift | Camada de integração — staging entre RM e business |
| `business_gec` | Redshift | Camada de consumo — views finais para PBI/ferramentas |

---

## Produtos de dados mapeados (linhagem confirmada)

| # | Produto | View / Tabela | Destino | Principais Fontes |
|---|---|---|---|---|
| 1 | Colaborador Ex Aluno Cogna | `vw_colaborador_x_aluno` | `business_gec` | RM, Capital Humano, AESabi, Platos, Colaborar, Olimpo |
| 2 | Consultor Comercial | `vw_consultor_comercial` | `business_gec` | Capital Humano (`cdl_gente_business`) |
| 3 | Engajamento & Diversidade | `vw_engajamento_diversidade` | `business_gec` | Blend (`dp_gente_cultura_trusted`) |
| 4 | ENPS Organizacional | `vw_enps_engajamento` | `business_gec` | Blend (`dp_gente_cultura_trusted`) |
| 5 | Evolução Salarial & Mérito | `vw_salarios_meritos_promocoes` | `business_gec` | Capital Humano (`cdl_gente_business`) |
| 6 | Folha de Pagamento | `vw_processamento_analitico_folha` | `business_gec` | RM (Financeiro), Capital Humano, SharePoint |
| 7 | Indicadores de Capacitação | `vw_media_horas_capacitação` | S3 (Deep ESG) | Capital Humano, `dim_cursos_realizados` ⚠️ Parcial |
| 8 | Jornada Funcional do Colaborador | `vw_tempo_de_casa_funcao` | `business_gec` | Capital Humano (`cdl_gente_business`) |
| 9 | Treinamentos Corporativos | `tb_vw_treinamento_unico` | `business_gec` | Moodle (UniCo) |
| 10 | Turnover e Motivos de Desligamento | `vw_turnover_colabs` | `business_gec` | ServiceNow (CSC) |
| 11 | Visão 360 do Colaborador | `fat_funcionario_analitico` | `cdl_gente_business` | RM, Capital Humano, Moodle/Diversidade |
| 12 | Base Analítica de Benefícios | `fat_beneficios_analitico` | `cdl_gente_business` | RM, Capital Humano (mesma base da fat_funcionario_analitico) |
| 13 | Base Analítica de Remuneração | `fat_remuneracao_analitico` | `cdl_gente_business` | RM, Capital Humano (gcc-v1) ⚠️ Parcial |

---

## Tabelas-chave

| Tabela | Schema | O que é |
|---|---|---|
| `fat_funcionario_analitico` ⚠️ DUAS VERSÕES | ver abaixo | **Atenção: existem duas tabelas com o mesmo nome.** (1) `business_gec` — versão simples, apenas 7 colunas, **sem CPF**. (2) `cdl_gente_business` — versão completa com `num_cpf`, `cod_estrutura_n1`, `cod_tempo` etc. **✅ Usar sempre: `cdl_gente_business.fat_funcionario_analitico`** |
| `fat_beneficios_analitico` | `cdl_gente_business` | Base analítica de benefícios — mesma origem da fat_funcionario_analitico |
| `fat_remuneracao_analitico` | `cdl_gente_business` | Base analítica de remuneração — SQL no gcc-v1 |
| `capital_humano_rh_prd_hst_estrutura` | `dp_gente_cultura_trusted` | Histórico de estrutura organizacional v2 (fonte principal para hierarquia) |
| `capital_humano_v1_hestrutura` | `dp_gente_cultura_trusted` | Estrutura organizacional v1 (legada, complementar) |
| `tb_rh_hestrutura` | `business_gec` | Snapshot atual da estrutura (sem histórico — limitação conhecida) |
| `dim_ch_hst_estrutura` | `business_gec` | Dimensão de histórico de estrutura processada |
| `dim_ch_estrutura` | `business_gec` | Dimensão de estrutura vigente |
| `dim_ch2_hist_estrutura_n1` a `n7` | `business_gec` | Hierarquia histórica por nível (N1=VP … N7) — usadas para quebras |
| `unico_moodle_mdl_user` | `dp_gente_cultura_trusted` | Usuários cadastrados na plataforma UniCo/Moodle |
| `unico_moodle_int_colaborador` | `dp_gente_cultura_trusted` | Integração UniCo ↔ RM. Campos: `funccpf`, `funcnome`, `funcemailcorp`, `funcfuncaonome`, `funcfuncaocod`, `funcsituacao` |
| `unico_moodle_mdl_course` | `dp_gente_cultura_trusted` | Catálogo de cursos UniCo |
| `unico_moodle_mdl_tool_certificate_issues` | `dp_gente_cultura_trusted` | Certificações emitidas — 1 registro por (usuário × curso) |
| `tb_vw_treinamento_unico` | `business_gec` | View processada de treinamento UniCo com flag de conclusão |
| `blend_arquivo_engajamento_consolidado` | `dp_gente_cultura_trusted` | Pesquisas de engajamento Blend — fonte de Engajamento & Diversidade e ENPS |
| `servicenow_ito_desligamentos_voluntarios` | `dp_gente_cultura_trusted` | Desligamentos voluntários com motivo — fonte de Turnover |
| `rm_corporerm_tbltotvskpifinanceiro` | `dp_gente_cultura_trusted` | Dados financeiros RM — fonte de Folha de Pagamento |
| `sharepoint_lancamento_manual_pd_financeiro_ativacao_horas` | `dp_gente_cultura_trusted` | Lançamento manual de horas via SharePoint — complemento da Folha |
| `tbltotvskpifinanceiro` | `cdl_gente_integration` | Staging financeiro RM |
| `tbltotvskpifuncionario` | `cdl_gente_integration` | Staging de funcionários RM |
| `vwKPIFuncionario_HstSalarial` | `cdl_gente_integration` | Histórico salarial RM |
| `rh_hestrutura_hist` / `rh_hestrutura` | `cdl_gente_integration` | Estrutura organizacional Capital Humano (staging) |

---

## Linhagem por produto — fontes detalhadas

### Visão 360 do Colaborador (`fat_funcionario_analitico`)
Fontes: `cdl_gente_integration` (tbltotvskpifuncionario, vwKPIFuncionario_HstSalarial, rh_hestrutura_hist, vw_curso_importancia_da_diversidade) + `cdl_gente_business` (dim_gerente_senior, dim_gerente, dim_coordenador, dim_nivel_hierarquico, dim_raca_cor, dim_tempo, dim_filial, dim_ch_estrutura_n1..n6)

### Folha de Pagamento (`vw_processamento_analitico_folha`)
Fontes: `dp_gente_cultura_trusted` (rm_corporerm_tbltotvskpifinanceiro, sharepoint_lancamento_manual_pd_financeiro_ativacao_horas) + `cdl_gente_integration` (tbltotvskpifinanceiro) + `cdl_gente_business` (fat_funcionario_analitico, dim_funcionario, dim_vp)

### Engajamento & Diversidade / ENPS (`vw_engajamento_diversidade`, `vw_enps_engajamento`)
Fonte única: `dp_gente_cultura_trusted.blend_arquivo_engajamento_consolidado`
- Engajamento: filtro `desconda LIKE '%Engajamento%'`
- ENPS: filtro `descindicador = 'ENPS'`
- Engine: Redshift (`WITH NO SCHEMA BINDING`)

### Colaborador Ex Aluno Cogna (`vw_colaborador_x_aluno`)
Fontes cruzadas: `cdl_gente_business` (fat_funcionario_analitico, dim_funcionario, dim_tempo) + `dp_aesabi` (stage_sas_tb_dim_pessoa, dm_campanhas_tb_tgt_dmc_ci_aluno/ex_aluno/evadido) + `cdl_platos_business` (tb_tgt_dmc_ci_aluno_pos/ex_aluno_pos/evadido_pos) + `dp_trusted` (colaborar_edprod_edmatric, olimpo_producao_alunsitu)

---

## Convenções técnicas

- **Hierarquia organizacional:** montada via campo `codigo` (ex: `00.09.00.12.02`) — mais confiável que `idestruturapai`
- **Competência:** derivado de `TRUNC(sysdatainicio, 'MM')` — agrupa registros por mês/ano
- **Data sentinel:** datas "nulas" representadas como `1900-01-01` — nunca filtrar por `IS NULL` em campos de data
- **Join UniCo ↔ RM:** chave = CPF (`unico_moodle_mdl_user.idnumber = unico_moodle_int_colaborador.funccpf`)
- **Filtros padrão UniCo:** excluir `username IN ('guest','aluna','admin','aluno')`, manter `funcsituacao IN ('Ativo','Férias')`, excluir tutores e docentes
- **Engajamento/ENPS:** engine Redshift com `WITH NO SCHEMA BINDING` — atenção ao criar views dependentes

---

## Pendências de investigação

| Produto | Situação | Ação recomendada |
|---|---|---|
| Indicadores de Capacitação (`vw_media_horas_capacitação`) | Lógica em PySpark no gcc-v1 — view SQL não localizada no datax-gcc | Confirmar se a view foi criada a partir dos scripts `aux_ext_treinamento_*` ou se ainda está pendente de migração |
| Base Analítica de Remuneração (`fat_remuneracao_analitico`) | SQL encontrado no gcc-v1, não no datax-gcc | Validar se o pipeline já foi migrado para o datax-gcc ou se ainda roda apenas pelo gcc-v1 |

---

## Stakeholders recorrentes

| Nome | Papel |
|---|---|
| Karla Ynonye Zucchini | DPO / reportante |
| Luciano De Oliveira Santos | Responsável técnico (DAPL-550) |
| Paulo Denilson | Engenharia — lógica de estrutura organizacional |
| Alexandre / Alê | Arquitetura — grupo de segurança PBI |
| Brandão | Plataforma — ingestão de novas fontes |
| Rodolfo Romano | Equipe tech Elofy (rodolfo.romano@cogna.com.br) |

---

## Scripts de referência no repo

| Script | O que faz |
|---|---|
| `gente_cultura/estrutura_unificada.sql` | Rascunho do UNION v1+v2 (tem filtro de teste — não usar direto) |
| `gente_cultura/estrutura_ancestrais.sql` | Lógica de ancestrais via campo `codigo` |
| `gente_cultura/estrutura_descendentes.sql` | Lógica de descendentes via campo `codigo` |
| `hestrutura/query/dim_ch_estrutura.sql` | Pipeline atual: carrega só snapshot mais recente |
| `views/query/vw_treinamento_unico.sql` | Join UniCo × RM em produção |

---

## Backlog de entregas — Prazo: julho 2026

> Legenda de status: **Concluído** | **Homologado** | **Em homologação** | **Em desenvolvimento** | **Ingestão** | **Discovery** | **Despriorizdo**

### Prioridade 1 — Health Metrics

| Indicador | Status G&C | Painel | Frequência | Observação |
|---|---|---|---|---|
| Engajamento | Concluído | Blend (parceiro) | Carga fria ~4x/ano | Favorabilidades, eNPS, lNPS por pesquisa |
| Turnover | Concluído | People Analytics | Diária (tarde) | Desligamentos voluntários — cruzamento manual com Service Now |
| Diversidade | Concluído | People Analytics | Diária (tarde) | Favorabilidade LGBTQIA+ — Blend / Manual. Quebras: VP e Diretoria |

### Prioridade 1 — Gestão de Desempenho

> ⚠️ Em 2025 houve mudança de classificação na Elofy. Avaliar impacto e ajustar. Validar com Raquel se os dados estão disponíveis.

| Indicador | Status G&C | Etapa | Quebras | Observação |
|---|---|---|---|---|
| Colaboradores avaliados | Em desenvolvimento (Refinamento) | Ingestão | VP, Diretoria, raça, PCD, tempo de casa, tempo da última movimentação | Revisar painel |
| Avaliações de Liga | Em desenvolvimento (Refinamento) | Ingestão | Por consenso, raça, PCD, tempo de casa, última movimentação, YoY | Revisar painel |
| Feedbacks | Em desenvolvimento (Refinamento) | Ingestão | Raça, PCD, tempo de casa, última movimentação, competência destacada, YoY | Revisar painel |
| PDIs | Em desenvolvimento (Refinamento) | Ingestão | Raça, PCD, tempo de casa, última movimentação, YoY | Revisar painel |
| Pessoas no L Superior | Em desenvolvimento (Refinamento) | Ingestão | VP, raça, PCD, tempo de casa, última movimentação, YoY | Revisar painel |
| Pessoas no L Inferior | Em desenvolvimento (Refinamento) | Ingestão | VP, raça, PCD, tempo de casa, última movimentação, YoY | Revisar painel |
| Avaliação de competências | Em desenvolvimento (Refinamento) | Ingestão | VP, raça, PCD, tempo de casa, última movimentação, líder/não líder, YoY | Revisar painel |

### Prioridade 1 — Treinamento (UniCo)

> Migrando dados do Moodle devido a alterações do fornecedor. Painel destino: Portal UniCo / Experiência do colaborador.

| Indicador | Status G&C | Etapa | Quebras | Observação |
|---|---|---|---|---|
| Cursos pendentes | Homologado | Discovery | Individual — curso + gestor | Integração com Service Now para cobrança |

### Prioridade 1 — Geral

| Indicador | Status G&C | Etapa | Quebras | Observação |
|---|---|---|---|---|
| Tempo de casa | Em homologação | Não iniciado | Por colaborador | Tempo sequencial independente de múltiplos vínculos — RM |
| Tempo na função | Em homologação | Não iniciado | Por colaborador | Tempo no cargo atual — RM |

### Prioridade 1 — Orçamento

> Fonte: RM / Hyperion / Capital Humano. Quebras comuns: VP, Diretoria, organograma, regional, recebimento, CAPEX, OPEX. Necessário time de dados — verificar owner das queries no lake.

| Indicador | Status G&C | Etapa |
|---|---|---|
| Realizado | Homologado | Ingestão |
| Orçado | Homologado | Ingestão |
| Real vs Orçado | Em desenvolvimento | Ingestão |
| Forecast | — | Ingestão |
| Real vs Forecast | — | Ingestão |
| Folha / ROL | — | Ingestão |

### Prioridade 3 — Treinamento (UniCo — DAPL-550)

> Painel: Portal UniCo. Quebras comuns: tipo de usuário (colaborador/externo), VP, Diretoria, Regional, MoM, YoY. Fonte: UniCo / RM.

| Indicador | Status G&C | Etapa |
|---|---|---|
| Certificações emitidas | Desenvolvimento | Etapa 1 |
| Horas de capacitação | Desenvolvimento | Etapa 1 |
| Taxa de pessoas certificadas | Desenvolvimento | Etapa 1 |
| Quantidade de pessoas certificadas | Desenvolvimento | Etapa 1 |
| Ranking usuários | Desenvolvimento | Etapa 1 |
| Ranking cursos | Desenvolvimento | Etapa 1 |
| Conclusão de trilhas | Desenvolvimento | Etapa 1 |
| Analítico de conclusão | Desenvolvimento | Etapa 1 |
| Certificações por estrutura | Desenvolvimento | Etapa 1 — quebra VP e Diretoria |
| Analítico de pendências | Desenvolvimento | Etapa 1 |

### Prioridade 4 — Recrutamento e Seleção (Gupy)

> Em desenvolvimento por terceiro. Painel: Jornada do Candidato. Fonte: Gupy / RM / Capital Humano. Quebras: VP, Diretoria, regional, recebimento, função. Frequência: manual 1x/dia.

| Indicador | Status G&C | Etapa |
|---|---|---|
| Gestão de Vagas (total, abertas, rascunho, canceladas, admissão, encerradas) | Em desenvolvimento (Ingestão) | Desenvolvimento |
| SLA das etapas de recrutamento e seleção | Em desenvolvimento (Ingestão) | Desenvolvimento |
| Candidatos por vaga / funil | Em desenvolvimento (Ingestão) | Desenvolvimento |
| Vagas afirmativas (PCD, Racial, Gênero) | Em desenvolvimento (Ingestão) | Desenvolvimento |
| Contratados por source / função | Em desenvolvimento (Ingestão) | Desenvolvimento |
| Valoriza (vagas, entrevistados, contratados) | Em desenvolvimento (Ingestão) | Desenvolvimento |
| Migração de contrato | Em desenvolvimento (Ingestão) | Desenvolvimento |
| Médias SLA (geral, publicação, R&S, admissão) | Em desenvolvimento (Ingestão) | Desenvolvimento |
| Total de aplicações / candidatos em vagas afirmativas | Em desenvolvimento (Ingestão) | Desenvolvimento |
| Reprovados Valoriza / Candidatos por etapas | Em desenvolvimento (Ingestão) | Desenvolvimento |
