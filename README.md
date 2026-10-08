

# Motor de Decisão e Política de Crédito (Data Lakehouse & PySpark)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Apache Spark](https://img.shields.io/badge/Apache_Spark-E25A1C?style=for-the-badge&logo=apachespark&logoColor=white)
![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white)
![AWS S3](https://img.shields.io/badge/Amazon_S3-569A31?style=for-the-badge&logo=amazons3&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-00ADEE?style=for-the-badge&logo=delta-lake&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)

Este projeto implementa um pipeline end-to-end de Engenharia e Ciência de Dados focado em Risco de Crédito. O objetivo principal é estruturar dados financeiros não padronizados, calcular variáveis comportamentais de risco, treinar um modelo preditivo de inadimplência (Probability of Default - PD) e traduzir os resultados num **Scorecard de Crédito (0-1000)** integrado a um **Motor de Decisão Comercial**.

---

## Arquitetura do Projeto (Medallion Architecture)

O projeto foi construído sobre uma arquitetura de **Data Lakehouse** no Databricks, utilizando a **Arquitetura Medallion**:

```text
[ AWS S3 ] ──> [ BRONZE LAYER ] ──> [ SILVER LAYER ] ──> [ GOLD LAYER ]
 (CSV Raw)      (Raw Delta Tables)   (Feature Store)    (Credit Scorecard)

```

1. **Camada Bronze (Raw):** Ingestão bruta de ficheiros CSV do AWS S3 para tabelas em formato Delta Lake (`bronze_application`, `bronze_bureau`, `bronze_previous_application`, `bronze_installments_payments`).
2. **Camada Silver (Trusted/Features):** Sanitização, deduplicação e agregação de variáveis comportamentais por cliente (`silver_credit_features`). Inclui cálculo de dias de atraso e rácio de contratos ativos.
3. **Camada Gold (Business/Decision):** Execução do modelo preditivo de Regressão Logística, calibração do **Credit Score (0 a 1000)** e classificação automática da política de crédito (`gold_credit_decision`).

---

## Estrutura da Política de Crédito (Motor de Decisão)

O motor de decisão categoriza as propostas de empréstimo com base nas faixas de Score:

| Faixa de Score | Classificação da Política | Ação do Motor |
| --- | --- | --- |
| **850 a 1000** | Risco Muito Baixo | **APROVADO_AUTO** |
| **700 a 849** | Risco Médio | **ANALISE_MANUAL** (Mesa de Crédito) |
| **0 a 699** | Risco Alto | **NEGADO_AUTO** |

---

## Tecnologias e Ferramentas Utilizadas

* **Linguagem:** Python / PySpark
* **Plataforma de Processamento:** Databricks (Serverless / Clusters Distribuídos)
* **Formato de Armazenamento:** Delta Lake (ACID Compliance)
* **Cloud Storage:** AWS S3
* **Machine Learning:** PySpark ML (`VectorAssembler`, `LogisticRegression`, `BinaryClassificationEvaluator`)
* **Métricas Estatísticas de Risco:** WOE (Weight of Evidence), IV (Information Value), AUC-ROC e Coeficiente de Gini.

---

## Estrutura do Repositório

```text
Motor-de-Decisao-e-Politica-de-Credito/
├── .gitignore
├── README.md
├── docs/
│   └── der_modelo_conceitual.png
├── sql/
│   └── 01_create_tables.sql
├── notebooks/
│   ├── 01_data_pipeline_bronze_silver.ipynb
│   └── 02_credit_scorecard_model.ipynb
└── src/
    └── policy_engine.py

```

---

## Como Executar o Projeto

1. Configure as variáveis de ambiente com as suas credenciais AWS no Databricks.
2. Execute o notebook `notebooks/01_data_pipeline_bronze_silver.ipynb` para estruturar as camadas Bronze e Silver.
3. Execute o notebook `notebooks/02_credit_scorecard_model.ipynb` para treinar o modelo, gerar o Scorecard e popular a camada Gold.

