# puc-data-science-analytics
Data Science &amp; Analytics portfolio | Projetos e MVPs da pós-graduação da PUC

# MVP: Impacto da Literacia Financeira na Alocação de Ativos (Poupança)

## Descrição do Projeto
Este projeto analisa se o nível de instrução (escolaridade) de cada estado brasileiro influencia a velocidade de reação e o comportamento do investidor em relação à caderneta de poupança, especialmente em cenários de Juro Real positivo. 

O objetivo é validar a hipótese de que estados com maior literacia financeira (Ensino Superior) tendem a otimizar melhor o capital, migrando recursos para ativos mais rentáveis quando a Selic sobe.

## Tecnologias Utilizadas
* **Python 3.x**
* **Pandas**: Limpeza e manipulação de dados.
* **Plotly**: Visualizações interativas.
* **Requests**: Consumo de API REST (IBGE).
* **Statsmodels**: Regressão linear para linha de tendência.

## Fontes de Dados
1. **Banco Central (SGS)**: Séries temporais de Selic e IPCA.
2. **IpeaData**: Saldo de poupança por município/UF (2023).
3. **IBGE (API)**: População residente (Censo 2022) para normalização per capita.
4. **PNAD (IBGE)**: Percentual de ensino superior completo por UF.

## Como Executar o Projeto
1. Clone este repositório.
2. Abra o ficheiro `.ipynb` no Google Colab ou Jupyter Notebook.
3. Execute as células em ordem. Os dados macro e demográficos são consumidos via link direto/API, garantindo a reprodutibilidade.

## Principais Insights
* A normalização **per capita** revelou que o Distrito Federal possui a maior reserva por habitante, mas também a maior taxa de literacia.
* A correlação via **Regressão Linear (OLS)** demonstra que a escolaridade é um preditor direto do acúmulo de capital.
* Estados com alta escolaridade apresentam maior volatilidade na poupança, sugerindo uma gestão de portfólio mais ativa.

Para detalhes sobre as variáveis, tipos de dados e definições técnicas, consulte o nosso [Catálogo de Dados](./data_dictionary.md).

---
**Autor:** Thiago F. O. Pinto  
**Instituição:** PUC - Data Science & Analytics
