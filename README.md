# Economic Data Lakehouse — Brazilian Economic Indicators

Projeto de Engenharia de Dados desenvolvido com **Databricks, PySpark e Delta Lake**, com o objetivo de construir um Lakehouse para ingestão, transformação e análise de indicadores econômicos brasileiros.

Os dados são obtidos da API de séries temporais do Banco Central do Brasil (BCB), utilizando as séries históricas da meta da taxa Selic e da cotação de venda do dólar comercial, referentes ao ano de 2025.

## Objetivo

Construir um pipeline de dados seguindo a arquitetura Medallion (Bronze, Silver e Gold), aplicando conceitos de Engenharia de Dados, como:

- Ingestão de dados via API REST.
- Processamento e transformação com PySpark.
- Armazenamento em formato Delta.
- Limpeza e padronização de dados.
- Integração de diferentes fontes de dados.
- Agregações para análises temporais.
- Validação da qualidade dos dados.

## Arquitetura

O projeto utiliza a arquitetura Medallion, dividida em três camadas:

### Bronze — Ingestão

Responsável pela extração e armazenamento dos dados brutos disponibilizados pela API do Banco Central.

Fontes utilizadas:

- **Meta Selic:** série SGS 432.
- **Dólar comercial — venda:** série SGS 1.

Os dados são coletados utilizando Python e a biblioteca Requests, convertidos para DataFrames e armazenados em tabelas Delta.

Tabelas:
- `bronze_selic`
- `bronze_dolar`

### Silver — Transformação

Responsável pela limpeza e padronização dos dados provenientes da camada Bronze.

Principais transformações:
- Conversão das datas para o tipo `date`.
- Conversão dos valores para o tipo `double`.
- Remoção de registros duplicados.
- Filtragem de registros com datas ou valores nulos.

Tabelas:
- `silver_selic`
- `silver_dolar`

### Gold — Análise

Responsável pela integração dos indicadores e preparação dos dados para análises.

Principais transformações:
- Integração das séries de Selic e dólar por data utilizando `FULL OUTER JOIN`.
- Consolidação da coluna de data com `COALESCE`.
- Preenchimento da Selic com o último valor conhecido (*forward fill*).
- Criação das colunas de ano e mês.
- Cálculo das médias mensais dos indicadores.

Tabelas:
- `gold_indicadores`: indicadores integrados em granularidade diária.
- `gold_indicadores_mensais`: médias mensais da Selic e do dólar.

## Tecnologias utilizadas

- Python
- Requests
- Pandas
- Apache Spark
- PySpark
- Databricks
- Delta Lake
- SQL
- API do Banco Central do Brasil

## Qualidade dos dados

Foram realizadas verificações de qualidade nas tabelas Gold, incluindo:

- Identificação de valores nulos.
- Verificação de registros duplicados.
- Identificação de valores menores ou iguais a zero.
- Conferência dos tipos de dados.
- Validação da consistência entre data, ano e mês.
- Verificação da quantidade de registros.
- Validação da cobertura temporal.
- Verificação de duplicidade e valores nulos na agregação mensal.

### Resultados

| Tabela | Registros |
|---|---:|
| Bronze Selic | 365 |
| Silver Selic | 365 |
| Bronze Dólar | 252 |
| Silver Dólar | 252 |
| Gold diária | 365 |
| Gold mensal | 12 |

A tabela Gold diária contempla o período de 01/01/2025 a 31/12/2025.

Os valores nulos encontrados na cotação do dólar foram mantidos quando não havia cotação disponível, como em finais de semana e feriados. Esses valores não foram substituídos por zero.

## Estrutura do projeto

```text
economic-data-lakehouse/
│
├── notebooks/
│   ├── 01_bronze_ingestion
│   ├── 02_silver_transformation
│   └── 03_gold_analytics
│
└── README.md
```

## Fonte dos dados

Banco Central do Brasil — Sistema Gerenciador de Séries Temporais (SGS).

- [API de dados do Banco Central](https://dadosabertos.bcb.gov.br/)
- [Série SGS 432 — Meta Selic](https://api.bcb.gov.br/dados/serie/bcdata.sgs.432/dados?formato=json&dataInicial=01/01/2025&dataFinal=31/12/2025)
- [Série SGS 1 — Dólar comercial, venda](https://api.bcb.gov.br/dados/serie/bcdata.sgs.1/dados?formato=json&dataInicial=01/01/2025&dataFinal=31/12/2025)

---

**Projeto desenvolvido para fins de aprendizado e prática de Engenharia de Dados.**