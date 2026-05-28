---
inclusion: manual
---

# Contexto de Sessão — DAPL-XXXX Aliança J&A Macroprocessos

> **Como usar:** chame com `#dapl-xxx-alianca` para retomar o contexto operacional desta demanda.

**Última atualização:** 2025-05-26
**Status atual:** Épico refinado — aguardando mapeamento de tabelas no Lake com Diogo Kobayashi.

---

## O que é essa demanda

Produto de dados consolidado para o macroprocesso "Prover Tutorias" da Aliança J&A, com métricas de custo, nível de serviço e CSAT por produto e modalidade.

---

## O que já foi discutido

- Levantamento das 4 fontes de dados (Moodle/Olimpo, Colaborar, PTC/Sofia, Cosmos)
- Identificação dos 3 indicadores principais: custo ÷ demanda, nível de serviço, CSAT
- Definição de escopo: fora do escopo — custo CLT e IA generativa
- Épico gerado e documentado

---

## Decisões tomadas

- Custo de folha CLT (~500 mediadores) fora do escopo desta versão
- CSAT disponível diretamente apenas para PTC (AVA Sofia) — demais via NPS geral
- Contato técnico: Diogo Kobayashi / Fernando Bugatti

---

## Lacunas em aberto

- **Tabelas no Lake:** quais dos 4 sistemas já têm tabelas ingeridas? Mapear com Diogo Kobayashi — pode gerar DDPLs
- **Frequência de atualização:** D-1 ou mensal? Confirmar com Sheila
- **Custo por demanda:** modelo de rateio de licenças não definido — alinhar com financeiro e TO
- **CSAT legado:** confirmar se painel exibe CSAT parcial (só PTC) com flag ou aguarda migração completa
- **Schema destino:** nomenclatura padrão para Aliança J&A no Lake — alinhar com Eng. de Dados

---

## Próximos passos

- Mapear com Diogo Kobayashi quais sistemas estão ingeridos
- Confirmar frequência com Sheila Pelegri
- Alinhar modelo de custo com financeiro / TO (Erika, Daniele)
