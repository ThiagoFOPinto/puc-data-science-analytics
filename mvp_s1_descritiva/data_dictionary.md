# Dicionário de Dados (Data Dictionary)
Este documento descreve as variáveis contidas no dataset final (**df_gold**) utilizado na análise de literacia financeira e comportamento do investidor.

| Nome da Coluna | Descrição | Fonte | Tipo de Dado |
| :--- | :--- | :--- | :--- |
| **uf** | Sigla da Unidade da Federação (ex: SP, RJ, DF). | IpeaData / IBGE | String |
| **poupanca_total_milhares_rs** | Saldo bruto total em caderneta de poupança (em R$ 1.000). | IpeaData | Float |
| **selic_2023** | Média das taxas Selic mensais acumuladas no ano de 2023. | BCB (SGS) | Float |
| **ipca_2023** | Média das variações mensais do IPCA no ano de 2023. | BCB (SGS) | Float |
| **juro_real** | Taxa real calculada (Selic - IPCA). | Engenharia de Atributos | Float |
| **populacao_habitantes** | População residente (Censo 2022). | API IBGE | Integer |
| **superior_completo_pct** | Percentual da população com ensino superior completo por UF. | PNAD (via CSV externo) | Float |
| **poupanca_per_capita_reais** | Saldo médio de poupança por habitante (valor normalizado em R$). | Engenharia de Atributos | Float |

---
### 🛠 Notas de Processamento:
1. **Sincronia Temporal:** Todos os dados financeiros e educacionais foram filtrados/processados com foco no ano base de **2023**.
2. **Normalização:** A coluna `poupanca_per_capita_reais` foi calculada multiplicando o valor bruto por 1.000 (para converter de milhares para reais) e dividindo pela população total, garantindo uma comparação justa entre UFs de diferentes tamanhos.
3. **Escolaridade:** O dado de ensino superior foi externalizado para o arquivo `escolaridade_uf_pnad.csv` para garantir a reprodutibilidade e auditabilidade do modelo.
