---
inclusion: always
---

# Template — ÉPICO | Produtos de Dados Cogna

> **Quando usar:** sempre. Este é o documento principal de qualquer refinamento.
> **Output destino:** campo "Descrição" do épico no Jira (projeto DAPL).
> **Estilo:** markdown limpo, formato Jira, sem decoração visual exagerada.

---

## Estrutura obrigatória do output

Use **exatamente** a estrutura abaixo. Não pule seções. Use `[? FALTA: ...]` para lacunas.

```markdown
# [Verbo de ação] + [Objeto] + [Contexto de negócio]

**ID:** DAPL-XXXX  
**Prioridade:** [Alta | Média | Baixa]  
**Tipo de demanda:** [escolher 1 abaixo]  
**Camadas envolvidas:** [listar do conjunto abaixo]  

---

## 1. Necessidade de negócio

### Contexto
[2-3 frases descrevendo o cenário atual. O que está acontecendo no negócio que gerou a demanda. Sem floreio.]

### Problema ou oportunidade
[Qual decisão de negócio está sendo prejudicada OU qual oportunidade está sendo perdida. 1-2 frases.]

### Quem é impactado
- **Área solicitante:** [nome da área]
- **Stakeholders secundários:** [áreas/pessoas que se beneficiam ou serão consultados]

---

## 2. Objetivo do épico

Como [persona/área],  
Quero [capacidade ou entrega esperada],  
Para que [resultado de negócio mensurável].

---

## 3. Tipo de demanda

Marcar 1 principal (X) e secundários (-) se aplicável:

- [ ] 🆕 Novo Produto de Dados
- [ ] 🔄 Alteração de Produto Existente
- [ ] 📥 Ingestão de Dados
- [ ] 🏗️ Engenharia de Dados
- [ ] 📊 Visualização / DataViz
- [ ] 🔍 Discovery / Refinamento
- [ ] 🏛️ Governança

---

## 4. Camadas envolvidas

- [ ] Stage / Raw
- [ ] Trusted (dp_trusted)
- [ ] Business (cdl_*_business)
- [ ] Gold / Consumo
- [ ] Visualização (PBI / Tableau)

---

## 5. Escopo

### Dentro do escopo
- [item 1]
- [item 2]

### Fora do escopo (explícito)
- [o que NÃO será entregue, mesmo que pareça relacionado]

### Premissas
- [condições assumidas como verdadeiras para a entrega]

### Restrições conhecidas
- [limites técnicos, de prazo, de dependência]

---

## 6. Critérios de aceite

O épico estará concluído quando:

- [ ] Os dados estão disponíveis na camada acordada
- [ ] A qualidade dos dados foi validada pelo DPO e pelo solicitante
- [ ] A documentação foi atualizada (Gov_Dados / Dicionário de Dados)
- [ ] O produto foi homologado pela área solicitante
- [ ] O acesso foi configurado para os usuários finais
- [ ] [se DataViz] Dashboard publicado e validado em produção
- [ ] [se Ingestão] Pipeline rodando estável por ≥1 ciclo completo

> Mantenha apenas os critérios aplicáveis ao tipo da demanda. Remova os irrelevantes.

---

## 7. Origem dos dados

- **Fonte(s):** [Olimpo / Colaborar / PTC / Hyperion / SAP / API / Excel-SharePoint / outra]
- **Tipo:** [Interno | Externo | Misto]
- **Frequência de atualização:** [Diária | Semanal | Mensal | Por ciclo | Tempo real]
- **Formato de entrega:** [Banco | API | Arquivo CSV/XLSX | Stream]
- **Contato técnico da fonte:** [nome / área]
```

---

## Instruções de preenchimento (para o agente)

### Inferências permitidas
- Se a transcrição menciona área de negócio claramente (ex: "captação", "RH", "financeiro"), preencher "Área solicitante" sem `[?]`.
- Se o tipo de demanda é evidente pelo contexto (ex: "vamos criar uma view nova" → Engenharia de Dados), marcar o tipo principal.
- Se as camadas são mencionadas pelo nome técnico (`trusted`, `business`), marcar diretamente.

### Inferências NÃO permitidas
- Inventar prazo se não foi mencionado.
- Inventar nome de stakeholder específico.
- Inventar números (impacto financeiro, volume de dados, número de usuários).
- Marcar critério de aceite que não foi discutido.

### Como marcar lacunas

| Situação | Como marcar |
|---|---|
| Falta info totalmente | `[? FALTA: o que está faltando]` |
| Tem inferência razoável | `[? inferência aqui — confirmar]` |
| Tem múltiplas opções | `[? opção A ou opção B — confirmar]` |

### Após o output completo

Adicione uma seção abaixo do épico:

```markdown
---

## ⚠️ Pontos que precisam de validação

1. **[Seção X]:** [descrição da lacuna] — *Sugestão: perguntar ao solicitante na próxima sync.*
2. **[Seção Y]:** [descrição da lacuna] — *Sugestão: validar com [área técnica].*
```

---

## Exemplo de output bem preenchido

> **Use este exemplo como referência de estilo, concisão e tom.**

```markdown
# Criar produto de dados de Funil de Captação por Polo

**ID:** DAPL-XXXX  
**Prioridade:** Alta  
**Tipo de demanda:** 🆕 Novo Produto de Dados  
**Camadas envolvidas:** Trusted, Business, Visualização (PBI)  

---

## 1. Necessidade de negócio

### Contexto
A área de Captação acompanha hoje os indicadores de funil em planilhas manuais consolidadas pela equipe regional. Os dados chegam com 3-5 dias de atraso e exigem retrabalho de cruzamento entre fontes.

### Problema ou oportunidade
Sem visão consolidada e tempestiva do funil por polo, a área não consegue identificar gargalos antes do fechamento do ciclo de matrícula, perdendo a janela de ação.

### Quem é impactado
- **Área solicitante:** Captação de Alunos
- **Stakeholders secundários:** Diretoria Comercial, equipes regionais de polo

---

## 2. Objetivo do épico

Como analista de Captação,  
Quero um painel consolidado de funil (inscrição → aprovação → matrícula) por polo,  
Para que eu identifique gargalos antes do fechamento do ciclo e atue em tempo hábil.

---

## 3. Tipo de demanda

- [X] 🆕 Novo Produto de Dados
- [-] 📊 Visualização / DataViz

---

## 4. Camadas envolvidas

- [X] Trusted (dp_trusted)
- [X] Business (cdl_captacao_business)
- [X] Visualização (PBI)

---

## 5. Escopo

### Dentro do escopo
- View consolidada de funil de captação no schema business
- Dashboard PBI com filtro por polo, ciclo, e curso
- Histórico desde 2023

### Fora do escopo
- Integração com CRM de pré-matrícula (ficará para próximo épico)
- [? FALTA: validar se inclui ou não os polos EAD]

### Premissas
- Dados de inscrição vêm do Olimpo (já no Lake)
- Dados de matrícula vêm do Colaborar (já no Lake)

### Restrições conhecidas
- [? FALTA: prazo de entrega — não foi mencionado na reunião]

---

## 6. Critérios de aceite

- [ ] View `vw_funil_captacao_polo` disponível em `cdl_captacao_business`
- [ ] Dashboard PBI publicado em workspace de Captação
- [ ] Documentação no Dicionário de Dados
- [ ] Homologação pela analista responsável (Captação)

---

## 7. Origem dos dados

- **Fonte(s):** Olimpo (inscrições), Colaborar (matrículas)
- **Tipo:** Interno
- **Frequência de atualização:** Diária
- **Formato de entrega:** Banco de dados (já no Lake)
- **Contato técnico da fonte:** [? FALTA: nome do contato técnico]

---

## ⚠️ Pontos que precisam de validação

1. **Escopo — Polos EAD:** não ficou claro se entram no escopo desta versão. *Sugestão: confirmar com a área solicitante.*
2. **Restrições — Prazo:** prazo não foi mencionado na reunião. *Sugestão: perguntar ao DPO se há janela de fechamento de ciclo a respeitar.*
3. **Origem — Contato técnico:** não identificado. *Sugestão: identificar com o time de Eng. de Dados.*
```
