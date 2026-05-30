# 🏆 MIDAS BR — Documentação Completa v6

> **O Sistema de Previsão da Bolsa de Valores**
> *Tecnologia Brasileira. Inteligência Global.*

---

## 📋 Sumário

1. [Visão Geral](#visão-geral)
2. [Novidades da v6](#novidades-da-v6)
3. [Como Funciona](#como-funciona)
4. [Requisitos e Instalação](#requisitos-e-instalação)
5. [Como Executar](#como-executar)
6. [Estrutura Completa (49 células)](#estrutura-completa)
7. [Universo das 1.000 Empresas](#universo-das-1000-empresas)
8. [Sistema de Cache JSON](#sistema-de-cache-json)
9. [Metodologia do Modelo de IA](#metodologia-do-modelo-de-ia)
10. [Simulação Top 30% Previsíveis](#simulação-top-30-previsíveis)
11. [Coeficiente MIDAS](#coeficiente-midas)
12. [Impostos na Venda](#impostos-na-venda)
13. [Análise de Debêntures](#análise-de-debêntures)
14. [Outputs Gerados](#outputs-gerados)
15. [Interpretando os Resultados](#interpretando-os-resultados)
16. [Customização](#customização)
17. [Perguntas Frequentes](#perguntas-frequentes)
18. [Aviso Legal](#aviso-legal)

---

## Visão Geral

O **MIDAS BR** é um sistema completo de análise quantitativa de ativos financeiros em Python que:

- Analisa **~1.000 empresas** de múltiplos mercados (EUA, Brasil, Europa, Ásia)
- Usa **Machine Learning (Gradient Boosting)** para prever a direção dos preços
- Realiza **backtesting rigoroso**: treina em 4 anos, testa no 1 ano mais recente
- Gera **relatório visual completo** com 9+ gráficos e tabelas detalhadas
- Calcula **impostos reais** (IR, IRRF, DARF) na venda para pessoa física brasileira
- Analisa **debêntures** com dados de mercado em tempo real (Banco Central)
- Simula quanto você teria ganho investindo nos **Top 30% mais previsíveis**

---

## Novidades da v6

| Recurso | Versão | Descrição |
|---|---|---|
| 🏢 1.000 empresas | v6 | Expandido de 100 → ~1.000 tickers |
| 🎯 Simulação Top 30% | v6 | Compara estratégia focada vs diversificada |
| 💾 Cache incremental | v4 | Baixa só os dias que faltam no próximo run |
| ⏱️ Métricas de tempo | v4 | Mostra % de tempo gasto em API vs ML |
| 🧾 Impostos BR 2025 | v3 | Calcula IR, IRRF, DARF por empresa |
| 📜 Debêntures | v3 | Analisa 10 debêntures com IR real |
| 💰 Coeficiente MIDAS | v2 | Score 0–100 de lucratividade esperada |
| 📋 Tabela 07.1 | v5 | Previsibilidade em ordem crescente |

---

## Como Funciona

```
◄──────────────────── 5 ANOS DE DADOS ────────────────────────►

│◄────────── TREINO (4 anos) ──────────►│◄──── TESTE ──────────►│
│  Modelo APRENDE padrões históricos    │  Simulação como se    │
│  Nunca vê dados do período de teste   │  fosse tempo real     │
▼                                       ▼                       ▼
HOJE − 5 anos                       HOJE − 1 ano             HOJE
(START_DATE)                        (TRAIN_END)            (END_DATE)
```

### O que o modelo prevê

O MIDAS BR prevê a **direção** do próximo dia:
```
Pergunta: "Amanhã o preço vai SUBIR ou CAIR?"

pred_ret > 0  →  PREVIU ALTA  (1)
pred_ret ≤ 0  →  PREVIU BAIXA (0)

Acerto: pred_dir == real_dir
Baseline: 50% (chute aleatório)
```

---

## Requisitos e Instalação

### Hardware recomendado

| Componente | Mínimo | Recomendado |
|---|---|---|
| CPU | 4 núcleos | 8+ núcleos |
| RAM | 16 GB | 32 GB |
| Armazenamento | 5 GB | 10 GB |
| Internet | Necessária (1ª vez) | Banda larga |

> ⚠️ Com 1.000 empresas, a RAM é mais crítica. Use 16 GB ou mais.

### Instalação

```bash
# Ambiente virtual (recomendado)
python -m venv midas_env
source midas_env/bin/activate     # Linux/Mac
midas_env\Scripts\activate        # Windows

# Dependências
pip install yfinance prophet scikit-learn pandas numpy \
            matplotlib seaborn tqdm tabulate requests beautifulsoup4
```

**Na Célula 01 do notebook:** apague os `#` do bloco comentado para instalar automaticamente.

---

## Como Executar

```bash
jupyter notebook midas_br_v6.ipynb
```

Execute as células **em ordem de cima para baixo**.

### Estimativa de tempo por etapa

| Célula | Etapa | 1ª execução | Execuções seguintes |
|---|---|---|---|
| 01 | Instalação de libs | 2–8 min | Pular (comentado) |
| 02 | Imports | < 30 seg | < 30 seg |
| 02-B | Config. cache | < 5 seg | < 5 seg |
| 03 | Tickers | < 1 seg | < 1 seg |
| 04 | Features | < 1 seg | < 1 seg |
| **05** | **Loop ~1.000 empresas** | **2–6 horas** | **10–30 min** |
| 05-B | Gráfico de timing | ~10 seg | ~10 seg |
| 06–12 | 7 gráficos de análise | ~2 min | ~2 min |
| 07.1 | Tabela + Simulação Top 30% | ~30 seg | ~30 seg |
| 13–15 | Relatório + exportação | ~30 seg | ~30 seg |
| 16–18 | Coeficiente MIDAS | ~1 min | ~1 min |
| 19 | Impostos | ~10 seg | ~10 seg |
| 20–21 | Debêntures + comparativo | ~15 seg | ~15 seg |

> ⏱️ Tempo total estimado:
> **1ª execução:** 3 a 7 horas (depende da CPU e internet)
> **Execuções seguintes:** ~15 a 45 minutos (graças ao cache)

---

## Estrutura Completa

O notebook tem **49 células** (25 markdown + 24 código):

```
CÉLULA 01    — Instalação (bloco comentado, ativar só na 1ª vez)
CÉLULA 02    — Imports, configurações, tema dourado
CÉLULA 02-B  — Cache JSON + atualização incremental
CÉLULA 03    — Lista das ~1.000 empresas
CÉLULA 04    — Engenharia de 20 features técnicas
CÉLULA 05    — Loop principal: download + treino + backtest
CÉLULA 05-B  — Gráficos de timing (API % vs ML %)
CÉLULA 06    — Tabela consolidada de resultados
CÉLULA 07    — Gráfico: acurácia por empresa (rótulos em todas as barras)
CÉLULA 07.1  — Tabela crescente + Simulação Top 30%
CÉLULA 08    — Histograma + pizza de classes
CÉLULA 09    — Top 10 vs Bottom 10
CÉLULA 10    — Acertos vs Erros acumulados
CÉLULA 11    — Heatmap mensal (Top 20)
CÉLULA 12    — Previsão vs Real (Top 3)
CÉLULA 13    — Relatório executivo final
CÉLULA 14    — Tabelas Top 20 / Bottom 20
CÉLULA 15    — Exportação de todos os arquivos
CÉLULA 16    — Coeficiente MIDAS (score 0–100)
CÉLULA 17    — Gráficos do Coeficiente + Simulação R$ 10k
CÉLULA 18    — Tabela de recomendações finais
CÉLULA 19    — Calculadora de impostos BR 2025
CÉLULA 20    — Análise de debêntures (CDI real + IPCA real)
CÉLULA 21    — Comparativo Ações vs Debêntures (pós-IR)
```

---

## Universo das 1.000 Empresas

### Distribuição geográfica

| Mercado | Qtd. | Bolsa | Sufixo |
|---|---|---|---|
| EUA | ~550 | NYSE / NASDAQ | (nenhum) |
| Brasil | ~110 | B3 | `.SA` |
| Europa | ~80 | LSE, Euronext, Xetra | `.L`, `.PA`, `.DE`, etc. |
| Ásia/Pacífico | ~60 | NYSE ADRs, OTC | (nenhum ou ADR) |
| Canadá / LatAm | ~40 | NYSE / TSX | (nenhum) |
| ETFs / Índices | ~10 | NYSE | (nenhum) |

### Destaques por setor

**Brasil (B3):** PETR4, VALE3, ITUB4, BBDC4, ABEV3, WEGE3, RENT3, NUBR33, PRIO3, RDOR3, EMBR3, AZUL4, GOLL4, SUZB3, CMIG4, ELET3, ENEV3 e ~90 outros.

**Tecnologia EUA:** NVDA, AAPL, MSFT, TSLA, AMD, SMCI, MSTR, MARA, RIOT, IONQ, CRWD, NET, DDOG, SNOW e ~100 outros.

**Saúde/Biotech:** LLY, MRNA, BNTX, ABBV, VRTX, BIIB, ISRG, CRSP, BEAM, NTLA e ~110 outros.

---

## Sistema de Cache JSON

### Como funciona

```
Para cada ticker, ao chamar baixar_ou_cachear():

┌── JSON existe e tem dados de ontem? ──► Lê do disco (0 ms de rede)
│
├── JSON existe mas está desatualizado? ──► Baixa SÓ os dias faltando
│   Exemplo: cache vai até 28/mai → baixa apenas 29 e 30/mai
│   Resultado: mescla antigo + novos → salva JSON atualizado
│
└── JSON não existe? ──► Baixa tudo desde START_DATE → salva JSON
```

### Estrutura de arquivos

```
pasta_do_notebook/
└── cache_acoes/
    ├── PETR4.SA_desde20200530.json    ← atualizado incrementalmente
    ├── VALE3.SA_desde20200530.json
    ├── AAPL_desde20200530.json
    └── ... (~1.000 arquivos, ~2–5 GB total)
```

### Por que usar o nome do arquivo sem END_DATE?

Ao incluir só `START_DATE` no nome, o mesmo arquivo é reutilizado em diferentes execuções, e apenas os dias novos são adicionados via update incremental — sem necessidade de recriar o arquivo do zero.

---

## Metodologia do Modelo de IA

### Algoritmo: Gradient Boosting Regressor

```python
GradientBoostingRegressor(
    n_estimators  = 200,   # 200 árvores sequenciais
    learning_rate = 0.05,  # aprendizado conservador
    max_depth     = 4,     # árvores rasas (evita overfitting)
    subsample     = 0.8,   # usa 80% dos dados por iteração
    random_state  = 42     # seed para reprodutibilidade
)
```

### 20 features utilizadas

| Categoria | Features |
|---|---|
| Retornos passados | ret_1d, ret_5d, ret_20d, ret_60d |
| Médias móveis | ma5_ratio, ma10_ratio, ma20_ratio, ma50_ratio, ma200_ratio |
| Volatilidade | vol_10d, vol_30d |
| Indicadores técnicos | bb_pos, rsi, macd, macd_signal, macd_hist |
| Calendário | day_of_week, month, quarter, day_of_month |

### Divisão temporal (sem data leakage)

```
StandardScaler: fit apenas no treino, transform no teste
→ Evita que informação do futuro vaze para o modelo
```

---

## Simulação Top 30% Previsíveis

### Lógica da simulação (Célula 07.1)

```
1. Ordena todas as empresas por acurácia (desc)
2. Seleciona os top 30% (ex: 300 de 1.000)
3. Divide R$ 10.000 igualmente entre essas 300 empresas
4. Calcula retorno real de cada uma no período de backtest
5. Calcula retorno médio da carteira (equal-weight)
6. Compara com: investir igualmente em TODAS as 1.000
```

### Por que Top 30%?

- Evita concentração excessiva (top 1% seria só 10 empresas)
- Equilibra qualidade da previsão e diversificação
- 30% é o ponto ótimo empírico entre precisão e risco

### O que os resultados mostram

| Resultado | Interpretação |
|---|---|
| Top 30% > Todas | ✅ Focar em previsibilidade compensa |
| Top 30% ≈ Todas | ⚖️ Diversificação total equivale |
| Top 30% < Todas | ℹ️ Ações menos previsíveis subiram muito (bull market amplo) |

> Em mercados de alta generalizada (bull), diversificar em tudo tende a superar qualquer filtro. O filtro de previsibilidade se destaca mais em mercados voláteis ou laterais.

---

## Coeficiente MIDAS

Score de 0 a 100 que combina:

```
Coef. MIDAS = (
    Acurácia IA         × 0.35  +
    Retorno real 1 ano  × 0.30  +
    Sharpe Ratio        × 0.20  +
    Consistência mensal × 0.10  +
    Tendência 30d       × 0.05
) normalizado para 0–100
```

| Faixa | Classificação |
|---|---|
| ≥ 72 | 🌟 Forte Compra |
| 60–71 | ✅ Compra |
| 45–59 | ⚖️ Neutro |
| 30–44 | ⚠️ Cautela |
| < 30 | 🔴 Evitar |

---

## Impostos na Venda

Regras vigentes para **Pessoa Física brasileira** em 2025:

| Tipo | Alíquota | Condição |
|---|---|---|
| Swing trade — isenção | 0% | Vendas ≤ R$ 20.000/mês |
| Swing trade | 15% | Vendas > R$ 20.000/mês |
| Day trade | 20% | Sempre (compra e venda no dia) |
| IRRF swing | 0,005% | Sobre o valor de venda |
| IRRF day trade | 1% | Sobre o lucro apurado |

Parâmetros configuráveis na Célula 19:
- `INVESTIMENTO_IR` — valor simulado por empresa
- `CORRETAGEM_PERCENT` — ajuste pela sua corretora (padrão: 0,25%)
- `PREJUIZO_ACUMULADO` — losses de meses anteriores para compensar
- `TIPO_OPERACAO` — `'swing'` ou `'daytrade'`

---

## Análise de Debêntures

A Célula 20 analisa **10 debêntures reais** do mercado brasileiro:

- 4 incentivadas (Lei 12.431) — **IR 0% para PF**
- 4 simples (CDI + spread)
- 1 prefixada
- 1 SABESP

Taxas buscadas em tempo real via **API do Banco Central**:
- SELIC: `api.bcb.gov.br/dados/serie/bcdata.sgs.11`
- IPCA: `api.bcb.gov.br/dados/serie/bcdata.sgs.433`

Tabela regressiva de IR aplicada:

| Prazo | Alíquota |
|---|---|
| ≤ 180 dias | 22,5% |
| 181–360 dias | 20,0% |
| 361–720 dias | 17,5% |
| > 720 dias | 15,0% |
| Incentivadas (PF) | **0%** |

---

## Outputs Gerados

Pasta criada: `midas_br_resultados_YYYYMMDD_HHMM/`

| Arquivo | Conteúdo |
|---|---|
| `midas_resultados_backtest.csv` | Métricas de todas as empresas |
| `midas_coeficiente_lucratividade.csv` | Coeficiente + simulação R$ 10k |
| `midas_acuracia_por_empresa.png` | Barchart com rótulos de previsibilidade |
| `midas_previsibilidade_tabela.png` | Horizontal chart ordem crescente |
| `midas_simulacao_top30.png` | Top 30% vs Todas — 3 painéis |
| `midas_distribuicao.png` | Histograma + pizza de classes |
| `midas_top10_vs_bot10.png` | Ranking top vs bottom |
| `midas_acertos_vs_erros.png` | Acertos e erros acumulados |
| `midas_heatmap_mensal.png` | Heatmap mês a mês |
| `midas_previsao_vs_real.png` | Preço com pontos de acerto/erro |
| `midas_coeficiente_lucratividade.png` | 4 painéis de oportunidades |
| `midas_timing_api.png` | Análise de tempo por etapa |
| `midas_acoes_vs_debentures.png` | Comparativo pós-IR |

---

## Interpretando os Resultados

### Acurácia de direção

| Acurácia global | Significado |
|---|---|
| ≥ 60% | 🏆 Excelente — raro em mercados eficientes |
| 55–59% | ✅ Muito bom — útil como sinal auxiliar |
| 52–54% | ⚠️ Razoável — use com outros indicadores |
| 50–51% | ❌ Fraco — essencialmente aleatório |
| < 50% | ❌ Pior que aleatório — revisar modelo |

### Simulação Top 30% vs Todas

- **Top 30% > Todas por > 5pp:** o filtro de previsibilidade funcionou bem
- **Diferença < 5pp:** mercado subiu de forma generalizada
- **Top 30% < Todas:** ações menos previsíveis tiveram alta explosiva (momentum)

---

## Customização

### Alterar o corte do Top %

```python
# Célula 07.1 — altere o percentual de corte
n_top30pct = max(1, round(n_total * 0.30))  # 30% padrão
# Para Top 20%:
n_top30pct = max(1, round(n_total * 0.20))
# Para Top 10%:
n_top30pct = max(1, round(n_total * 0.10))
```

### Adicionar empresas

```python
# Célula 03 — acrescente ao final da lista:
TICKERS += [
    'NOVO3.SA',   # Exemplo Brasil
    'RDVT3.SA',
    'ARM',        # Exemplo EUA
    'SMCI',
]
```

### Trocar o algoritmo

```python
# Na Célula 05, substitua o modelo:
from xgboost import XGBRegressor
model = XGBRegressor(n_estimators=200, learning_rate=0.05, random_state=42)

# Ou LightGBM (mais rápido):
from lightgbm import LGBMRegressor
model = LGBMRegressor(n_estimators=200, learning_rate=0.05, random_state=42)
```

---

## Perguntas Frequentes

**Q: Por que o loop demora muito mais com 1.000 empresas?**
A: O loop tem complexidade O(n) em downloads e O(n) em treino de ML. Com 1.000 empresas vs 100, o tempo é ~10x maior. O cache elimina os downloads nas próximas execuções, deixando só o ML (~2–5 seg por empresa).

**Q: Quantas empresas vão falhar no download?**
A: Entre 10–20% costumam falhar por: ticker inválido, empresa deslistada, dados insuficientes, ou timeout de rede. O sistema registra todas as falhas em `midas_falhas.csv` e continua processando.

**Q: O Top 30% é calculado antes ou depois de ver os dados de teste?**
A: Depois — o modelo é treinado e avaliado no backtest, e só então o ranking de acurácia é formado. Para uma estratégia real futura, você usaria a acurácia histórica como proxy, mas os resultados reais dependeriam de dados não vistos.

**Q: Por que usar equal-weight (pesos iguais) na simulação?**
A: É a estratégia mais simples e imparcial. Em estratégias reais, você poderia ponderar pelo Coeficiente MIDAS ou pelo Sharpe Ratio para melhorar o desempenho ajustado ao risco.

---

## Aviso Legal

```
╔══════════════════════════════════════════════════════════════╗
║                    ⚠️  AVISO LEGAL                          ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  O MIDAS BR foi desenvolvido EXCLUSIVAMENTE para fins       ║
║  educacionais, acadêmicos e de pesquisa.                    ║
║                                                              ║
║  As previsões e simulações NÃO constituem recomendação      ║
║  de investimento, análise fundamentalista ou gestão de      ║
║  carteira. Resultados passados não garantem resultados      ║
║  futuros.                                                   ║
║                                                              ║
║  Mercados financeiros envolvem RISCO REAL DE PERDA DO       ║
║  CAPITAL INVESTIDO. Consulte sempre um assessor             ║
║  certificado (CEA/CFP) antes de investir.                   ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
```

---

*MIDAS BR v6 — Tecnologia Brasileira. Inteligência Global.*
*O futuro dos investimentos é previsível.*
