# Case 05 — Daily Operations Command Center

## Visão Geral

| Atributo | Detalhe |
|---|---|
| **Domínio** | Operações / Varejo / Supervisão de Rede |
| **Volume** | ~138MB |
| **Páginas** | 28 páginas (maior do portfólio) |
| **Áreas cobertas** | Vendas · Metas · Campanhas · Inventários · Entregas · Resíduos · NPS · Telemedicina |
| **Ferramenta** | Power BI + SQL Server |

---

## Contexto

Dashboard operacional de supervisão de rede com **28 páginas** cobrindo todas as dimensões do dia a dia de uma rede de varejo farma. Utilizado por supervisores, gerentes regionais e diretoria para acompanhamento em tempo real das operações.

---

## Problema de Negócio

Supervisores de rede sem ponto centralizado de visibilidade operacional. Dados de vendas, campanhas, logística, qualidade e NPS espalhados em múltiplos sistemas — cada área acessava sua fonte individualmente, sem visão consolidada.

**Consequência:** reuniões de alinhamento consumindo horas de preparação manual. Desvios de meta identificados tarde, quando a correção já era mais custosa.

---

## Estrutura do Dashboard (28 Páginas)

### Vendas e Metas
| Página | Descrição |
|---|---|
| **Home** | Navegação central com KPIs executivos |
| **Meta Dia** | Acompanhamento diário de meta por loja |
| **Vendas Diárias** | Evolução de vendas por dia/loja |
| **Vendas MP** | Vendas de medicamentos de marca própria |
| **Vendas MP - Regionais** | Visão regional de MP |

### Campanhas
| Página | Descrição |
|---|---|
| **Campanhas Top 15/20/30** | Rankings de produtos em campanha |
| **Regional Campanhas Top 15** | Performance de campanha por regional |
| **Campanhas M.P** | Campanhas de marca própria |
| **Campanhas Hilab** | Campanhas de exames laboratoriais |
| **Campanhas Telemedicina** | Campanhas de serviços digitais |
| **Campanhas Kits** | Kits promocionais |

### Logística e Inventário
| Página | Descrição |
|---|---|
| **Inventários Rede** | Status de inventários da rede |
| **Inventários Regionais** | Detalhamento por regional |
| **Entregas** | Acompanhamento de entregas |
| **Entregas - Regionais** | Performance logística por regional |

### Qualidade e Compliance
| Página | Descrição |
|---|---|
| **Resíduos** | Descarte de medicamentos e materiais |
| **Resíduos Regionais** | Gestão de resíduos por regional |
| **ENCARTES** | Materiais de comunicação em loja |

---

## Diferenciais Técnicos

- **28 páginas estruturadas** — maior cobertura operacional do portfólio
- **Multi-área** — único modelo atendendo vendas, logística, campanhas e qualidade
- **Visão dupla** — cada tema tem página de rede (consolidada) e regional (detalhada)
- **Rankings dinâmicos** — Top 15/20/30 de campanhas calculados em DAX
- **Operação em tempo real** — estrutura pensada para atualização frequente (intraday)

---

## Impacto de Negócio

- Redução do tempo de preparação de reuniões operacionais
- Identificação imediata de lojas com desvio de meta
- Visibilidade de performance de campanhas em tempo real
- Monitoramento de compliance de resíduos por regional
- Suporte à decisão operacional de supervisores no campo
