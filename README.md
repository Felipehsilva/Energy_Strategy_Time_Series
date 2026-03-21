# ⚡ Sistema Preditivo para Aquisição Estratégica de Energia (Hedge)

> **Projeto de Data Science end-to-end** que combina previsão de série temporal com um motor de decisão prescritivo para proteger empresas de varejo contra a volatilidade do Mercado Livre de Energia elétrica.

![Status](https://img.shields.io/badge/Status-Em%20Desenvolvimento-yellow)
![Python](https://img.shields.io/badge/Python-3.10-blue)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 📌 Sumário

- [O Problema de Negócio](#o-problema-de-negócio)
- [Solução Proposta](#solução-proposta)
- [Premissas do Projeto](#premissas-do-projeto)
- [Arquitetura da Solução](#arquitetura-da-solução)
- [Stack Tecnológica](#stack-tecnológica)
- [Estrutura do Repositório](#estrutura-do-repositório)
- [Como Executar](#como-executar)
- [Resultados e Métricas](#resultados-e-métricas)
- [Motor de Decisão Prescritivo](#motor-de-decisão-prescritivo)
- [Próximos Passos](#próximos-passos)
- [Autor](#autor)

---

## 🏭 O Problema de Negócio

Redes de supermercados figuram entre os maiores consumidores de energia elétrica do varejo — a energia representa, em média, o **segundo maior custo operacional** dessas empresas.

No **Mercado Livre de Energia**, a flutuação do **PLD (Preço de Liquidação das Diferenças)** representa um risco financeiro severo. A estratégia tradicional de compras reativas expõe a empresa a picos de preço durante períodos secos, onde o custo por MWh pode **quadruplicar**.

**Empresa simulada:** Rede varejista *Foodshop* — 50 lojas de grande porte na região de São Paulo/SP.

| Métrica | Valor |
|---|---|
| Consumo médio por loja/dia | 406 MWh |
| Consumo total da rede/mês | ~12.180 MWh |
| Despesa mensal estimada | R$ 2.436.000,00 |
| Despesa anual estimada | ~R$ 29,2 milhões |

---

## 💡 Solução Proposta

Um sistema de **Analytics end-to-end** com três camadas:

1. **Preditiva (IA):** Modelo de série temporal para prever o volume de consumo de energia (MWh) para os próximos meses.
2. **Prescritiva (Motor de Decisão):** Algoritmo que cruza a previsão de consumo com o histórico de preços do PLD (CCEE) e recomenda se a empresa deve ou não travar um contrato de Hedge.
3. **Financeira (Backtesting):** Simulação de cenário passado para calcular o ROI real da estratégia, penalizando o modelo pelos seus próprios erros de previsão (MAPE).

---

## 📐 Premissas do Projeto

- **Proxy de Demanda:** O consumo diário da rede representa **0,05% da Carga Verificada total do submercado SE/CO** via API pública do **ONS**.
- **Variável Alvo (Y):** Volume de Consumo em MWh/dia.
- **Variáveis Exógenas (X):** Temperatura Média (°C) e Calendário (dia da semana + feriados de SP).
- **Preço Spot (Risco):** Histórico real do PLD baixado do portal da **CCEE**.
- **Preço do Contrato (Hedge):** Preço fixo fictício de R$ 160,00/MWh.

---

## 🏗️ Arquitetura da Solução

```
APIs Externas                  Pipeline de Dados              Modelagem & Decisão
─────────────────              ──────────────────             ───────────────────────
ONS API (Carga SE/CO)  ──►    getDataFromAPI.ipynb   ──►    Step6_Prescriptive_
Meteostat API (Temp.)  ──►    (ETL + Feature Eng.)           Analytics.ipynb
CCEE (PLD histórico)   ──►    Foodshop.csv                   │
workalendar (Feriados) ──►                                    ├── Análise Exploratória
                                                              ├── SARIMA / SARIMAX
                                                              ├── XGBoost
                                                              ├── Prophet
                                                              ├── Backtesting Financeiro
                                                              └── Motor Prescritivo
```

---

## 🛠️ Stack Tecnológica

| Categoria | Tecnologias |
|---|---|
| **Linguagem** | Python 3.10 |
| **Manipulação de Dados** | Pandas, NumPy |
| **Séries Temporais** | SARIMA (pmdarima), SARIMAX (statsmodels), Prophet |
| **Machine Learning** | XGBoost, Scikit-learn |
| **Visualização** | Plotly, Matplotlib, Seaborn, Sweetviz |
| **Coleta de Dados** | Requests (ONS API, CCEE), Meteostat, workalendar |
| **Persistência de Modelos** | Joblib |
| **Ambiente** | Jupyter Notebook |
| **Próxima fase** | Databricks, MLflow, PySpark |

---

## 📁 Estrutura do Repositório

```
📦 energy-hedge-prediction/
├── 📂 datasets/
│   └── Foodshop.csv
├── 📂 models/
│   └── best_model_sarima.pkl
├── 📂 notebooks/
│   ├── getDataFromAPI.ipynb
│   └── Step6_Prescriptive_Analytics.ipynb
├── 📂 reports/
│   └── SWEETVIZ_REPORT.html
└── README.md
```

---

## ▶️ Como Executar

### Pré-requisitos

```bash
pip install pandas numpy plotly matplotlib seaborn sweetviz joblib pmdarima
pip install statsmodels xgboost scikit-learn prophet
pip install meteostat geopy workalendar holidays
```

### 1. Coleta de Dados
```bash
jupyter notebook notebooks/getDataFromAPI.ipynb
```

### 2. Pipeline de Modelagem e Decisão
```bash
jupyter notebook notebooks/Step6_Prescriptive_Analytics.ipynb
```

---

## 📊 Resultados e Métricas

### Performance dos Modelos (validação — últimos 90 dias)

| Modelo | MAE (MWh) | MSE | RMSE (MWh) | MAPE |
|---|---|---|---|---|
| Naive (Baseline) | 68,69 | 5962,07 | 77,21 | 11,99% |
| SARIMA | 25,34 | 887,44 | 29,79 | 4,54% |
| SARIMAX + Temperatura | 21,51 | 698,12 | 26,42 | 3,87% |
| SARIMAX + Temp + Feriados | 20,49 | 613,62 | 24,77 | 3,69% |
| Prophet + Temp + Feriados | 16,52 | 452,18 | 21,26 | 2,97% |
| **XGBoost + Lags + Temp + Feriados** | **11,12** | **239,87** | **15,49** | **2,00%** |

> 🏆 **Modelo vencedor: XGBoost + Lags + Temp + Feriados** — reduziu o MAPE de **11,99% (baseline)** para **2,00%**, uma melhoria de ~83% em relação ao modelo ingênuo.

### Dataset

- **Período:** 2000-01-01 a 2026-02-21
- **Linhas:** 9.549 observações diárias
- **Features:** `date`, `consumption_mwh_day`, `temp_mean_c`, `is_holiday`
- **Consumo médio:** 406,76 MWh/dia | **Desvio padrão:** 78,41 MWh

---

## 🚦 Motor de Decisão Prescritivo

| Nível | Sinal | Condição | Ação Sugerida |
|---|---|---|---|
| 0 | 🟢 Verde | Spot < Preço do Contrato | Não travar. Comprar no spot. |
| 1 | 🟡 Amarelo | Spot 1%–15% acima do contrato | Travar Contrato (Margem Fina) |
| 2 | 🟠 Laranja | Spot 16%–50% acima do contrato | Travar Contrato |
| 3 | 🔴 Vermelho | Spot >50% acima do contrato | **TRAVAR CONTRATO AGORA** |

### Fórmula de Poupança (Backtesting)

```
Custo Base = Consumo Real × Preço Spot (PLD CCEE)
Custo IA   = (Previsão IA × Preço Contrato) + [(Consumo Real - Previsão IA) × Preço Spot]
Poupança   = Custo Base - Custo IA
```

---

## 🔭 Próximos Passos

- [ ] Automação do pipeline de captura de dados
- [ ] Retroalimentação com dados reais e retreino contínuo do modelo
- [ ] Migração para Databricks + PySpark
- [ ] MLflow para rastreamento de experimentos e versionamento de modelos
- [ ] Dashboard interativo para recomendações de Hedge em tempo real

---

## 👨‍💻 Autor

**Felipe Henrique da Silva**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-felipehenriquedasilva-blue?logo=linkedin)](https://www.linkedin.com/in/felipehenriquedasilva/)
[![GitHub](https://img.shields.io/badge/GitHub-Felipehsilva-black?logo=github)](https://github.com/Felipehsilva/)

---

> 💡 *Este projeto faz parte do meu portfólio de transição para Data Science, demonstrando aplicação prática de séries temporais, engenharia de features e analytics prescritivo em um problema real de negócio do setor energético.*

