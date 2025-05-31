# Análise de Dados com Azure Databricks e Apache Spark 🚀

Bem-vindo a este guia sobre como realizar análises de dados poderosas utilizando Azure Databricks e Apache Spark. Este README tem como objetivo fornecer uma visão didática do processo, desde a configuração inicial até a geração de insights valiosos.

![Logo Azure Databricks](https://upload.wikimedia.org/wikipedia/commons/thumb/6/63/Databricks_Logo.png/320px-Databricks_Logo.png)
![Logo Apache Spark](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f3/Apache_Spark_logo.svg/320px-Apache_Spark_logo.svg.png)

---

## 📜 Índice

1.  [O que é Azure Databricks?](#o-que-é-azure-databricks)
2.  [O que é Apache Spark?](#o-que-é-apache-spark)
3.  [Fluxo de Trabalho Típico para Análise de Dados](#fluxo-de-trabalho-típico-para-análise-de-dados)
    * [Passo 1: Configuração do Ambiente](#passo-1-configuração-do-ambiente-no-azure-databricks)
    * [Passo 2: Ingestão de Dados](#passo-2-ingestão-de-dados)
    * [Passo 3: Exploração e Limpeza de Dados (EDA)](#passo-3-exploração-e-limpeza-de-dados-eda)
    * [Passo 4: Transformação e Análise de Dados](#passo-4-transformação-e-análise-de-dados)
    * [Passo 5: Visualização de Dados](#passo-5-visualização-de-dados)
4.  [Exemplo Prático Simplificado (PySpark)](#exemplo-prático-simplificado-pyspark)
5.  [💡 Insights e Descobertas](#-insights-e-descobertas)
6.  [🔮 Possibilidades e Próximos Passos](#-possibilidades-e-próximos-passos)
7.  [🛠️ Boas Práticas](#️-boas-práticas)

## O que é Azure Databricks?

Azure Databricks é uma plataforma de análise de dados otimizada para a nuvem Microsoft Azure. Baseada no Apache Spark, ela fornece um ambiente colaborativo com notebooks interativos, clusters gerenciados e integrações com diversos serviços do Azure, facilitando o processamento de big data, machine learning e análises em tempo real.

* **Colaborativo:** Notebooks podem ser compartilhados e coeditados.
* **Escalável:** Clusters Spark podem ser facilmente dimensionados para cima ou para baixo.
* **Integrado:** Conecta-se nativamente com Azure Data Lake Storage, Blob Storage, Azure Synapse Analytics, etc.

`[Imagem: Arquitetura simplificada do Azure Databricks dentro do ecossistema Azure]`
*Descrição da imagem: Um diagrama mostrando o Azure Databricks se conectando a fontes de dados (como ADLS Gen2) e como os usuários interagem através de notebooks.*

## O que é Apache Spark?

Apache Spark é um poderoso motor de processamento distribuído de código aberto, projetado para velocidade e facilidade de uso. Ele permite processar grandes volumes de dados em paralelo, utilizando conceitos como RDDs (Resilient Distributed Datasets), DataFrames e Datasets.

* **Velocidade:** Processamento em memória e otimizações de query.
* **Versatilidade:** Suporta SQL (Spark SQL), streaming de dados (Spark Streaming), machine learning (MLlib) e processamento de grafos (GraphX).
* **Linguagens:** APIs para Scala, Python (PySpark), Java e R.

## Fluxo de Trabalho Típico para Análise de Dados

Aqui descrevemos um fluxo comum para realizar análises no Azure Databricks.

### Passo 1: Configuração do Ambiente no Azure Databricks

1.  **Criação do Workspace:** No portal do Azure, crie um recurso "Azure Databricks".
2.  **Criação do Cluster:** Dentro do workspace Databricks:
    * Acesse a seção "Compute".
    * Clique em "Create Cluster".
    * Defina um nome para o cluster, a versão do Databricks Runtime (que inclui Spark), o tipo e o número de workers (nós de trabalho) e o tipo de driver.
    * Configure opções como terminação automática por inatividade para economizar custos.

### Passo 2: Ingestão de Dados

Os dados podem residir em diversas fontes. O Azure Databricks facilita a conexão com:

* Azure Data Lake Storage (ADLS Gen1/Gen2)
* Azure Blob Storage
* Bancos de dados SQL e NoSQL
* Serviços de streaming como Kafka ou Event Hubs
* Arquivos (CSV, JSON, Parquet, ORC, etc.)

**Exemplo (PySpark) lendo um CSV do ADLS Gen2 (requer montagem ou configuração de acesso):**

# Supondo que o Data Lake está montado ou credenciais configuradas
caminho_arquivo = "/mnt/meudatalake/dados_brutos/vendas.csv"
```python
df_vendas = spark.read.format("csv") \
    .option("header", "true") \
    .option("inferSchema", "true") \
    .load(caminho_arquivo)
df_vendas.printSchema()
df_vendas.show(5)
```

### Passo 3: Exploração e Limpeza de Dados (EDA)
Antes da análise, é crucial entender e preparar os dados:

* **Verificar tipos de dados:** Use `` `df.printSchema()` ``.
* **Estatísticas descritivas:** Use `` `df.describe().show()` ``.
* **Contar valores nulos:**
    ```python
    from pyspark.sql.functions import col, sum as spark_sum

    df_vendas.select([spark_sum(col(c).isNull().cast("int")).alias(c) for c in df_vendas.columns]).show()
    ```
* **Tratar valores ausentes:** Use `` `df.fillna()` `` ou `` `df.dropna()` ``.
* **Remover duplicatas:** Use `` `df.dropDuplicates()` ``.
* **Filtrar dados:** Use `` `df.filter()` ``.
* **Renomear colunas:** Use `` `df.withColumnRenamed()` ``.

### Passo 4: Transformação e Análise de Dados
Com os dados limpos, podemos começar a extrair informações:

* **Seleção de colunas:** Use `` `df.select("coluna1", "coluna2")` ``.
* **Criação de novas colunas (features):** Use `` `df.withColumn("nova_coluna", expressao)` ``.
* **Agregações:** Use `` `df.groupBy("coluna_agrupadora").agg({"coluna_valor": "sum"})` ``.
* **Junções (Joins):** Use `` `df1.join(df2, df1.id == df2.id, "inner")` ``.
* **Uso de Spark SQL:**
    df_vendas.createOrReplaceTempView("vendas_view")
    ```sql
    SELECT regiao, SUM(valor_venda) as total_vendas 
    FROM vendas_view 
    GROUP BY regiao
    ```
    ```python
    resultado_sql = spark.sql("SELECT regiao, SUM(valor_venda) as total_vendas FROM vendas_view GROUP BY regiao")
    resultado_sql.show()
    ```

### Passo 5: Visualização de Dados
Databricks oferece formas fáceis de visualizar dados diretamente nos notebooks:

* **Comando `display()`:** O Databricks fornece uma função `` `display(dataframe)` `` que automaticamente sugere gráficos.
    ```python
    display(resultado_sql) # Irá gerar uma tabela e opções de gráficos (barras, pizza, etc.)
    ```
* **Bibliotecas Python/R:** Utilize bibliotecas como Matplotlib, Seaborn, Plotly (para Python) ou ggplot2 (para R) para visualizações mais customizadas.

### Exemplo Prático Simplificado (PySpark)
Vamos simular um cenário com dados de vendas.

```python
# Passo 1: (Configuração do ambiente já foi discutida)

# Passo 2: Ingestão de Dados (simulando dados)
from pyspark.sql import Row
data = [
    Row(id_venda=1, produto="Produto A", quantidade=10, valor_unitario=5.0, regiao="Norte"),
    Row(id_venda=2, produto="Produto B", quantidade=5, valor_unitario=10.0, regiao="Sul"),
    Row(id_venda=3, produto="Produto A", quantidade=7, valor_unitario=5.0, regiao="Norte"),
    Row(id_venda=4, produto="Produto C", quantidade=3, valor_unitario=12.0, regiao="Leste"),
    Row(id_venda=5, produto="Produto B", quantidade=8, valor_unitario=10.0, regiao="Sul"),
    Row(id_venda=6, produto="Produto A", quantidade=12, valor_unitario=4.8, regiao="Norte"), # Desconto
]
df_vendas = spark.createDataFrame(data)
print("Dados brutos:")
df_vendas.show()

# Passo 3: Exploração e Limpeza (simples, sem nulos neste exemplo)
df_vendas.printSchema()

# Passo 4: Transformação e Análise
from pyspark.sql.functions import col, round

# Calcular valor total da venda
df_com_total = df_vendas.withColumn("valor_total_venda", round(col("quantidade") * col("valor_unitario"), 2))
print("Dados com valor total da venda:")
df_com_total.show()

# Agregação: Total de vendas por produto
df_vendas_por_produto = df_com_total.groupBy("produto") \
    .agg({"valor_total_venda": "sum", "quantidade": "sum"}) \
    .withColumnRenamed("sum(valor_total_venda)", "receita_total_produto") \
    .withColumnRenamed("sum(quantidade)", "unidades_vendidas_produto") \
    .orderBy(col("receita_total_produto").desc())

print("Receita total e unidades por produto:")
df_vendas_por_produto.show()

# Agregação: Total de vendas por região
df_vendas_por_regiao = df_com_total.groupBy("regiao") \
    .sum("valor_total_venda") \
    .withColumnRenamed("sum(valor_total_venda)", "receita_total_regiao") \
    .orderBy(col("receita_total_regiao").desc())

print("Receita total por região:")
df_vendas_por_regiao.show()

# Passo 5: Visualização
print("\n--- Visualizações (usar display() no Databricks) ---")
# No Databricks, você usaria:
# display(df_vendas_por_produto)
# display(df_vendas_por_regiao)
```

### 💡 Insights e Descobertas
A partir da análise de dados, podemos obter insights como:

* Produtos mais vendidos: Identificar quais produtos geram maior receita ou têm maior volume de vendas (ex: "Produto A" no nosso exemplo).
* Desempenho regional: Quais regiões geográficas apresentam melhor/pior desempenho em vendas.
* Tendências de vendas: Ao analisar dados ao longo do tempo (não coberto neste exemplo simples), pode-se identificar sazonalidades ou crescimento/declínio nas vendas.
* Segmentação de clientes: Com dados de clientes, poderíamos agrupá-los por comportamento de compra.
* Detecção de anomalias: Vendas incomuns, valores muito discrepantes que podem indicar erros ou fraudes.
* Correlações: Relação entre diferentesáveis (ex: investimento em marketing vs. vendas).

### 🔮 Possibilidades e Próximos Passos
Azure Databricks e Spark abrem um leque de possibilidades:

* **Machine Learning:**
    * Treinar modelos de previsão de vendas (regressão).
    * Criar sistemas de recomendação de produtos.
    * Detectar fraudes com modelos de classificação.
    * Segmentar clientes com algoritmos de clustering (K-Means).
    * Utilizar a biblioteca `` `Spark MLlib` ``.
* **Análise de Dados em Tempo Real (Streaming):**
    * Integrar com Azure Event Hubs ou Kafka para processar dados à medida que chegam.
    * Monitorar atividades em tempo real, como transações financeiras ou logs de aplicação.
* **Pipelines de Dados Automatizados:**
    * Usar Azure Data Factory para orquestrar e agendar a execução de notebooks Databricks.
* **Integração com Ferramentas de BI:**
    * Conectar Power BI, Tableau ou outras ferramentas de BI aos resultados da análise no Databricks para criar dashboards interativos e relatórios.
* **Governança de Dados:**
    * Utilizar o Unity Catalog no Databricks para gerenciamento centralizado de dados, metadados, segurança e linhagem.
* **Análise de Grafos:**
    * Utilizar GraphX para analisar relações complexas, como redes sociais ou cadeias de suprimentos.

### 🛠️ Boas Práticas
* **Comente seu código:** Facilita a compreensão e manutenção.
* **Otimize queries Spark:**
    * Use o formato Parquet para armazenamento (colunar e eficiente).
    * Evite `` `collect()` `` em DataFrames grandes para o driver.
    * Use `` `cache()` `` ou `` `persist()` `` para DataFrames que serão reutilizados.
    * Filtre e selecione dados o mais cedo possível no pipeline.
* **Gerenciamento de Clusters:**
    * Use clusters com auto-scaling para ajustar os recursos à carga de trabalho.
    * Configure a terminação automática para economizar custos.
    * Escolha os tipos de VM adequados para suas cargas de trabalho (memória otimizada, computação otimizada).
* **Segurança:**
    * Gerencie o acesso aos dados e notebooks usando as funcionalidades do Databricks e do Azure (RBAC, Azure Key Vault para segredos).
* **Organização de Notebooks:**
    * Mantenha uma estrutura lógica e divida tarefas complexas em múltiplos notebooks ou funções.
* **Versionamento:**
    * Utilize Git para versionar seus notebooks (Databricks Repos).
