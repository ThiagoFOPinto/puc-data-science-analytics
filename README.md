# ⚓ Inteligência Analítica e Preditiva aplicada à Infraestrutura Portuária Brasileira

Este repositório contém o desenvolvimento dos projetos de conclusão das disciplinas de Data Science & Analytics da PUC-Rio, focados na avaliação da capacidade física, gargalos logísticos e obsolescência da infraestrutura portuária nacional frente ao fenômeno do gigantismo naval.

---

## 🗺️ Organização do Repositório

O projeto está estruturado em módulos independentes e complementares, evoluindo da análise exploratória histórica para a inteligência preditiva de cenários futuros:

* **📂 `mvp_s1_descritiva/`**: Módulo focado na Engenharia e Análise Descritiva de Dados (MVP 1). Realiza o mapeamento histórico do crescimento do porte dos navios (DWT) e sua relação com as restrições físicas dos canais de acesso.
* **📂 `mvp_s2_predictive/`**: Módulo focado em Machine Learning e Modelagem Preditiva (MVP 2). Implementa pipelines preditivos multivariados para projetar a rampa de demanda de calado e identificar pontos de colapso físico e obsolescência operacional.

---

## 🛠️ Tecnologias e Boas Práticas Utilizadas

* **Linguagem principal:** Python 3.12+
* **Ambiente de Desenvolvimento:** GitHub Codespaces & Jupyter Notebooks
* **Bibliotecas de Processamento:** `pandas`, `numpy`
* **Visualização Científica:** `plotly`, `plotly.graph_objects` (Gráficos Interativos Dinâmicos)
* **Machine Learning:** `scikit-learn` (Ridge Regression, Random Forest, GridSearchCV, TimeSeriesSplit)
* **Governança de Dados:** Estratégia de *Snapshot Histórico Homologado* para garantia de reprodutibilidade científica (mitigando instabilidades de APIs públicas externas).

---

## 🧑‍💻 Autor
* **Thiago F. O. Pinto** - Aluno de Pós-Graduação em Data Science & Analytics (PUC-Rio).
