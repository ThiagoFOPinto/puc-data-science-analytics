### 📄 Proposta de `README.md` Atualizada e Consolidada

Este documento reúne tudo o que construímos até aqui no **`pipeline_mvp_s3.ipynb`**, registrando os desafios reais de infraestrutura e atendendo rigorosamente à estrutura do edital do MVP da S3:

```markdown
# ⚓ MVP - Engenharia de Dados (S3)
**Pós-Graduação em Ciência de Dados e Analytics — PUC-Rio**  
**Aluno:** Thiago F. O. Pinto  
**Notebook Oficial:** `mvp_s3_data_engineering/pipeline_mvp_s3.ipynb`  
**Plataforma de Nuvem:** Databricks Community Edition (Serverless)  
**Linguagem & Motor de Processamento:** PySpark / Spark SQL / Delta Lake  

---

## 1. Contexto de Negócio e Objetivos

Este projeto aplica os fundamentos de Engenharia de Dados na nuvem para construir uma infraestrutura analítica voltada ao setor portuário brasileiro. A partir de dados de infraestrutura e operações portuárias, o pipeline consolida uma fonte única da verdade para apoiar decisões sobre capacidade física, logística e demanda de calado.

### 🎯 Perguntas de Negócio
1. **Distribuição e Capacidade Operacional:** Qual é a distribuição dos complexos portuários e berços por Unidade Federativa (UF)?
2. **Infraestrutura x Porte de Embarcações:** Qual é a relação entre a capacidade física (DWT médio) e a utilização dos complexos portuários?
3. **Concentração Logística:** Quais regiões/portos concentram a maior capacidade operacional para movimentação de granéis e cargas gerais?

---

## 2. Arquitetura da Solução (Arquitetura Medalhão)

A solução foi desenvolvida no **Databricks Community Edition** utilizando o formato de tabela de alto desempenho **Delta Lake**, organizada nas três camadas da **Arquitetura Medalhão**:

```
[GitHub / Raw CSV]
       │
       ▼
┌───────────────────────────┐
│  Camada Bronze (Raw)      │  <- Ingestão bruta + Metadados de Auditoria
│  mvp_s3_bronze.infra_portos│     (180 registros)
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│  Camada Silver (Cleaned)  │  <- Limpeza, Tipagem, Acurácia e Qualidade
│  mvp_s3_silver.infra_portos│     (180 registros validados)
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│  Camada Gold (Modeled)    │  <- Modelagem Dimensional (Star Schema)
│  mvp_s3_gold.fato/dim     │     (Próxima Etapa)
└───────────────────────────┘
```

---

## 3. Carga de Dados e Camada Bronze (`mvp_s3_bronze`)

* **Fonte dos Dados:** Arquivo público `puc_infra_portos.csv` hospedado no repositório GitHub do projeto (`/mvp_s2_predictive/data/`).
* **Desafios Técnicos de Engenharia & Resolução:**
  1. *Problema do FileSystem do Spark (`HttpsFileSystem`):* O leitor nativo `spark.read.csv` não realiza navegação direta via HTTP/HTTPS.
  2. *Restrição de Permissão no Databricks Serverless (`LocalFilesystemAccessDeniedException`):* O ambiente gerenciado bloqueou a gravação intermediária no sistema de arquivos local (`/tmp/`).
  3. *Solução:* Foi construída uma ponte de ingestão híbrida que realiza o download da URL Raw via biblioteca `pandas` em memória e realiza a conversão imediata para `DataFrame` PySpark (`spark.createDataFrame`), garantindo um fluxo ágil e sem travas de segurança.
* **Governança & Rastreabilidade:** Adição das colunas de auditoria `dh_ingestao` (timestamp da carga) e `fonte_dados`.
* **Persistência:** Tabela `mvp_s3_bronze.infra_portos` persistida em formato **Delta Lake** (Total: **180 registros**).

---

## 4. Camada Silver: Limpeza, Tipagem e Qualidade de Dados (`mvp_s3_silver`)

Na Camada Silver, os dados foram submetidos às verificações dos 5 pilares de **Qualidade de Dados**:

1. **Unicidade:** Aplicação de `.dropDuplicates()`. Nenhum registro duplicado foi identificado na base.
2. **Consistência & Padronização:** 
   * Tratamento de espaços e formatação de texto na coluna `porto` via `F.initcap(F.trim())`.
   * Resolução da discrepância do schema entre nomes sugeridos e colunas nativas (`porto`, `dwt_medio`, `ano`, `mes`).
3. **Tipagem Adequada:** Conversão explícita de tipos literais (strings) para tipos numéricos rigorosos:
   * `dwt_medio` \\(\rightarrow\\) `DoubleType()`
   * `ano` e `mes` \\(\rightarrow\\) `IntegerType()`
4. **Acurácia & Outliers:** Aplicação de filtro de integridade domain-driven para garantir que valores de `dwt_medio` sejam nulos ou estritamente não negativos (`dwt_medio >= 0`).
5. **Completude & Auditoria:** Adição do metadado de processamento `dh_processamento_silver`.
* **Resultado:** **180 registros validados** e gravados na tabela `mvp_s3_silver.infra_portos`.

---

## 5. Próximas Etapas (Camada Gold, Catálogo & Autoavaliação)
* [ ] **Modelagem Dimensional (Camada Gold):** Criação das tabelas de dimensão (`dim_porto`, `dim_tempo`) e da tabela fato (`fato_infra_operacional`).
* [ ] **Catálogo de Dados:** Transcrição de esquemas e screenshots do painel *Catalog* do Databricks.
* [ ] **Análise SQL:** Resposta analítica às 3 perguntas de negócio via Spark SQL.
* [ ] **Autoavaliação:** Reflexão sobre facilidades, dificuldades e aprendizados com o ecossistema PySpark/Databricks.