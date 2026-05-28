---
inclusion: manual
---

# Produto — Gestão de Desempenho (Elofy)

> **Quando usar:** chame com `#gestao-desempenho` ao trabalhar em qualquer demanda que envolva dados de desempenho da Elofy.

---

## Visão geral

- **Épico:** DAPL-XXXX
- **Schema destino:** [? confirmar — proposta: `cdl_gente_business`]
- **Engine:** [? confirmar]
- **Status:** Em refinamento — ingestão da Elofy pendente
- **Contato técnico fonte:** Rodolfo Romano (rodolfo.romano@cogna.com.br) — equipe tech Elofy
- **Responsável ingestão:** Brandão (Plataforma)

---

## Origem dos dados

- **Elofy:** avaliações, Liga de Gente, feedbacks, PDIs
- **Base de pessoas:** fornecida pela Yas (elegibilidade, diversidade)
- **Metadado Elofy:** recebido de Rodolfo Romano — ingestão a ser executada pela Plataforma (Brandão)

---

## Regras de negócio

- **Base elegível:** colaboradores com 6+ meses de companhia, contratados até 30 de abril do ciclo
- **Exclusões:** monitores e estagiários. [? jovens aprendizes — confirmar]
- **Mapeamento consenso → box (versionar):**
  - Atenção: consenso 1 ou 4
  - Talento: consenso 8, 6 ou 9
  - Sólido: consenso 3, 5, 7 ou 2
- **L Superior:** box talento (consenso 8, 6 ou 9)
- **L Inferior:** box atenção (consenso 1 ou 4)
- **Filtro geral:** elegibilidade ao PRV (elegível / não elegível)
- **Demissão pós-ciclo:** filtro para identificar talentos que saíram após o ciclo
- **Histórico:** incluir ciclos anteriores para avaliações de Liga [? profundidade a confirmar]

---

## Indicadores

| Frente | Indicador | Status | Quebras |
|---|---|---|---|
| Gestão de Desempenho | Colaboradores avaliados | Em refinamento | VP, Diretoria, gênero, raça, PCD, tempo de casa, grupo de cargos, tipo de avaliação |
| Gestão de Desempenho | Média de competência | Em refinamento | VP, Diretoria, raça, PCD, grupo de cargos, competências Cogna |
| Gestão de Desempenho | Avaliações de Liga | Em refinamento | Consenso, raça, PCD, tempo de casa, YoY, grupo de cargos, gênero, VP |
| Gestão de Desempenho | Pessoas no L Superior | Em refinamento | VP, raça, PCD, tempo de casa, YoY, grupo de cargos. Filtro demissão pós-ciclo |
| Gestão de Desempenho | Pessoas no L Inferior | Em refinamento | VP, raça, PCD, tempo de casa, YoY, grupo de cargos |
| Gestão de Desempenho | Feedbacks | ⚠️ não finalizado | Raça, PCD, tempo de casa, competência destacada, YoY |
| Gestão de Desempenho | PDIs | ⚠️ não finalizado | Raça, PCD, tempo de casa, YoY |

---

## Lacunas em aberto

1. **Schema destino:** não definido — confirmar com Eng. de Dados.
2. **Frequência:** D-1 ou por ciclo? Confirmar com G&C.
3. **Profundidade de histórico:** "ciclos anteriores" para Liga — desde quando?
4. **Feedbacks e PDIs:** aguardar definição final de G&C.
5. **Jovens aprendizes:** confirmar se também são excluídos da base.
6. **Formato de entrega da Elofy:** API, banco direto ou arquivo? Confirmar com Brandão.
