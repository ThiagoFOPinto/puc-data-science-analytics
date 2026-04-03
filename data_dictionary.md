# Dicionário de Dados (Data Dictionary)

Este documento descreve as variáveis utilizadas no projeto de análise de literacia financeira e poupança por UF (2023).

| Nome da Coluna | Descrição | Fonte | Tipo |
| :--- | :--- | :--- | :--- |
| **uf** | Sigla da Unidade da Federação (Ex: SP, RJ, DF) | IpeaData / IBGE | String |
| **data** | Período de referência (Mês/Ano) | BCB (SGS) | Datetime |
| **selic** | Taxa Selic acumulada no mês (%) | BCB (SGS) | Float |
| **ipca** | Inflação oficial medida pelo IPCA (%) | BCB (SGS) | Float |
| **juro_real** | Diferença entre Selic e IPCA (Taxa Real) | Calculado (Eng. Atributos) | Float |
| **poupanca_total_milhares_rs** | Saldo bruto total em poupança (em R$ 1.000) | IpeaData | Float |
| **populacao_habitantes** | População residente estimada | API IBGE (Censo 2022) | Integer |
| **poupanca_per_capita_reais** | Valor médio de poupança por habitante (R$) | Calculado (Normalização) | Float |
| **superior_completo_pct** | % da população com ensino superior completo | PNAD / IBGE | Float |

---
**Nota técnica:** Os dados de poupança foram agregados de nível municipal para estadual antes do cálculo per capita.
