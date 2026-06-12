---
inclusion: manual
---

# Template — DCOD | Card de Dashboard / DataViz

> **Quando usar:** quando o épico envolve entrega de dashboard (Power BI, Tableau ou similar).
> **Output destino:** card no projeto DCOD do Jira.
> **Insumo principal:** épico DAPL validado (sem `[?]` em aberto) + transcrição/notas de reunião.
> **Pré-requisito obrigatório:** o épico DAPL deve estar 100% validado antes de gerar o DCOD — mesma regra da DENA.

---

## Pré-condições: validar antes de gerar

Se qualquer campo **obrigatório** estiver vazio, não gere o épico. Liste os campos ausentes em 1 batch e solicite o preenchimento antes de prosseguir.

**Obrigatórios (bloqueantes):**
- Área solicitante e responsável pela demanda
- Objetivo do dashboard — decisões que ele suporta e perguntas que responde
- Ao menos 1 KPI com nome, fórmula e fonte
- Ao menos 1 fonte de dados identificada
- Público-alvo

**Recomendados (não bloqueantes):**
- Mockup ou esboço visual (qualquer formato)
- Glossário de termos de negócio
- Regras de RLS
- Frequência de atualização de dados
- Responsável técnico pelas fontes

Para campos recomendados ausentes, use `[? FALTA: descrição]` no campo correspondente e registre na seção de pendências.

---

## Estrutura obrigatória do output

```markdown
# [DCOD] [Verbo] + [Objeto] + [Área]

**Épico vinculado:** DAPL-XXXX
**Prioridade:** [Alta | Média | Baixa]

---

## 1. Objetivo e contexto

**Área solicitante:** [nome da área]
**Responsável:** [nome do responsável]
**Data da solicitação:** [data]

### Problema ou necessidade
[2-3 frases descrevendo o cenário atual. O que está acontecendo no negócio que gerou a demanda.]

### Objetivo do dashboard
[Quais decisões ele suporta, quais perguntas responde, qual resultado esperado com a entrega.]

### Público-alvo
[Perfis de usuários, cargos, nível técnico e frequência de uso esperada.]

---

## 2. KPIs e métricas

| KPI | Descrição | Fórmula | Fonte | Responsável pelo dado |
|-----|-----------|---------|-------|-----------------------|
| [nome] | [o que mede] | [fórmula] | [sistema/tabela] | [nome/área] |

**Glossário de termos de negócio:**

| Termo | Definição |
|-------|-----------|
| [termo] | [definição] |

> Se o glossário não foi fornecido, extrair os termos presentes nas fórmulas dos KPIs e marcar cada linha com `[? FALTA: validar definição com solicitante]`.

---

## 3. Fontes de dados

| Sistema / Fonte | Tipo | Camada no Lake | Responsável técnico | Acesso disponível? |
|-----------------|------|----------------|---------------------|--------------------|
| [nome] | [BD / arquivo / API] | [dp_*_trusted / cdl_*_business / outro] | [nome/área] | [Sim / Não / [? FALTA]] |

---

## 4. Layout e experiência do usuário

**Mockup:** [descrever o esboço fornecido — ou `[? FALTA: mockup — necessário antes do desenvolvimento]`]

**Estrutura esperada:**
- Páginas previstas: [listar]
- Tipos de visuais: [barras, linhas, KPI cards, tabelas, etc.]
- Filtros e slicers: [campos e posicionamento]
- Navegação entre páginas: [sim/não — descrever se sim]
- Destaques visuais: [o que é destaque vs. detalhe]

---

## 5. Atualização de dados

**Frequência:** [Diária | Semanal | Mensal | Por ciclo | `[? FALTA: confirmar com responsável técnico]`]
**Gateway necessário:** [Sim | Não | [? FALTA]]
**Workspace de publicação:** [nome do workspace Power BI / Tableau]
**Forma de distribuição:** [app Power BI | link direto | embutido em portal]

---

## 6. Segurança e acessos

**RLS necessário:** [Sim | Não]

> Se sim: descrever os perfis de acesso e as regras de Row-Level Security.

**Permissões de acesso:** [quem terá acesso e em qual nível — Viewer, Contributor, Admin]

---

## 7. Critérios de aceite

O épico estará concluído quando:

- [ ] Todos os KPIs listados estão presentes e validados contra a fonte
- [ ] Regras de negócio aplicadas corretamente (conferir com solicitante)
- [ ] Layout implementado conforme mockup aprovado
- [ ] Filtros e navegação funcionando corretamente
- [ ] Refresh configurado na frequência acordada e testado com sucesso
- [ ] [se RLS] RLS implementado e validado por perfil
- [ ] Dashboard publicado no workspace correto com permissões configuradas
- [ ] Usuário final testou e aprovou em homologação
- [ ] Checklist de aceite formal assinado pelo responsável

> Mantenha apenas os critérios aplicáveis. Remova os irrelevantes.

---

## ⚠️ Pontos que precisam de validação

1. **[Campo/Seção]:** [descrição da lacuna] — *Sugestão: [encaminhamento]*
2. ...
```

---

## Regra de priorização

| Condição | Prioridade |
|---|---|
| Decisão crítica com prazo definido | Alta |
| KPIs vinculados a metas estratégicas | Alta |
| Substituição de processo manual recorrente | Média |
| Novo dashboard sem urgência declarada | Média |
| Evolução de dashboard existente | Baixa |
| Análise exploratória ou pontual | Baixa |

---

## Instruções de preenchimento (para o agente)

### Inferências permitidas
- Se a área solicitante está clara no documento ou transcrição, preencher diretamente.
- Se a frequência de atualização não for mencionada, assumir **Diária** e marcar `[? assumido Diária — confirmar com responsável técnico]`.
- Se o glossário não foi fornecido, extrair termos das fórmulas dos KPIs e sinalizar cada um pra validação.
- Se o mockup não foi fornecido, preencher Layout com o que for possível inferir dos KPIs e marcar `[? FALTA: mockup — necessário antes do desenvolvimento]`. **Não bloquear a geração do épico por ausência de mockup.**

### Inferências NÃO permitidas
- Inventar KPIs, fórmulas ou fontes não mencionadas.
- Inventar nomes de stakeholders ou responsáveis técnicos.
- Definir regras de RLS sem base no documento ou transcrição.
- Assumir workspace de publicação sem confirmação.

### Como marcar lacunas

| Situação | Como marcar |
|---|---|
| Falta info totalmente | `[? FALTA: o que está faltando]` |
| Tem inferência razoável | `[? inferência aqui — confirmar]` |
| Tem múltiplas opções | `[? opção A ou opção B — confirmar]` |

### O que NÃO incluir no épico

- Medidas DAX e transformações Power Query — são detalhamentos técnicos que pertencem às tasks de desenvolvimento, não ao épico de escopo.
- "Contexto para IA" como seção explícita — esse insumo é interno ao CoE e não deve aparecer como campo do solicitante no Jira.

### Após o output

Adicione a seção **"⚠️ Pontos que precisam de validação"** com cada `[?]` listado, seção de origem e sugestão de encaminhamento.

Depois, ofereça de forma curta:

> "Quer que eu verifique as fontes no catálogo (discovery) ou tem mais alguma coisa pra ajustar?"
