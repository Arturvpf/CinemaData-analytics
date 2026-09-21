# CinemaData-analytics

# CinemaData Analytics

Projeto de Engenharia de Dados desenvolvido no Databricks utilizando PySpark, Spark SQL e Delta Lake.

O objetivo e construir um pipeline de dados end-to-end para dados de filmes, seguindo a Arquitetura Medalhao:

Landing -> Bronze -> Silver -> Gold

## Tecnologias utilizadas

- Databricks
- PySpark
- Spark SQL
- Delta Lake
- Databricks Workflows
- API PTAX do Banco Central

## Arquitetura

### Bronze

O notebook `Landing_to_Bronze.ipynb` realiza a ingestao dos arquivos CSV para a camada Bronze.

Principais responsabilidades:

- leitura dos arquivos de origem;
- preservacao dos dados brutos;
- inclusao da coluna `ingestion_datetime`;
- armazenamento das tabelas em formato Delta utilizando Append;
- consulta da cotacao do dolar atraves da API PTAX do Banco Central.

Tabelas criadas:

- `bronze.tb_movies_info`
- `bronze.tb_movies_financials`
- `bronze.tb_movies_metrics`
- `bronze.tb_credits_and_tags`
- `bronze.tb_movies_reviews`
- `bronze.tb_cotacao_dolar`

### Silver

O notebook `Bronze_to_Silver.ipynb` realiza a limpeza, padronizacao e tipagem dos dados da camada Bronze.

Tabelas criadas:

- `silver.tb_info_filmes`
- `silver.tb_financeiro_filmes`
- `silver.tb_metricas_engajamento`
- `silver.tb_avaliacoes_usuarios`
- `silver.tb_generos`
- `silver.tb_pessoas_empresas`
- `silver.tb_cotacao_dolar`

Entre os principais tratamentos realizados estao:

- deduplicacao de registros;
- tratamento de datas em multiplos formatos;
- normalizacao e traducao de status;
- conversao e validacao de valores numericos;
- conversao de valores financeiros de USD para BRL;
- calculo de lucro e margem de lucro;
- tratamento de notas e metricas de engajamento;
- explosao e normalizacao de generos;
- consolidacao de atores, diretores, roteiristas e produtoras;
- Forward Fill da cotacao do dolar.

## Gold

O notebook `Silver_to_Gold.ipynb` constroi a camada analitica utilizando modelagem dimensional.

### Dimensoes

- `gold.dim_movies`
- `gold.dim_genres`
- `gold.dim_people`
- `gold.dim_companies`
- `gold.dim_reviews`

### Tabela fato

- `gold.fact_movies_performance`

### Tabelas ponte

- `gold.bridge_movie_genre`
- `gold.bridge_movie_person`
- `gold.bridge_movie_company`

### Contexto para IA

Tambem e criada a tabela:

- `gold.gold_genai_movies_context`

Essa tabela consolida informacoes dos filmes em documentos textuais preparados para uso em sistemas de RAG e Vector Search.

## Analytics

O notebook Gold tambem responde as seguintes perguntas de negocio:

1. Receita total dos filmes em BRL.
2. Cinco filmes com maior popularidade.
3. Quantidade de filmes por genero.
4. Dez filmes com maior receita utilizando `RANK()`.
5. Ator com maior quantidade de participacoes nos filmes lancados nos ultimos dois anos.
6. Produtora com maior lucro nos ultimos cinco anos.

## Orquestracao

O pipeline e executado atraves do Databricks Workflow:

`CineData_Medallion_Pipeline`

Fluxo de execucao:

```text
to_Bronze
    |
    v
to_Silver
    |
    v
to_Gold
