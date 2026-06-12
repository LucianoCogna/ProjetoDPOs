---
inclusion: manual
---

# Limitações do Power BI — Referência para Mockups | Cogna

Este arquivo é a fonte de verdade sobre o que existe e o que **não existe** no Power BI Desktop nativo. Consultar sempre antes de gerar o HTML do mockup.

---

## O que NÃO existe no Power BI (não incluir no mockup)

### 1. Botão "Aplicar" em filtros/slicers
Slicers no Power BI são **responsivos por padrão** — a seleção é aplicada imediatamente em todos os visuais da página. Existe um recurso "Apply all slicers" como botão nativo, mas é uma otimização de performance, não um padrão de design. Não incluir botão "Aplicar" nos mockups.

### 2. Badges/chips coloridos em células de tabela
Table/Matrix suporta formatação condicional (cor de fundo da célula, cor da fonte, data bars, ícones), mas **não suporta** badges/chips com `border-radius`, `padding` customizado ou múltiplos badges lado a lado na mesma célula. Alternativas corretas:
- Formatação condicional de cor de fundo da célula inteira
- Ícones condicionais (KPI icons)
- Data bars dentro de células

### 3. Sparklines / mini gráficos dentro de células de tabela
Não usar sparklines (mini bar charts ou line charts) como coluna dentro de Table/Matrix. O recurso esteve em preview mas não é confiável em produção. Para indicar tendência dentro de uma tabela, usar:
- Ícones condicionais (seta para cima/baixo) na célula
- Um gráfico de linhas separado como visual independente na página

### 4. Sidebar de navegação com hover/estados
Power BI não tem sidebar nativa. A navegação entre páginas é feita via:
- Abas de página na parte inferior (page tabs)
- Botões de navegação com ação "Page navigation"
- Bookmarks para simular estados
- Page Navigator visual (visual nativo que lista páginas)

### 5. Modais / popups interativos
Power BI não suporta modais. Alternativas:
- Tooltips de página (report page tooltips) para detalhamento
- Drillthrough pages para aprofundamento
- Bookmarks que mostram/escondem visuais

### 6. Dropdowns com múltiplas colunas ou layouts complexos
Slicers são simples: lista, dropdown, between (range) ou relative date. Não suportam layouts multi-coluna dentro do dropdown.

### 7. Degradê/gradiente dentro de barras
Power BI não suporta gradient/degradê CSS dentro de uma mesma barra. Usar cor sólida por barra. Para hierarquia visual, usar formatação condicional baseada em regras.

### 8. Texto com formatação mista na mesma célula de tabela
Table/Matrix aplica formatação por coluna inteira — não por célula individual com mix de estilos (bold + normal na mesma célula).

### 9. Cards com avatar/foto circular
O visual Card mostra apenas valor + rótulo. Não suporta imagens circulares embutidas. Para exibir imagem, usar Image URL em tabela ou o visual "Image" como elemento separado.

### 10. Animações, transições CSS, hover effects elaborados
Power BI é estático visualmente. Interações se limitam a cross-filter, cross-highlight, drill e tooltips.

### 11. Mais de 8–10 visuais por página
Cada visual gera queries separadas ao modelo semântico. Acima de 8–10 visuais por página, a performance degrada significativamente. Respeitar esse limite no mockup.

---

## O que o Power BI SUPORTA nativamente (usar no mockup)

- Slicers: dropdown, list, tile, between — filtro responsivo imediato
- Botão "Limpar filtros" (Clear all slicers) — nativo
- Formatação condicional em tabelas: cor de fundo, cor de fonte, ícones, data bars
- Ícones condicionais: setas, semáforos, flags para indicar tendência
- Bookmarks para simular navegação/estados
- Botões com ações: navegação de página, bookmark, URL, drillthrough
- Tooltips de página (report page tooltips) para detalhamento ao hover
- Drillthrough pages para aprofundamento de contexto
- Page Navigator visual para menu de páginas
- Cross-filter e cross-highlight entre visuais
- Hierarquias com drill-down/drill-up nos eixos

---

## Visuais nativos disponíveis

Card, Multi-row card, KPI, Gráfico de barras, Gráfico de colunas, Gráfico de linhas, Gráfico de área, Pizza/Rosca, Mapa, Tabela, Matriz, Slicer (segmentador), Treemap, Funil, Gauge, Waterfall, Scatter plot, Decomposition tree, Key influencers, Q&A, Paginated report visual.

---

## Limites de dados por visual

| Visual | Limite |
|---|---|
| Table / Matrix | 3.500 data points (linhas × colunas visíveis) |
| Barras / Colunas | 3.500 data points |
| Scatter plot | 10.000 data points |
| Mapas | 3.500 data points |
| KPI visual | 1 valor + meta + tendência |
| Card | 1 valor (Multi-row card: até ~5 valores) |

---

## Especificações técnicas do canvas

| Parâmetro | Valor |
|---|---|
| Resolução padrão | 1280 × 720 px (16:9) |
| Cabeçalho | Shape/Textbox/Image com logo + título (boa prática) |
| Rodapé | Textbox com medida DAX de data de atualização (boa prática) |
| Degradê em barras | Não suportado — usar cor sólida |
| Máximo de visuais por página | 8–10 (performance) |
