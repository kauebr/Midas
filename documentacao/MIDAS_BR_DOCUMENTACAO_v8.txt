# 🏆 MIDAS BR — Documentação Completa v8
## Sistema de Previsão Quantitativa da Bolsa de Valores Brasileira (B3)

> *Tecnologia Brasileira. Inteligência Global.*
> *O futuro dos investimentos é analisável.*

---

## Sumário

1. [Visão Geral](#visão-geral)
2. [Histórico de Versões](#histórico-de-versões)
3. [Arquitetura do Sistema](#arquitetura-do-sistema)
4. [Requisitos e Instalação](#requisitos-e-instalação)
5. [Como Executar](#como-executar)
6. [Estrutura Completa — 55 Células](#estrutura-completa)
7. [Universo da B3 — Descoberta Dinâmica](#universo-da-b3)
8. [Sistema de Cache JSON](#sistema-de-cache-json)
9. [Modelo de IA — Gradient Boosting](#modelo-de-ia)
10. [Features Técnicas (20 variáveis)](#features-técnicas)
11. [Métricas de Avaliação](#métricas-de-avaliação)
12. [Seleção Dupla 30%×30%](#seleção-dupla)
13. [Previsão 2026 — Ano Fechado](#previsão-2026)
14. [Validação: 2025 Previsto vs Real](#validação-2025)
15. [Relatórios da pasta /previsao_2026](#relatórios-previsao_2026)
16. [Coeficiente MIDAS](#coeficiente-midas)
17. [Impostos na Venda (BR 2025)](#impostos)
18. [Debêntures](#debêntures)
19. [Outputs Gerados](#outputs-gerados)
20. [Timing e Performance](#timing-e-performance)
21. [Customização](#customização)
22. [Perguntas Frequentes](#perguntas-frequentes)
23. [Aviso Legal](#aviso-legal)

---

## 1. Visão Geral

O **MIDAS BR** é um framework completo de análise quantitativa de ações da B3 em Python. Ele:

- Descobre **automaticamente** todas as ações listadas na B3 (~475 tickers ativos)
- Baixa 5 anos de histórico diário com **cache JSON incremental**
- Treina um modelo de **Machine Learning (Gradient Boosting)** por empresa
- Realiza **backtest rigoroso**: treina em 4 anos, testa no último 1 ano
- Aplica **seleção dupla 30%×30%** (previsibilidade × lucratividade)
- Gera **previsão dia a dia para 2026** com curvas projetadas
- Compara **2025 previsto vs real** em gráficos side-by-side
- Produz **3 relatórios** salvos em `/previsao_2026`
- Calcula **impostos reais** (IR, IRRF, DARF) para pessoa física brasileira
- Analisa **debêntures** com taxas SELIC/IPCA em tempo real

---

## 2. Histórico de Versões

| Versão | Principal mudança |
|---|---|
| v1 | 100 empresas EUA + BR, backtest básico |
| v2 | Cache JSON, Coeficiente MIDAS, simulação R$ 10k |
| v3 | Impostos BR, análise de debêntures |
| v4 | Cache incremental, métricas de tempo de API |
| v5 | Gráfico 07 com rótulos, tabela 07.1 crescente |
| v6 | ~790 empresas globais, simulação Top 30%, seleção dupla |
| v7 | Expandido para ~1.000 empresas |
| **v8** | **B3 completa (~475) com descoberta dinâmica, previsão 2026, validação 2025 vs real, 3 relatórios em /previsao_2026** |

---

## 3. Arquitetura do Sistema

```
DESCOBERTA DA B3 (Célula 03)
  └── API dadosdemercado.com.br  →  ~475 tickers ativos (atualização diária)
  └── Status Invest               →  fallback
  └── Lista curada base           →  garantia mínima
  └── Cache local 7 dias

CACHE DE DADOS (Célula 02-B)
  └── cache_acoes/TICKER_desdeANO.json
  └── Update incremental: só baixa dias que faltam
  └── Nível 1: download completo (1ª vez)
  └── Nível 2: incremental (execuções seguintes)
  └── Nível 3: leitura local (dados de ontem/hoje)

MODELO (Célula 04-05)
  └── 20 features técnicas por empresa
  └── GradientBoostingRegressor (200 árvores)
  └── Treino: 4 anos | Teste: 1 ano (sem data leakage)
  └── Previsão de direção: sobe (1) ou cai (0)

ANÁLISE DE BACKTEST (Células 06-15)
  └── Acurácia de direção vs baseline 50%
  └── Coeficiente MIDAS score 0-100
  └── Seleção dupla 30%×30%
  └── 9 gráficos + CSVs exportados

PREVISÃO 2026 (Células 22-24)
  └── Retreina com histórico completo
  └── Projeta 252 dias úteis de 2026 recursivamente
  └── Gera curvas de preço projetadas
  └── 3 relatórios em pasta /previsao_2026

COMPLEMENTOS
  └── Impostos IR/IRRF/DARF (Célula 19)
  └── Debêntures com taxas BCB em tempo real (Células 20-21)
```

---

## 4. Requisitos e Instalação

### Hardware

| Componente | Mínimo | Recomendado |
|---|---|---|
| CPU | 4 núcleos | 8+ núcleos |
| RAM | 8 GB | 16 GB |
| Disco | 3 GB livres | 8 GB livres |
| Internet | Necessária (1ª execução) | Banda larga |

### Instalação das dependências

```bash
# Criar e ativar ambiente virtual
python -m venv midas_env
source midas_env/bin/activate    # Linux / Mac
midas_env\Scripts\activate       # Windows

# Instalar bibliotecas
pip install yfinance prophet scikit-learn pandas numpy \
            matplotlib seaborn tqdm tabulate requests scipy
```

Alternativamente, **na Célula 01 do notebook**: apague os `#` do bloco comentado para instalação automática. Após instalar, comente de volta — não precisa rodar novamente.

---

## 5. Como Executar

```bash
jupyter notebook midas_br_b3_2026.ipynb
```

Execute as células **em ordem de cima para baixo**. O notebook detecta automaticamente a pasta onde está salvo — nenhum caminho fixo necessário.

### Tempo estimado por etapa

| Célula | Etapa | 1ª execução | Execuções seguintes |
|---|---|---|---|
| 01 | Instalação de libs | 3–8 min | Pular (comentar) |
| 02 | Imports e configs | < 30 seg | < 30 seg |
| 02-B | Cache e pasta base | < 5 seg | < 5 seg |
| **03** | **Descoberta da lista B3** | **5–15 seg** | **< 1 seg (cache)** |
| 04 | Definição de features | < 1 seg | < 1 seg |
| **05** | **Loop ~475 empresas** | **1–3 horas** | **15–30 min** |
| 05-B | Gráfico de timing | ~10 seg | ~10 seg |
| 06–12 | 7 gráficos de backtest | ~2 min | ~2 min |
| 07.1 | Tabela + simulação 30% | ~30 seg | ~30 seg |
| 13–15 | Relatório + exportação | ~30 seg | ~30 seg |
| 16–18 | Coeficiente MIDAS | ~1 min | ~1 min |
| 19 | Impostos | ~10 seg | ~10 seg |
| 20–21 | Debêntures + comparativo | ~15 seg | ~15 seg |
| **22** | **Previsão 2026** | **15–40 min** | **15–40 min** |
| 23 | Validação 2025 vs real | ~1 min | ~1 min |
| **24** | **Relatórios /previsao_2026** | **~2 min** | **~2 min** |
| **TOTAL** | | **~2 a 4 horas** | **~40 min** |

---

## 6. Estrutura Completa — 55 Células

```
[00] Capa MIDAS BR
[01] MD  — CÉLULA 01: Instalação de dependências
[02] COD — Bloco comentado; ativar só na 1ª vez
[03] MD  — CÉLULA 02: Imports e configurações
[04] COD — Theme dourado, datas, constantes de cor
[05] MD  — CÉLULA 02-B: Pasta base e cache JSON
[06] COD — BASE_DIR = os.getcwd(); funções de cache incremental
[07] MD  — CÉLULA 03: Descoberta automática da B3
[08] COD — 3 fontes + cache 7 dias → TICKERS list
[09] MD  — CÉLULA 04: Engenharia de features
[10] COD — add_features(); FEATURE_COLS (20 variáveis)
[11] MD  — CÉLULA 05: Loop principal
[12] COD — download → features → treino → backtest → timing
[13] MD  — CÉLULA 05-B: Análise de tempo de API
[14] COD — 3 gráficos: barras, pizza cache/API, histograma
[15] MD  — CÉLULA 06: Tabela geral de resultados
[16] COD — df_results ordenado por acurácia
[17] MD  — CÉLULA 07: Gráfico acurácia com rótulos
[18] COD — barchart com rótulo "XX.X% / Alta/Média/Baixa"
[19] MD  — CÉLULA 07.1: Tabela crescente + simulação Top 30%
[20] COD — tabela pior→melhor + simular_carteira() + 3 painéis
[21] MD  — CÉLULA 08: Histograma + pizza de classes
[22] COD — distribuição de acurácias + proporção por classe
[23] MD  — CÉLULA 09: Top 10 vs Bottom 10
[24] COD — barras horizontais comparativas
[25] MD  — CÉLULA 10: Acertos vs Erros
[26] COD — stacked bar + totais consolidados
[27] MD  — CÉLULA 11: Heatmap mensal
[28] COD — heatmap RdYlGn mês a mês (Top 20)
[29] MD  — CÉLULA 12: Previsão vs Real (Top 3)
[30] COD — preço real + pontos verde/vermelho
[31] MD  — CÉLULA 13: Relatório final
[32] COD — relatório executivo ASCII com interpretação automática
[33] MD  — CÉLULA 14: Tabelas Top 20 / Bottom 20
[34] COD — tabulate fancy_grid
[35] MD  — CÉLULA 15: Exportação de arquivos
[36] COD — salva CSVs + move PNGs para pasta timestampada
[37] MD  — CÉLULA 16: Coeficiente MIDAS
[38] COD — score 0-100 (5 dimensões ponderadas)
[39] MD  — CÉLULA 17: Gráficos do Coeficiente
[40] COD — 4 painéis: ranking, simulação, scatter, evolução Top 5
[41] MD  — CÉLULA 18: Tabela de recomendações
[42] COD — resumo executivo + tabela completa + Top 5 detalhado
[43] MD  — CÉLULA 19: Impostos na venda
[44] COD — IR 15%/20%, IRRF, isenção R$20k, DARF
[45] MD  — CÉLULA 20: Debêntures
[46] COD — 10 emissões reais + SELIC/IPCA via API BCB
[47] MD  — CÉLULA 21: Ações vs Debêntures
[48] COD — 4 painéis comparativos pós-IR
[49] MD  — CÉLULA 22: Previsão 2026
[50] COD — retreina com histórico completo + projeta 252 dias
[51] MD  — CÉLULA 23: 2025 Previsto vs Real
[52] COD — grid 3×3 com linha real x linha prevista
[53] MD  — CÉLULA 24: Relatórios /previsao_2026
[54] COD — 3 relatórios CSV + 3 gráficos + curvas diárias 2026
```

---

## 7. Universo da B3 — Descoberta Dinâmica

### Por que não usar lista hardcoded?

A B3 tem ~475 empresas listadas, mas esse número muda constantemente — novas IPOs, cancelamentos de listagem, fusões e aquisições. Uma lista estática fica desatualizada em semanas.

### Estratégia em cascata (3 fontes)

**Fonte 1 — dadosdemercado.com.br (preferida)**
```
URL: https://www.dadosdemercado.com.br/api/v1/stocks
Atualização: diária (reflete o pregão mais recente)
Retorno: lista de todos os ativos com volume no último pregão
```

**Fonte 2 — Status Invest (fallback)**
```
URL: https://statusinvest.com.br/acoes/busca
Método: extração de padrões de ticker via regex no HTML
Ativada: somente se Fonte 1 retornar menos de 100 tickers
```

**Fonte 3 — Lista curada (~120 tickers)**
```
Sempre somada às fontes dinâmicas
Garante cobertura das empresas mais importantes
Organizada por setor com comentários
```

### Cache de tickers (7 dias)

```
cache_acoes/b3_tickers_lista.json
├── Criado na 1ª execução (ou após 7 dias)
├── Contém: lista de tickers + data de geração
└── Renovado automaticamente quando expira
```

O sistema sempre consolida, deduplica e valida o sufixo `.SA` antes de usar.

### Cobertura por setor (estimativa com ~475 tickers)

| Setor | Tickers ativos |
|---|---|
| Energia Elétrica | ~50 |
| Bancos / Financeiras | ~45 |
| Saúde / Farmácia | ~30 |
| Varejo / Consumo | ~45 |
| Construção Civil | ~30 |
| Petróleo / Gás / Combustíveis | ~20 |
| Alimentos / Bebidas | ~25 |
| Transporte / Logística | ~25 |
| Tecnologia / Fintechs | ~20 |
| Mineração / Siderurgia | ~20 |
| Papel / Celulose / Química | ~20 |
| Outros (educação, agro, holding…) | ~145 |

---

## 8. Sistema de Cache JSON

### Função principal: `baixar_ou_cachear(ticker)`

Implementa 3 níveis de acesso em ordem de preferência:

**Nível 3 — Cache válido (zero rede)**
```
Condição: JSON existe E último dado é de ontem ou hoje
Ação:     lê do disco diretamente
Tempo:    < 5 ms por empresa
```

**Nível 2 — Atualização incremental**
```
Condição: JSON existe MAS está desatualizado
Ação:     calcula dias faltantes → baixa só esses dias →
          mescla com dados antigos → salva JSON atualizado
Tempo:    ~200–500 ms por empresa (poucos KB de dados novos)
```

**Nível 1 — Download completo**
```
Condição: JSON não existe
Ação:     baixa 5 anos de histórico → salva JSON completo
Tempo:    ~500–2000 ms por empresa
```

### Estrutura dos arquivos de cache

```
pasta_do_notebook/
└── cache_acoes/
    ├── b3_tickers_lista.json        ← lista de tickers (7 dias)
    ├── PETR4.SA_desde20200530.json  ← histórico de cada ação
    ├── VALE3.SA_desde20200530.json
    └── ... (~475 arquivos, 1-3 GB total)
```

O nome do arquivo usa `START_DATE` mas não `END_DATE`. Isso garante que o mesmo arquivo é reutilizado e apenas estendido incrementalmente a cada execução.

---

## 9. Modelo de IA — Gradient Boosting

### Configuração

```python
GradientBoostingRegressor(
    n_estimators  = 200,   # 200 árvores sequenciais
    learning_rate = 0.05,  # aprendizado conservador (evita overfitting)
    max_depth     = 4,     # árvores rasas
    subsample     = 0.8,   # 80% dos dados por iteração (stochastic GB)
    random_state  = 42     # seed fixo para reprodutibilidade
)
```

### Por que Gradient Boosting?

- Robusto a outliers e dados financeiros ruidosos
- Captura relações não-lineares entre indicadores
- Funciona bem com datasets de 200–1.000 observações
- Não precisa de normalização para treinar (mas usa StandardScaler para consistência)
- Significativamente mais estável que redes neurais para séries temporais curtas

### O que o modelo prevê

O modelo prevê o **retorno do próximo dia** (regressão). A **direção** (sobe/cai) é derivada do sinal:

```
pred_ret > 0  →  PREVIU ALTA  (1)
pred_ret ≤ 0  →  PREVIU BAIXA (0)

Acerto: pred_dir == real_dir
Baseline: 50% (equivale a chutar aleatório)
```

### Divisão temporal — sem data leakage

```
◄──────────── 5 ANOS DE DADOS ──────────────►
│                                             │
│◄──────── TREINO (4 anos) ──────►│◄─ TESTE ►│
│  Modelo aprende aqui            │ Nunca viu │
▼                                 ▼           ▼
HOJE-5anos                    HOJE-1ano     HOJE
```

`StandardScaler`: fit **apenas no treino**, transform aplicado com a mesma escala no teste. Usar dados de teste para calcular a escala seria data leakage.

---

## 10. Features Técnicas (20 variáveis)

| # | Feature | Janela | Interpretação |
|---|---|---|---|
| 1 | `ret_1d` | 1 dia | Retorno diário (momentum imediato) |
| 2 | `ret_5d` | 5 dias | Retorno semanal |
| 3 | `ret_20d` | 20 dias | Retorno mensal |
| 4 | `ret_60d` | 60 dias | Retorno trimestral |
| 5 | `ma5_ratio` | 5 dias | Preço / MA5: desvio da média de 1 semana |
| 6 | `ma10_ratio` | 10 dias | Preço / MA10 |
| 7 | `ma20_ratio` | 20 dias | Preço / MA20 |
| 8 | `ma50_ratio` | 50 dias | Preço / MA50 (tendência média) |
| 9 | `ma200_ratio` | 200 dias | Preço / MA200 (tendência longa) |
| 10 | `vol_10d` | 10 dias | Volatilidade de curto prazo |
| 11 | `vol_30d` | 30 dias | Volatilidade de médio prazo |
| 12 | `bb_pos` | — | Posição nas Bandas de Bollinger: 0=inferior, 1=superior |
| 13 | `rsi` | 14 dias | RSI: >70 sobrecomprado, <30 sobrevendido |
| 14 | `macd` | 12/26 dias | Diferença EMA12 − EMA26 |
| 15 | `macd_signal` | 9 dias | EMA9 do MACD (linha de sinal) |
| 16 | `macd_hist` | — | MACD − Signal (histograma) |
| 17 | `day_of_week` | — | 0=seg, 4=sex (padrão semanal) |
| 18 | `month` | — | 1–12 (sazonalidade anual) |
| 19 | `quarter` | — | 1–4 (padrões trimestrais) |
| 20 | `day_of_month` | — | 1–31 (padrões de início/fim de mês) |

---

## 11. Métricas de Avaliação

### Acurácia de direção (métrica principal)

```
Acurácia = Dias com direção correta / Total de dias de teste × 100

Exemplo:
  252 dias de teste, 145 acertos
  Acurácia = 145/252 × 100 = 57,5%
```

**Benchmark:** 50% = resultado de um modelo que chuta aleatoriamente.

### Interpretação

| Acurácia | Classificação | Interpretação |
|---|---|---|
| ≥ 58% | 🟢 Alta | Vantagem clara sobre o aleatório |
| 52–57% | 🟡 Média | Vantagem moderada |
| < 52% | 🔴 Baixa | Próximo ao aleatório |

### MAPE (métrica secundária)

Erro percentual médio absoluto sobre a magnitude do retorno previsto. Menos útil que a acurácia de direção, mas indica a qualidade da previsão de magnitude.

### Hit Rate Mensal

Acurácia calculada mês a mês. Identifica se o modelo é consistente ao longo do tempo ou se tem períodos ruins (ex: meses de alta volatilidade por eventos macro).

---

## 12. Seleção Dupla 30%×30%

```
~475 empresas processadas
  │
  ├── Rankeadas por acurácia (desc)
  └── Top 30% = ~140 empresas mais previsíveis
        │
        ├── Rankeadas pelo Coeficiente MIDAS (desc)
        └── Top 30% = ~42 empresas
              │
              └── Simulação de investimento equal-weight
```

**Por que filtrar duas vezes?**

Filtro 1 (previsibilidade) garante que o modelo sabe interpretar o ativo. Filtro 2 (lucratividade) garante que o ativo também tem bom potencial de retorno. Usar apenas um filtro deixa passar empresas previsíveis mas estagnadas, ou lucrativas mas imprevisíveis.

**Equal-weight:** o capital é dividido igualmente entre todas as empresas selecionadas. Para uso real, ponderação pelo Coeficiente MIDAS ou pelo Sharpe pode melhorar o resultado.

---

## 13. Previsão 2026 — Ano Fechado (Célula 22)

### Metodologia

O modelo é **retreinado com todo o histórico disponível** (sem reservar período de teste) para maximizar a informação antes de projetar 2026.

```python
# Projeção recursiva dia a dia
preco_simulado = ultimo_preco_real
for data in dias_uteis_2026:          # 252 pregões
    atualiza_features_de_calendario(data)
    ret_previsto = model.predict(features)
    ret_previsto = clip(ret_previsto, -10%, +10%)   # sanidade
    preco_simulado *= (1 + ret_previsto)
    registra(data, preco_simulado, ret_previsto)
```

### Score de Potencial 2026 (0–100)

```
Potencial = (
    acuracia_hist_normalizada  × 0.30 +   histórico confiável?
    retorno_projetado           × 0.35 +   modelo prevê alta?
    sharpe_projetado            × 0.20 +   qualidade do retorno?
    pct_dias_alta               × 0.15     maioria dos dias sobe?
) → convertido para [0, 100]
```

### Limitações importantes

- Previsão de longo prazo acumula erro a cada passo recursivo
- Eventos imprevisíveis (resultados, crises, eleições) não são capturados
- Quanto mais distante a data projetada, maior o intervalo de incerteza
- Usar como **sinal de tendência**, não como preço-alvo preciso

---

## 14. Validação: 2025 Previsto vs Real (Célula 23)

Esta célula é a **mais honesta** do sistema — mostra exatamente onde o modelo acertou e onde errou durante o período de backtest.

### Grid de gráficos 3×3

As 9 empresas com maior acurácia exibem, para o período de teste:

- **Linha azul** — preço real de fechamento (base 100 no início)
- **Linha dourada tracejada** — simulação da trajetória prevista pelo modelo
- **Área verde** — períodos em que a realidade superou a previsão
- **Área vermelha** — períodos em que o modelo foi otimista demais
- **Título** — acurácia, retorno real e retorno previsto de forma comparada

### Tabela comparativa

Para **todas** as empresas com backtest, a tabela mostra:
- Retorno real no período de teste
- Retorno acumulado das previsões
- Erro em pontos percentuais
- % de dias com direção errada

---

## 15. Relatórios da pasta /previsao_2026 (Célula 24)

A pasta `previsao_2026/` é criada automaticamente ao lado do notebook.

### Relatório 1 — Carteira Recomendada (investir agora)

**Arquivo:** `RELATORIO_1_CARTEIRA_RECOMENDADA_YYYY-MM-DD.csv`

**Critério de seleção:**
- Acurácia histórica ≥ 55% (modelo confiável)
- Potencial 2026 ≥ 60 (projeção positiva)

**Colunas:**

| Coluna | Descrição |
|---|---|
| ticker | Código da ação |
| potencial_2026 | Score 0–100 |
| prioridade | 🌟 ALTA / ✅ MÉDIA / ⚪ BAIXA |
| alocacao_% | Alocação sugerida equal-weight |
| retorno_proj_% | Retorno projetado para 2026 |
| pct_dias_alta_% | % de pregões de 2026 com alta prevista |
| sharpe_proj | Sharpe ratio da projeção |
| acuracia_hist_% | Acurácia histórica do modelo |
| preco_atual | Último preço real conhecido |
| preco_proj_dez26 | Preço projetado para dezembro/2026 |

**Gráfico:** `RELATORIO_1_CARTEIRA_GRAFICO.png`
Barras de potencial + comparativo retorno vs alocação.

---

### Relatório 2 — Lista de Monitoramento (assistir antes de decidir)

**Arquivo:** `RELATORIO_2_MONITORAMENTO_YYYY-MM-DD.csv`

**Critério:**
- Acurácia histórica ≥ 52%
- Potencial 2026 entre 45 e 59 (zona de incerteza)

**Colunas adicionais:**

| Coluna | Descrição |
|---|---|
| status | 📈 Tendência Positiva / 📉 Negativa / ↔️ Lateral |
| nota | Recomendação qualitativa (aguardar, acompanhar, etc.) |

---

### Relatório 3 — Análise Geral (todas as previsíveis)

**Arquivo:** `RELATORIO_3_ANALISE_GERAL_YYYY-MM-DD.csv`

**Critério:** todas as empresas com acurácia ≥ 52%.

**Inclui colunas de 2025 real** mescladas com projeções 2026:
- Retorno Real 2025 (backtest)
- Retorno Previsto 2025 (simulação do modelo)
- Erro de retorno em pp
- Classificação 2026 (🌟 FORTE / ✅ BOM / ⚪ NEUTRO / ⚠️ FRACO)

**Gráfico:** `RELATORIO_3_ANALISE_GERAL_GRAFICO.png`
Scatter retorno real 2025 × projeção 2026 + top 15 potencial.

---

### Outros arquivos gerados em /previsao_2026

| Arquivo | Descrição |
|---|---|
| `PREVISAO_2026_CURVAS_DIARIAS_TOP10.png` | Curvas de preço projetado dia a dia para as top 10 empresas |
| `VALIDACAO_2025_PREVISTO_VS_REAL.png` | Grid 3×3 com linha real vs linha prevista |

---

## 16. Coeficiente MIDAS

Score de 0 a 100 calculado ao final do backtest para cada empresa:

```
Coef. MIDAS = (
    acuracia_normalizada   × 0.35 +   (acc - 50) / 50  → [-1, 1]
    retorno_real           × 0.30 +   clip em [-1, 2]
    sharpe_ratio           × 0.20 +   Sharpe/3, clip [-1, 1]
    consistencia_mensal    × 0.10 +   % meses positivos → [-1, 1]
    tendencia_30d          × 0.05     tendência recente amplificada
) → convertido de [-1, 1] para [0, 100]
```

| Faixa | Classificação |
|---|---|
| ≥ 72 | 🌟 Forte compra |
| 60–71 | ✅ Compra |
| 45–59 | ⚖️ Neutro |
| 30–44 | ⚠️ Cautela |
| < 30 | 🔴 Evitar |

---

## 17. Impostos na Venda (BR 2025)

Calculados na **Célula 19** com parâmetros configuráveis.

### Tabela de alíquotas (Pessoa Física)

| Tipo de operação | Alíquota | Condição |
|---|---|---|
| Swing trade — isenção | 0% | Vendas totais ≤ R$ 20.000 no mês |
| Swing trade — tributado | 15% | Vendas totais > R$ 20.000 no mês |
| Day trade | 20% | Sempre, independente do valor |
| IRRF swing (dedo-duro) | 0,005% | Sobre o valor de venda |
| IRRF day trade | 1% | Sobre o lucro apurado |

**Parâmetros configuráveis:**
- `INVESTIMENTO_IR` — valor simulado por empresa
- `CORRETAGEM_PERCENT` — taxa da corretora (padrão: 0,25%)
- `EMOLUMENTOS_B3` — emolumentos B3 (padrão: 0,0325%)
- `PREJUIZO_ACUMULADO` — losses de meses anteriores para compensar
- `TIPO_OPERACAO` — `'swing'` ou `'daytrade'`

**Importante:** o DARF deve ser pago até o último dia útil do mês seguinte à venda. O IRRF retido pode ser deduzido do valor total a pagar.

---

## 18. Debêntures

**Células 20–21** analisam 10 debêntures reais do mercado brasileiro:

| Tipo | Emissores (exemplos) | Tributação |
|---|---|---|
| Incentivada (Lei 12.431) | CEMIG, Vale, Petrobras, SABESP | **IR 0%** para PF |
| Simples CDI+spread | WEG, Renner, Raízen, MRV | Tabela regressiva |
| Prefixada | TIM | Tabela regressiva |

### Tabela regressiva de IR

| Prazo | Alíquota |
|---|---|
| ≤ 180 dias | 22,5% |
| 181–360 dias | 20,0% |
| 361–720 dias | 17,5% |
| > 720 dias | 15,0% |
| Incentivadas (PF) | **0%** |

### Taxas de mercado em tempo real

Buscadas via API do Banco Central do Brasil:
- SELIC: `api.bcb.gov.br/dados/serie/bcdata.sgs.11/dados/ultimos/1`
- IPCA 12m: `api.bcb.gov.br/dados/serie/bcdata.sgs.433/dados/ultimos/12`

---

## 19. Outputs Gerados

### Pasta principal (timestampada)

`midas_br_resultados_YYYYMMDD_HHMM/`

| Arquivo | Conteúdo |
|---|---|
| `midas_resultados_backtest.csv` | Acurácia e métricas de todas as empresas |
| `midas_coeficiente_lucratividade.csv` | Coeficiente MIDAS + simulação |
| `midas_acuracia_por_empresa.png` | Barchart com rótulos de previsibilidade |
| `midas_previsibilidade_tabela.png` | Horizontal chart ordem crescente |
| `midas_simulacao_top30.png` | Top 30%×30% vs todas |
| `midas_distribuicao.png` | Histograma + pizza de classes |
| `midas_top10_vs_bot10.png` | Ranking top vs bottom |
| `midas_acertos_vs_erros.png` | Acertos e erros acumulados |
| `midas_heatmap_mensal.png` | Heatmap mês a mês (Top 20) |
| `midas_previsao_vs_real.png` | Preço com pontos de acerto/erro |
| `midas_coeficiente_lucratividade.png` | 4 painéis de oportunidades |
| `midas_timing_api.png` | % tempo em API vs ML |
| `midas_acoes_vs_debentures.png` | Comparativo pós-IR |

### Pasta /previsao_2026

| Arquivo | Conteúdo |
|---|---|
| `RELATORIO_1_CARTEIRA_RECOMENDADA_*.csv` | Empresas para investir |
| `RELATORIO_1_CARTEIRA_GRAFICO.png` | Visual da carteira |
| `RELATORIO_2_MONITORAMENTO_*.csv` | Empresas para monitorar |
| `RELATORIO_3_ANALISE_GERAL_*.csv` | Análise completa |
| `RELATORIO_3_ANALISE_GERAL_GRAFICO.png` | Scatter 2025 vs 2026 |
| `PREVISAO_2026_CURVAS_DIARIAS_TOP10.png` | Curvas diárias Top 10 |
| `VALIDACAO_2025_PREVISTO_VS_REAL.png` | Grid validação 2025 |

### Cache (reutilizável entre execuções)

```
cache_acoes/
├── b3_tickers_lista.json      ← lista B3 (renovada a cada 7 dias)
├── PETR4.SA_desde*.json       ← histórico de preços (incremental)
└── ... (~475 arquivos)
```

---

## 20. Timing e Performance

### O que o relatório de tempo mostra (Célula 05-B)

Ao final do loop principal, um painel ASCII exibe:

```
╔══════════════════════════════════════════════════════╗
║       ⏱️  MIDAS BR — RELATÓRIO DE TEMPO              ║
╠══════════════════════════════════════════════════════╣
║  🌐 API Yahoo Finance  :   12.4 s  (31.2%)           ║
║     [████████████░░░░░░░░░░░░░░░░░░░░░░░░]           ║
║     Chamadas reais: 23   Cache hits: 452              ║
║  🧠 Machine Learning   :   24.1 s  (60.5%)           ║
║  ⚙️  Features + outros  :    3.3 s   (8.3%)           ║
╚══════════════════════════════════════════════════════╝
```

Três gráficos complementam: barras de tempo por empresa, pizza cache-hit vs API, histograma de distribuição dos tempos.

---

## 21. Customização

### Forçar rebusca da lista B3

```python
# Apaga o cache de tickers para forçar nova busca
import os
os.remove('cache_acoes/b3_tickers_lista.json')
```

### Adicionar empresas manualmente

```python
# Ao final da Célula 03, após a consolidação:
TICKERS += ['NOVA3.SA', 'XPTO4.SA']
TICKERS = list(dict.fromkeys(TICKERS))  # remove duplicatas
```

### Alterar o corte de 30% para a simulação

```python
# Célula 07.1 — mudar ambos os percentuais
n_top_prev = max(1, round(n_total * 0.20))   # 20% mais previsíveis
n_top_lucr = max(1, round(n_top_prev * 0.20))  # 20% mais lucrativos
```

### Trocar o algoritmo de ML

```python
# Célula 05 — substitua o modelo
from xgboost import XGBRegressor
model = XGBRegressor(n_estimators=200, learning_rate=0.05,
                     max_depth=4, subsample=0.8, random_state=42)

# LightGBM (mais rápido):
from lightgbm import LGBMRegressor
model = LGBMRegressor(n_estimators=200, learning_rate=0.05, random_state=42)
```

### Alterar o critério dos relatórios 2026

```python
# Célula 24 — mude os thresholds
df_invest = df_prev26[
    (df_prev26['acuracia_hist_%'] >= 58) &   # mais rigoroso
    (df_prev26['potencial_2026']  >= 65)     # mais exigente
].copy()
```

---

## 22. Perguntas Frequentes

**P: A lista da B3 inclui FIIs e ETFs?**
R: A fonte primária (dadosdemercado) retorna apenas ações (equities). FIIs têm sufixo `11` e podem aparecer na lista — o sistema os processa normalmente mas podem ter menor histórico disponível.

**P: Por que algumas empresas têm `dir_acc_pct` abaixo de 50%?**
R: É possível em backtests individuais, especialmente com períodos de teste curtos. O modelo pode ter aprendido o inverso do padrão real. Em média, com 475 empresas, a acurácia global tende a ficar acima de 50%.

**P: A previsão 2026 usa dados de 2026?**
R: Não. A previsão é gerada usando apenas dados até o último fechamento disponível (mai/2026 no momento atual). Os dias de 2026 são projetados recursivamente a partir do último estado real.

**P: O que acontece com empresas que saíram da bolsa durante o período?**
R: O yfinance retorna os dados disponíveis. Se a empresa foi deslistada, o histórico termina na data do último pregão. O sistema aceita isso normalmente, desde que haja 300+ dias de dados.

**P: Posso rodar em Google Colab?**
R: Sim. Faça upload do notebook, a Célula 01 instala as dependências. Atenção: o runtime do Colab pode ser encerrado após 90 min de inatividade. Salve os CSVs intermediários manualmente ou use o Google Drive como BASE_DIR.

**P: A pasta /previsao_2026 é criada onde?**
R: Sempre ao lado do notebook (o sistema usa `os.getcwd()` como BASE_DIR). Portátil — funciona em qualquer máquina sem configuração.

---

## 23. Aviso Legal

```
╔══════════════════════════════════════════════════════════════╗
║                    ⚠️  AVISO LEGAL                          ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  O MIDAS BR foi desenvolvido EXCLUSIVAMENTE para fins       ║
║  educacionais, acadêmicos e de pesquisa.                    ║
║                                                              ║
║  As previsões e simulações NÃO constituem:                  ║
║  • Recomendação de compra ou venda de ativos                ║
║  • Consultoria de investimentos                             ║
║  • Gestão de carteira ou portfólio                         ║
║  • Análise fundamentalista certificada                      ║
║                                                              ║
║  Mercados financeiros são influenciados por fatores         ║
║  imprevisíveis (crises, decisões de bancos centrais,        ║
║  resultados corporativos surpresa, eventos geopolíticos)    ║
║  que nenhum modelo estatístico antecipa com consistência.   ║
║                                                              ║
║  Investir em renda variável envolve RISCO REAL DE           ║
║  PERDA DO CAPITAL INVESTIDO.                                ║
║                                                              ║
║  Consulte sempre um assessor de investimentos               ║
║  certificado (CEA/CFP) antes de tomar decisões.             ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
```

---

## Referências

- **Gradient Boosting:** Friedman, J.H. (2001). *Greedy Function Approximation: A Gradient Boosting Machine*
- **RSI:** Wilder, J.W. (1978). *New Concepts in Technical Trading Systems*
- **MACD:** Appel, G. (1979). *The Moving Average Convergence-Divergence Trading Method*
- **Bandas de Bollinger:** Bollinger, J. (2001). *Bollinger on Bollinger Bands*
- **B3 — lista de ativos:** dadosdemercado.com.br/acoes (atualização diária)
- **SELIC/IPCA:** api.bcb.gov.br (Banco Central do Brasil)
- **Dados históricos:** finance.yahoo.com via yfinance

---

*MIDAS BR v8 — Tecnologia Brasileira. Inteligência Global.*
*O futuro dos investimentos é analisável.*
