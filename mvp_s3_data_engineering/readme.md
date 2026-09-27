# ⚓ MVP - Engenharia de Dados (S3)
**Pós-Graduação em Ciência de Dados e Analytics — PUC-Rio**  
**Aluno:** Thiago F. O. Pinto  
**Notebook Oficial:** `mvp_s3_data_engineering/pipeline_mvp_s3.ipynb`  
**Plataforma de Nuvem:** Databricks Community Edition (Serverless)  
**Linguagem & Motor:** PySpark / Spark SQL / Delta Lake  

---

## 1. Contexto de Negócio e Objetivos

Este projeto aplica os fundamentos de Engenharia de Dados na nuvem para consolidar uma infraestrutura analítica voltada ao setor portuário brasileiro. A partir de dados operacionais e de infraestrutura, a pipeline constrói uma fonte única da verdade (*Single Source of Truth*) para apoiar tomadas de decisão sobre capacidade física, suporte logístico e calado operacional.

### 🎯 Perguntas de Negócio Respondidas
1. **Capacidade Operacional por Porto:** Qual é a capacidade média de porte bruto (DWT) de cada complexo portuário?
2. **Evolução Temporal:** Como variou o DWT médio operacional ao longo dos anos para cada porto?
3. **Sazonalidade Operacional:** Qual é a distribuição da capacidade média operacional agrupada por trimestre?

---

## 2. Arquitetura da Solução (Arquitetura Medalhão)

A solução foi desenvolvida no **Databricks** utilizando o formato de armazenamento **Delta Lake**, estruturada nas três camadas da **Arquitetura Medalhão**:

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
│  mvp_s3_gold.dim_porto    │     (3 portos)
│  mvp_s3_gold.dim_tempo    │     (60 períodos)
│  mvp_s3_gold.fato_infra   │     (180 fatos operacionais)
└───────────────────────────┘
```

---

## 3. Ingestão e Camada Bronze (`mvp_s3_bronze`)

* **Fonte de Dados:** Ficheiro público `puc_infra_portos.csv` hospedado no repositório GitHub do projeto (`/mvp_s2_predictive/data/`).
* **Desafios Técnicos de Engenharia & Solução:**
  1. *Acesso HTTP e Permissão Serverless:* O leitor nativo `spark.read.csv` não realiza navegação direta via HTTP remoto e o ambiente Serverless do Databricks bloqueou a escrita intermediária no diretório local (`LocalFilesystemAccessDeniedException`).
  2. *Arquitetura de Ingestão:* Foi implementada uma ponte de ingestão híbrida carregando o arquivo diretamente via biblioteca `pandas` em memória e convertendo-o para `DataFrame` PySpark (`spark.createDataFrame`), garantindo execução fluida e segura.
* **Governança & Rastreabilidade:** Inclusão dos metadados de auditoria `dh_ingestao` (timestamp do carregamento) e `fonte_dados`.
* **Persistência:** Tabela `mvp_s3_bronze.infra_portos` salva em formato **Delta Lake** (**180 registros**).

---

## 4. Camada Silver: Limpeza e Qualidade de Dados (`mvp_s3_silver`)

Na Camada Silver, os dados foram tratados com base nos 5 pilares de **Qualidade de Dados**:

1. **Unicidade:** Aplicação de `.dropDuplicates()` para eliminação de registros redundantes.
2. **Padronização:** Tratamento de strings e remoção de espaços na coluna de texto via `F.initcap(F.trim())`.
3. **Tipagem Adequada:** Conversão explícita para tipos numéricos rigorosos (`DoubleType` para `dwt_medio`; `IntegerType` para `ano` e `mes`).
4. **Acurácia & Outliers:** Filtro de integridade de domínio para assegurar métricas não negativas (`dwt_medio >= 0`).
5. **Auditoria:** Inclusão do metadado de processamento `dh_processamento_silver`.
* **Resultado:** **180 registros validados** e persistidos na tabela Delta `mvp_s3_silver.infra_portos`.

---

## 5. Camada Gold: Modelagem Dimensional (`mvp_s3_gold`)

A Camada Gold consolida o modelo dimensional em **Esquema Estrela (*Star Schema*)** para suportar consultas analíticas de alta performance:

* **`dim_porto` (Dimensão):** Isola os atributos dos portos únicos e gera a *Surrogate Key* `id_porto` (3 registros).
* **`dim_tempo` (Dimensão):** Mapeia a hierarquia temporal (`ano`, `mes`, `trimestre`, `semestre`) e gera a *Surrogate Key* `id_tempo` (60 registros).
* **`fato_infra_operacional` (Tabela Fato):** Registra as métricas de `dwt_medio` associadas às chaves estrangeiras `id_porto` e `id_tempo` (180 registros).

> 💡 **Nota Técnica sobre Otimização:** Durante a geração das *Surrogate Keys* via funções de janela (`Window.orderBy`), o Spark emitiu um aviso referente à ausência de particionamento (`UserWarning: WARN WindowExpression`). Em tabelas de dimensão de pequeno e médio porte, essa consolidação em partição única é o comportamento normal para garantir o sequenciamento estrito das chaves numéricas sem comprometer o desempenho.

---

## 6. Consultas SQL Analíticas e Resultados

Para responder às perguntas de negócio, foi criada a visão analítica `vw_gold_analytics` unindo a tabela fato às dimensões no Spark SQL:

1. **Capacidade Média por Porto:** Identificação clara da capacidade média geral e máxima de DWT de cada complexo portuário.
2. **Evolução Anual:** Acompanhamento da variação da capacidade média de transporte ao longo dos anos registrados.
3. **Distribuição Trimestral:** Análise da constância e sazonalidade da capacidade operacional ao longo dos trimestres do ano.

---

## 7. Catálogo de Dados e Governança

Todas as tabelas do pipeline foram registradas no Catálogo do Databricks nos schemas correspondentes:
* `mvp_s3_bronze.infra_portos`
* `mvp_s3_silver.infra_portos`
* `mvp_s3_gold.dim_porto`
* `mvp_s3_gold.dim_tempo`
* `mvp_s3_gold.fato_infra_operacional`

Os metadados garantem rastreabilidade (*data lineage*) desde a origem bruta até a camada analítica.

<img width="1918" height="885" alt="image" src="https://github.com/user-attachments/assets/90aac073-e7ce-4fc7-be22-d4700cbb8e0b" />

<img width="1918" height="897" alt="image" src="https://github.com/user-attachments/assets/2b2a494a-64bf-4211-945d-6e25912f63e8" />

<img width="1918" height="916" alt="image" src="https://github.com/user-attachments/assets/a7bd30b1-71d3-4f07-baed-f2e3aa50003f" />
<img width="1918" height="911" alt="image" src="https://github.com/user-attachments/assets/2c84344f-dc97-4ac4-a47f-ccf4fbedba03" />

---

## 8. Autoavaliação e Trabalhos Futuros

### 🔹 Aprendizados e Dificuldades Contornadas
* **Integração com o Databricks:** A configuração de credenciais via Git Token no Databricks permitiu uma gestão de versão ágil e integrada ao GitHub.
* **Resolução de Problemas de Infraestrutura:** O contorno das limitações de leitura remota no ambiente Serverless via Pandas representou um aprendizado prático importante sobre arquitetura de dados na nuvem.

### 🔹 Reflexão Arquitetural: Monolítico vs. Desacoplado
Neste MVP, o pipeline foi construído de forma integrada no notebook `pipeline_mvp_s3.ipynb` para facilitar a execução sequencial e a validação acadêmica ponta a ponta. 

No entanto, em um **ambiente de produção corporativo**, a boa prática de Engenharia de Dados dita o **desacoplamento em jobs independentes**:
* Ingestão Bronze executada por agendamento continuo.
* Tratamento Silver acionado por gatilhos de chegada de dados (*event-driven*).
* Modelagem Gold orquestrada via **Databricks Workflows**, **Apache Airflow** ou **dbt**.

Essa separação garante isolamento de falhas, reexecução modular e economias de computação em grande escala.
