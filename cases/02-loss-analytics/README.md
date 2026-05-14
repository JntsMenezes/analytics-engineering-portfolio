# Case 02 — Retail Loss Intelligence Platform

## Visão Geral

| Atributo | Detalhe |
|---|---|
| **Domínio** | Varejo Farma / Gestão de Perdas |
| **Volume** | ~650MB de dados comprimidos |
| **Tabelas** | 22 tabelas de dados |
| **Medidas DAX** | 40+ em tabela centralizada `Medidas` |
| **Páginas** | 15 (Home + 14 visões de detalhamento) |
| **Ferramenta** | Power BI (Import Mode) + SQL Server |

---

## Contexto

Plataforma de análise de perdas para rede de varejo farma. O modelo consolida **múltiplas categorias de perda** que anteriormente eram analisadas em silos: perdas por NFS, quebras operacionais, inventários, produtos vencidos, remanejamentos e contratos comerciais.

---

## Problema de Negócio

Gestão de perdas sem visibilidade consolidada. Cada tipo de perda era tratado por uma área diferente, sem KPI unificado, sem comparativo histórico e sem rastreabilidade entre origem e valor da perda.

**Impacto anterior:**
- Decisões de compra sem considerar histórico de perdas por categoria
- Reuniões de resultado sem dados de perdas em tempo real
- Impossibilidade de comparar performance entre lojas e regionais

---

## Arquitetura do Modelo

### Tabelas de Dados (22 tabelas)

**Fatos:**
- `Perdas` — registro de perdas por NFS
- `inventarios` — perdas identificadas em inventários
- `Faturamento` / `Faturamento Agrupado` — base de faturamento para cálculo de % de perda
- `Atuacao_vencidos` — produtos com vencimento próximo
- `Remanejamento` — movimentações de transferência entre lojas
- `Estornos` — reversões de perdas registradas
- `Recargas` — operações de recarga

**Dimensões:**
- `Calendario` — dimensão de data
- `Empresas` — lojas e regionais
- `Produtos` — catálogo de produtos
- `TIPO_BAIXA` — categorias de baixa/perda
- `CURVAS` — curvas ABC de produtos

**Staging / Suporte:**
- `PV_FATURAMENTO`, `PV_ID`, `PV_Identificados`, `PV_Vendas` — base de produtos vencidos
- `IDENTIFICACAO&VENDAS-PV` — cruzamento de identificação com vendas
- `Prox.Vencimento` — produtos com vencimento nos próximos dias
- `Contratos comerciais perdas` — perdas cobertas por contrato com fornecedor
- `903,906,907,915` — lojas agrupadas por regional
- `tEixo` — eixo dinâmico para visuais

---

## Medidas DAX — Arquitetura em Camadas

Projeto utilizou **tabela centralizada `Medidas`** — boa prática de governança que centraliza todos os KPIs num único ponto.

### KPIs Principais

```dax
-- Perda total consolidada (NFS + Inventário)
[PERDAS + INVENTARIO] =
    [PERDAS NFS] + [PERDAS INVENTARIO]

-- Comparativo Year-over-Year
[PERDAS + INVENTARIOS YOY] =
    CALCULATE([PERDAS + INVENTARIO], SAMEPERIODLASTYEAR(Calendario[Dt]))

-- % de perda sobre faturamento
[%Geral x Fat.] =
    DIVIDE([PERDAS + INVENTARIO], [Faturamento Total])

-- KPI com cor dinâmica (verde/vermelho)
[COR_PERDAS] =
    IF([% Geral vs Geral YOY] < 0, "#27AE60", "#E74C3C")
```

### Categorias de Perda

| Categoria | Descrição |
|---|---|
| Perdas NFS | Perdas registradas via nota fiscal de saída |
| Perdas Inventário | Divergências identificadas em inventários físicos |
| Quebras Operacionais | Quebras por manuseio, validade ou dano |
| Produtos Vencidos | Perdas por vencimento de produtos |
| Remanejamento | Transferências entre lojas para evitar perda |
| Contratos Comerciais | Perdas cobertas por acordos com fornecedores |

---

## Páginas do Relatório

| Página | Tipo | Descrição |
|---|---|---|
| **Home** | Visível | KPIs executivos + navegação |
| Detalhe Período | Drill-through | Detalhamento por período selecionado |
| Atuação Vencidos | Análise | Performance de atuação em vencidos |
| Perdas Recuperadas | Análise | Perdas revertidas/recuperadas |
| Quebras Operacionais | Análise | Detalhe de quebras por tipo/loja |
| Perdas Inventário | Análise | Divergências de inventário |
| Remanejamento | Análise | Movimentações entre lojas |
| BASE * | Dados | Tabelas base para drill-through |

---

## Diferenciais Técnicos

- **Tabela `Medidas` centralizada** — todas as 40+ medidas num único ponto de governança
- **YOY em múltiplas categorias** — comparativo ano anterior em cada tipo de perda
- **Cores dinâmicas via DAX** — KPIs mudam de cor conforme tendência (melhora/piora)
- **15 páginas estruturadas** — separação clara entre visão executiva e detalhamento operacional
- **Múltiplas fontes integradas** — 22 tabelas de sistemas diferentes consolidadas no mesmo modelo

---

## Impacto de Negócio

- Visibilidade unificada de perdas por categoria, loja, regional e período
- Redução do tempo de análise mensal de perdas
- Identificação de lojas com maior índice de perda para ação preventiva
- Suporte a negociação com fornecedores usando dados de perdas por contrato comercial
