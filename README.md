# ⚡ Strategic Energy Acquisition Predictive System (Hedge)

[PT-BR](#pt-br) | [EN](#en)

👉 **Full project explanation available on my blog:**  
🔗 [Click here](https://dataineverywhere.ct.ws/projects/energy-hedge-strategy-how-a-predictive-system-protects-millions-in-energy-costs/)

[![YouTube](https://img.shields.io/badge/YouTube-Project%20Demo-red?logo=youtube)](https://www.youtube.com/watch?v=GKcSGRexRgw)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![Python](https://img.shields.io/badge/Python-3.10-blue)
![License](https://img.shields.io/badge/License-MIT-green)

---

## PT-BR

> **Projeto de Data Science end-to-end** que combina previsão de série temporal com um motor de decisão prescritivo para proteger empresas de varejo contra a volatilidade do Mercado Livre de Energia elétrica.

## 📌 Sumário

- [O Problema de Negócio](#-o-problema-de-negócio)
- [Solução Proposta](#-solução-proposta)
- [Premissas do Projeto](#-premissas-do-projeto)
- [Arquitetura da Solução](#️-arquitetura-da-solução)
- [Stack Tecnológica](#️-stack-tecnológica)
- [Estrutura do Repositório](#-estrutura-do-repositório)
- [Como Executar](#️-como-executar)
- [Resultados e Métricas](#-resultados-e-métricas)
- [Motor de Decisão Prescritivo](#-motor-de-decisão-prescritivo)
- [Migração para Databricks](#-migração-para-databricks)
- [Próximos Passos](#-próximos-passos)
- [Autor](#-autor)
- [English Version](#en)

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

```text
APIs Externas                  Pipeline de Dados              Modelagem & Decisão
─────────────────              ──────────────────             ───────────────────────
ONS API (Carga SE/CO)  ──►    getDataFromAPI.ipynb   ──►    Step6_Prescriptive_Analytics.ipynb
Meteostat API (Temp.)  ──►    (ETL + Feature Eng.)           │
CCEE (PLD histórico)   ──►    Foodshop.csv                   ├── Análise Exploratória
workalendar (Feriados) ──►                                    ├── SARIMA / SARIMAX
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

```text
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
|---|---:|---:|---:|---:|
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
- **Consumo médio:** 406,76 MWh/dia
- **Desvio padrão:** 78,41 MWh

---

## 🚦 Motor de Decisão Prescritivo

| Nível | Sinal | Condição | Ação Sugerida |
|---|---|---|---|
| 0 | 🟢 Verde | Spot < Preço do Contrato | Não travar. Comprar no spot. |
| 1 | 🟡 Amarelo | Spot 1%–15% acima do contrato | Travar Contrato (Margem Fina) |
| 2 | 🟠 Laranja | Spot 16%–50% acima do contrato | Travar Contrato |
| 3 | 🔴 Vermelho | Spot > 50% acima do contrato | **TRAVAR CONTRATO AGORA** |

### Fórmula de Poupança (Backtesting)

```text
Custo Base = Consumo Real × Preço Spot (PLD CCEE)
Custo IA   = (Previsão IA × Preço Contrato) + [(Consumo Real - Previsão IA) × Preço Spot]
Poupança   = Custo Base - Custo IA
```

---

## 🧱 Migração para Databricks

O projeto também foi estruturado no ambiente **Databricks**, utilizando **PySpark** para processamento de dados em maior escala e **MLflow** para gestão do ciclo de vida de Machine Learning.

### 🛠️ Ingestão e Tratamento de Dados (`getDataFromAPI_PySpark.ipynb`)

- **Extração via API:** Automação da coleta de dados de Carga Verificada do submercado SE/CO através da API pública do ONS.
- **Processamento de Proxy:** Implementação da técnica de escala Top-Down para gerar a curva de carga da rede varejista.
- **Regra dos 0,05%:** O consumo diário da rede Foodshop é calculado como exatamente 0,05% da carga total verificada do submercado SE/CO.
- **Sazonalidade e Clima:** A metodologia considera feriados, variações climáticas e sazonalidades regionais.

### 🤖 Pipeline de Machine Learning e Tracking (`XGBoost_ML_Pipeline.ipynb`)

- **Modelagem Multivariada:** Utilização do algoritmo **XGBoost** em ambiente PySpark para prever exclusivamente o volume de consumo (MWh).
- **Variáveis Exógenas (X):** O consumo é modelado considerando Temperatura Média/Máxima e Calendário (dias da semana e feriados).
- **Gestão com MLflow:** Rastreamento de experimentos e monitoramento de métricas de performance.
- **Escopo da IA:** O modelo é restrito à previsão de volume e não realiza previsão de preço de energia.

### 📊 Motor Prescritivo e Análise de Impacto (`Financial_Impact.ipynb`)

- **Matriz de Decisão:** Algoritmo que automatiza a recomendação de compra com base em uma matriz de risco parametrizada no código.
- **Níveis de Recomendação:** Classificação da urgência em quatro níveis, do Mercado Spot (Nível 0) ao Alerta Crítico (Nível 3), com base na diferença entre o preço Spot e o contrato fixo.
- **Backtesting Financeiro:** Simulação de cenários passados utilizando o histórico real do PLD da CCEE.
- **Hedge Travado:** Uso de um preço fixo fictício de R$ 160,00/MWh para validar a estratégia de proteção contra a volatilidade.
- **Cálculo de Saving:** Mensuração do sucesso através de *cost avoidance*, em que a poupança final é a diferença entre o Custo Base e o Custo IA.
- **Penalização por Erro:** O cálculo financeiro penaliza o modelo por erros de previsão (MAPE), integrando o custo de compra de déficit no mercado Spot.

### 4. Tecnologias Utilizadas

- **Processamento:** Databricks e PySpark.
- **Machine Learning:** XGBoost e MLflow.
- **Fontes de Dados:** ONS (Carga SE/CO) e CCEE (Preço Spot PLD).

---

## 🔭 Próximos Passos

- [ ] Criar Dashboard Interativo

---

## 👨‍💻 Autor

**Felipe Henrique da Silva**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-felipehenriquedasilva-blue?logo=linkedin)](https://www.linkedin.com/in/felipehenriquedasilva/)
[![GitHub](https://img.shields.io/badge/GitHub-Felipehsilva-black?logo=github)](https://github.com/Felipehsilva/)

---

## EN

> **End-to-end Data Science project** that combines time series forecasting with a prescriptive decision engine to protect retail companies against volatility in Brazil's Free Energy Market.

## Table of Contents

- [Business Problem](#-business-problem)
- [Proposed Solution](#-proposed-solution)
- [Project Assumptions](#-project-assumptions)
- [Solution Architecture](#️-solution-architecture)
- [Technology Stack](#️-technology-stack)
- [Repository Structure](#-repository-structure)
- [How to Run](#️-how-to-run)
- [Results and Metrics](#-results-and-metrics)
- [Prescriptive Decision Engine](#-prescriptive-decision-engine)
- [Databricks Migration](#-databricks-migration)
- [Next Steps](#-next-steps)
- [Author](#-author)

---

## 🏭 Business Problem

Supermarket chains are among the largest electricity consumers in retail, and energy represents one of the **largest operating costs** for these companies.

In Brazil's **Free Energy Market**, fluctuations in the **PLD (Settlement Price for Differences)** create severe financial risk. Traditional reactive purchasing strategies expose the company to price spikes during dry periods, when the cost per MWh can **quadruple**.

**Simulated company:** Retail chain *Foodshop* — 50 large stores in São Paulo/SP.

| Metric | Value |
|---|---|
| Average consumption per store/day | 406 MWh |
| Total network consumption/month | ~12,180 MWh |
| Estimated monthly expense | R$ 2,436,000.00 |
| Estimated annual expense | ~R$ 29.2 million |

---

## 💡 Proposed Solution

An end-to-end **Analytics** system with three layers:

1. **Predictive (AI):** A time series model that forecasts energy consumption volume (MWh) for the coming months.
2. **Prescriptive (Decision Engine):** An algorithm that combines consumption forecasts with historical PLD prices and recommends whether the company should lock in a hedge contract.
3. **Financial (Backtesting):** A historical simulation that calculates the real ROI of the strategy while penalizing the model for its own forecast errors (MAPE).

---

## 📐 Project Assumptions

- **Demand Proxy:** The network's daily consumption represents **0.05% of the total verified load of the SE/CO submarket** through the public **ONS** API.
- **Target Variable (Y):** Consumption volume in MWh/day.
- **Exogenous Variables (X):** Average temperature (°C) and calendar effects (weekday + São Paulo holidays).
- **Spot Price (Risk):** Real PLD historical data obtained from the **CCEE** portal.
- **Contract Price (Hedge):** Fictional fixed contract price of R$ 160.00/MWh.

---

## 🏗️ Solution Architecture

```text
External APIs                 Data Pipeline                  Modeling & Decision
─────────────                 ─────────────                  ───────────────────
ONS API (SE/CO Load)  ──►     getDataFromAPI.ipynb   ──►    Step6_Prescriptive_Analytics.ipynb
Meteostat API (Temp.) ──►     (ETL + Feature Eng.)          │
CCEE (PLD history)    ──►     Foodshop.csv                  ├── Exploratory Analysis
workalendar (Holidays) ──►                                 ├── SARIMA / SARIMAX
                                                           ├── XGBoost
                                                           ├── Prophet
                                                           ├── Financial Backtesting
                                                           └── Prescriptive Engine
```

---

## 🛠️ Technology Stack

| Category | Technologies |
|---|---|
| **Language** | Python 3.10 |
| **Data Manipulation** | Pandas, NumPy |
| **Time Series** | SARIMA (pmdarima), SARIMAX (statsmodels), Prophet |
| **Machine Learning** | XGBoost, Scikit-learn |
| **Visualization** | Plotly, Matplotlib, Seaborn, Sweetviz |
| **Data Collection** | Requests (ONS API, CCEE), Meteostat, workalendar |
| **Model Persistence** | Joblib |
| **Environment** | Jupyter Notebook |
| **Next phase** | Databricks, MLflow, PySpark |

---

## 📁 Repository Structure

```text
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

## ▶️ How to Run

### Requirements

```bash
pip install pandas numpy plotly matplotlib seaborn sweetviz joblib pmdarima
pip install statsmodels xgboost scikit-learn prophet
pip install meteostat geopy workalendar holidays
```

### 1. Data Collection

```bash
jupyter notebook notebooks/getDataFromAPI.ipynb
```

### 2. Modeling and Decision Pipeline

```bash
jupyter notebook notebooks/Step6_Prescriptive_Analytics.ipynb
```

---

## 📊 Results and Metrics

### Model Performance (validation — last 90 days)

| Model | MAE (MWh) | MSE | RMSE (MWh) | MAPE |
|---|---:|---:|---:|---:|
| Naive (Baseline) | 68.69 | 5962.07 | 77.21 | 11.99% |
| SARIMA | 25.34 | 887.44 | 29.79 | 4.54% |
| SARIMAX + Temperature | 21.51 | 698.12 | 26.42 | 3.87% |
| SARIMAX + Temp + Holidays | 20.49 | 613.62 | 24.77 | 3.69% |
| Prophet + Temp + Holidays | 16.52 | 452.18 | 21.26 | 2.97% |
| **XGBoost + Lags + Temp + Holidays** | **11.12** | **239.87** | **15.49** | **2.00%** |

> 🏆 **Winning model:** XGBoost + Lags + Temp + Holidays — reduced MAPE from **11.99% (baseline)** to **2.00%**, an improvement of approximately **83%** over the naive model.

### Dataset

- **Period:** 2000-01-01 to 2026-02-21
- **Rows:** 9,549 daily observations
- **Features:** `date`, `consumption_mwh_day`, `temp_mean_c`, `is_holiday`
- **Average consumption:** 406.76 MWh/day
- **Standard deviation:** 78.41 MWh

---

## 🚦 Prescriptive Decision Engine

| Level | Signal | Condition | Suggested Action |
|---|---|---|---|
| 0 | 🟢 Green | Spot < Contract Price | Do not lock. Buy on the spot market. |
| 1 | 🟡 Yellow | Spot 1%–15% above contract | Lock Contract (Thin Margin) |
| 2 | 🟠 Orange | Spot 16%–50% above contract | Lock Contract |
| 3 | 🔴 Red | Spot > 50% above contract | **LOCK CONTRACT NOW** |

### Savings Formula (Backtesting)

```text
Base Cost = Actual Consumption × Spot Price (CCEE PLD)
AI Cost   = (AI Forecast × Contract Price) + [(Actual Consumption - AI Forecast) × Spot Price]
Savings   = Base Cost - AI Cost
```

---

## 🧱 Databricks Migration

The project was also structured in a **Databricks** environment, using **PySpark** for large-scale data processing and **MLflow** for machine learning lifecycle management.

### 🛠️ Data Ingestion and Processing (`getDataFromAPI_PySpark.ipynb`)

- **API Extraction:** Automated collection of verified load data from the SE/CO submarket through the public ONS API.
- **Proxy Processing:** Implementation of a Top-Down scaling technique to generate the retail chain load curve.
- **0.05% Rule:** Foodshop's daily consumption is calculated as exactly 0.05% of the total verified SE/CO submarket load.
- **Seasonality and Weather:** The methodology incorporates holidays, climate variation, and regional seasonal patterns.

### 🤖 Machine Learning Pipeline and Tracking (`XGBoost_ML_Pipeline.ipynb`)

- **Multivariate Modeling:** Use of **XGBoost** in a PySpark environment to forecast consumption volume (MWh).
- **Exogenous Variables (X):** Consumption is modeled using average/maximum temperature and calendar features (weekdays and holidays).
- **MLflow Management:** Experiment tracking and performance metric monitoring.
- **AI Scope:** The model is limited to volume forecasting and does not predict energy prices.

### 📊 Prescriptive Engine and Impact Analysis (`Financial_Impact.ipynb`)

- **Decision Matrix:** Algorithm that automates purchasing recommendations based on a risk matrix parameterized in code.
- **Recommendation Levels:** Urgency is classified into four levels, from Spot Market (Level 0) to Critical Alert (Level 3), based on the gap between spot price and fixed contract price.
- **Financial Backtesting:** Historical scenario simulation using real PLD data from CCEE.
- **Locked Hedge:** Use of a fictional fixed contract price of R$ 160.00/MWh to validate the protection strategy.
- **Savings Calculation:** Success is measured through *cost avoidance*, where final savings are the difference between Base Cost and AI Cost.
- **Error Penalization:** Financial calculations penalize forecast errors (MAPE), incorporating the cost of deficit purchases in the spot market.

### 4. Technologies Used

- **Processing:** Databricks and PySpark.
- **Machine Learning:** XGBoost and MLflow.
- **Data Sources:** ONS (SE/CO Load) and CCEE (PLD Spot Price).

---

## 🔭 Next Steps

- [ ] Build Interactive Dashboard

---

## 👨‍💻 Author

**Felipe Henrique da Silva**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-felipehenriquedasilva-blue?logo=linkedin)](https://www.linkedin.com/in/felipehenriquedasilva/)
[![GitHub](https://img.shields.io/badge/GitHub-Felipehsilva-black?logo=github)](https://github.com/Felipehsilva/)
