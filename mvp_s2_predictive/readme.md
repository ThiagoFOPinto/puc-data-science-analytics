# 📈 MVP 2: Modelagem Preditiva de Calado e Risco de Obsolescência Portuária

Este módulo aplica algoritmos de Machine Learning para projetar a tendência de demanda de calado nos três principais complexos portuários do país (**Santos, Paranaguá e Itajaí**) para o horizonte futuro de 2026 a 2028, confrontando os resultados com os limites físicos homologados pelas autoridades portuárias.

---

## 🔬 Metodologia Científica e Defesa do Pipeline

Para garantir a máxima blindagem técnica do MVP e cumprir as diretrizes acadêmicas de avaliação, o pipeline foi desenhado sob os seguintes critérios:

1.  **Governança e Reprodutibilidade:** Devido à instabilidade crônica dos endpoints públicos do governo (`dados.gov.br`), adotou-se a estratégia de **Snapshot Histórico Homologado** (`puc_infra_portos.csv`), assegurando que qualquer auditor replique o experimento com os mesmos dados primários oficiais da ANTAQ/APPA.
2.  **Estratégia de Validação Temporal:** Séries temporais possuem dependência sequencial. Para evitar vazamento de dados (*data leakage*), mitigou-se o uso de K-Fold tradicional, aplicando-se uma divisão cronológica estrita (80% Treino / 20% Teste) e validação cruzada móvel via **`TimeSeriesSplit`**.
3.  **Avaliação de Cenários (Modelos Competidores):**
    * **Baseline (Ingênuo):** Média móvel dos últimos 12 meses como régua mínima de desempenho.
    * **Cenário 1 (Linear):** Regressão Ridge com regularização L2 (Modelo Campeão, capaz de extrapolar a rampa de tendência contínua).
    * **Cenário 2 (Não-Linear):** Random Forest Regressor (Avaliado e documentado sua falha conceitual em extrapolar valores fora do limite superior de treino).
4.  **Otimização:** Ajuste fino do hiperparâmetro `alpha` do modelo Ridge via `GridSearchCV`.

---

## 📊 Estrutura de Arquivos do Módulo

* **📂 `notebooks/`**: Contém o `notebook_s2.ipynb`, o relatório autodocumentado com todas as etapas de treino, tabelas comparativas de métricas (MAE, RMSE, R²) e análises visuais.
* **📂 `data/`**: Snapshot bruto homologado contendo as séries históricas mensais granulares de 2021 a 2025.
* **📂 `src/`**: Scripts e dependências auxiliares do projeto.
* **📄 `requirements.txt`**: Declaração explícita de todas as bibliotecas e versões do ambiente de produção.

---

## 🌡️ Entregáveis de Negócio (Outputs)

O modelo estende a previsão **mês a mês (sem concentrar dados)**, gerando dois artefatos visuais de alta relevância executiva:
* **Gráficos de Intersecção de Conflito:** Demonstração visual do exato momento cronológico onde a projeção do calado intercepta o teto físico portuário.
* **Heatmap de Risco Portuário:** Matriz de decisão categórica que classifica mensalmente o nível de obsolescência logística (Verde, Amarelo e Vermelho) para suporte à tomada de decisão de investimentos em dragagem ou expansão infraestrutural.
