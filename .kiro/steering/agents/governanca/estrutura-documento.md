---
inclusion: manual
---

# Estrutura do Documento de Governança — Produto de Dados | Cogna

Template normativo. O agente deve seguir esta estrutura exatamente ao gerar o documento.

---

## Instruções de preenchimento

| Situação | Como marcar |
|---|---|
| Informação presente no DAPL/DENA | Preencher diretamente |
| Informação ausente | `[? FALTA: descrição do que falta]` |
| Seção de responsabilidade do DPO | Deixar vazio com nota |
| Checkbox marcado | `[X]` |
| Checkbox desmarcado | `[ ]` |

---

## Template

```
# [Título do produto de dados]

---

## 1 – DADOS GERAIS

| Campo | Valor |
|---|---|
| Área Usuária Requisitante | [nome da área] |
| Usuário Responsável | [nome] |
| Data Product Owner | [nome] |
| Demanda PTC | [X] Sim  [ ] Não |

---

### 1.1 – HISTÓRICO

| Data | Versão | Fase | Descrição da Fase | Autor |
|---|---|---|---|---|
|  |  |  |  |  |

> *Histórico de versões preenchido pelo DPO.*

---

## 2 – LISTA DOS PRINCIPAIS CONTRIBUIDORES / KEY USERS

| Área | Nome | E-mail | Cargo |
|---|---|---|---|
[uma linha por key user informado pelo DPO]

---

## 3 – DETALHAMENTO DO PRODUTO DE DADOS

### 3.1 – OBJETIVO E DESCRIÇÃO DO DOCUMENTO

**Objetivo:**
[ ] Dashboard
[ ] Ingestão de Dados
[ ] API

**Tipo de Visão:**
[ ] Analítico
[ ] Consolidado
[ ] NA

**Tipo de Atualização / Carga:**
[ ] Full
[ ] Incremental
[ ] NA

**Periodicidade de Atualização:**
[ ] Diário
[ ] Mensal
[ ] Anual
[ ] Outros

**Tempo de Retenção:** [valor]

---

### 3.2 – REQUISITOS DE NEGÓCIOS E ESTRATÉGIAS

#### 3.2.1 – OBJETIVO DO NEGÓCIO

[Extraído do DAPL — seção Contexto + Problema ou oportunidade]

#### 3.2.2 – OBJETIVO ESTRATÉGICO

[Extraído do DAPL — objetivo de negócio expandido, se houver]

#### 3.2.3 – BENEFÍCIOS

[Extraído do DAPL — critérios de aceite e valor esperado, adaptado como lista de benefícios]

#### 3.2.4 – ESCOPO DA DEMANDA

[Extraído do DAPL — seção Dentro do escopo]

#### 3.2.5 – DESCRIÇÃO DO PRODUTO FINAL

[Extraído do DAPL + DENA — camada destino, nome da tabela/view, sistema de destino]

#### 3.2.6 – DESCRIÇÃO DO OKR

[Extraído do DAPL se houver. Se não houver: "Não se aplica."]

#### 3.2.7 – DETALHAMENTO DE KRs

[Extraído do DAPL se houver. Se não houver: "Não se aplica."]
- KR1: [descrição]
- KR2: [descrição]
- KR3: [descrição]

#### 3.2.8 – PREMISSAS E RESTRIÇÕES

**Premissas:**
[Extraído do DAPL — seção Premissas]

**Restrições:**
[Extraído do DAPL — seção Restrições conhecidas]

#### 3.2.9 – NÃO ESCOPO

[Extraído do DAPL — seção Fora do escopo]

#### 3.2.10 – ESPECIFICAÇÕES PARA ENGENHARIA DE DADOS

[Extraído da DENA — seção Regras de negócio + Pontos importantes]

##### 3.2.10.1 – Regras de Negócio

[Extraído da DENA — regras de negócio detalhadas, filtros, mapeamentos, volumetria, chave única]

#### 3.2.11 – CRITÉRIOS E HOMOLOGAÇÃO

[Extraído do DAPL + DENA — critérios de aceite técnicos e de negócio]

#### 3.2.12 – OBSERVAÇÕES

[Extraído do DAPL/DENA — observações finais. Se vazio: deixar em branco.]

---

## 4 – REFERÊNCIAS

| ID | Tipo | Descrição | Autor | Data | Link |
|---|---|---|---|---|---|
| 010 |  |  |  |  |  |

---

## 5 – GLOSSÁRIO / TERMINOLOGIA

| Nome | Descrição |
|---|---|
[Extraído do DAPL se houver glossário. Se não houver, deixar tabela vazia.]

---

## 6 – METADADOS

> *Planilha de Metadados a ser anexada pelo DPO.*

**Tabelas Alteradas/Criadas:**

| Sistema / Banco | Tabelas Criadas/Alteradas | Origem dos Dados | Tempo de Retenção |
|---|---|---|---|
[Extraído da DENA — tabela destino + origem dos dados]

**Dicionário de Dados:**

| Origem | Tabela | Descrição da Tabela | Campos da Tabela | Formato e Tamanho | Descrição do Campo | Classificação de Segurança | PII |
|---|---|---|---|---|---|---|---|
[Deixar vazio — será preenchido via planilha de metadados]

**Linhagem de Dados:**

| Tabela Origem | Coluna Origem | Tabela Destino | Coluna Destino | Descrição Linhagem |
|---|---|---|---|---|
[Deixar vazio — será preenchido via planilha de metadados]

**Qualidade de Dados:**

| Fonte de Dados | Tabela | Campo | Responsável | Nome Indicador | Descrição | Regra de Cálculo | Periodicidade | Limite Mínimo | Unidade | Monitoramento | Processo caso limites sejam atingidos |
|---|---|---|---|---|---|---|---|---|---|---|---|
[Deixar vazio — será preenchido via planilha de metadados]

**Termos de Negócio:**

| Nome do Termo | Área | Data Owner | Data Steward | Definição | Termos Relacionados | Sinônimos | Referências Tabela | Campo |
|---|---|---|---|---|---|---|---|---|
[Extraído do DAPL se houver glossário. Se não houver, deixar vazio.]

---

## 7 – ANEXOS

### 7.1 – Queries

[Extraído da DENA — queries SQL utilizadas no produto. Reproduzir em bloco de código.]

```sql
[queries aqui]
```
```

---

## Notas de extração por seção

| Seção | Fonte principal | Fonte secundária | Responsabilidade |
|---|---|---|---|
| 1 — Dados Gerais | DAPL (cabeçalho) | — | Agente |
| 1.1 — Histórico | — | — | DPO (preenche depois) |
| 2 — Key Users | DPO (batch) | — | Agente (transcreve) |
| 3.1 — Objetivo/Tipo | DAPL | — | Agente |
| 3.2.1 a 3.2.5 | DAPL | DENA | Agente |
| 3.2.6 a 3.2.7 | DAPL (OKR/KRs) | — | Agente |
| 3.2.8 a 3.2.9 | DAPL | — | Agente |
| 3.2.10 — Eng. Dados | DENA | — | Agente |
| 3.2.11 — Homologação | DAPL + DENA | — | Agente |
| 4 — Referências | DAPL (se houver) | — | Agente (+ DPO completa) |
| 5 — Glossário | DAPL (se houver) | — | Agente |
| 6 — Metadados | Planilha Excel | DENA (tabelas) | DPO (anexa depois) |
| 7.1 — Queries | DENA | Agente devs | Agente |
