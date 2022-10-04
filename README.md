# teste_bix_tecnologia
## Desafio criar pipeline
### Tabela de conteúdos
=================
<!--ts-->
  * [Tecnologias utilizadas](#Tecnologias)
  * [Arquitetura GCP](#Arquitetura-GCP)
  * [Análise dos dados(Ambiente de desenvolvimento)](#Análise-dos-dados)
    * [Item 1](#Item-1)
    * [Item 2](#Item-2)
    * [Item 3](#Item-3)
    * [Item 4](#Item-4)
  * GCP(Ambiente de produção)
    * [Cloud Function](#Cloud-Function)
    * [Cloud Storage](#Cloud-Storage)
    * [Cloud Scheduler](#Cloud-Scheduler)
    * [BigQuery](#BigQuery) 
<!--te-->
### Tecnologias

As seguintes ferramentas foram usadas na resolução dos questionamentos:

- Jupyter
- Anaconda
- Pandas 
- Python
- Drawio
- Google Cloud Platform

### Análise dos dados(ambiente de desenvolvimento)
### jupyter notebook
```python
import pandas as pd
import psycopg2 as pg
import pandas.io.sql as psql
import datetime as DT
from datetime import date, timedelta
import pytz
import requests
```
# Item 1
### 1. Dataset como fonte de dados tabela do postgressql
```python
#Gera a data e hora de São Paulo Brasil
tz_BR = pytz.timezone('America/Sao_Paulo')
#Gera a data atual em formato Timestamp
start = DT.datetime.strftime(DT.datetime.now(tz_BR),'%Y-%m-%d %H:%M:%S')
print("Processo iniciado em: "+start)

#PostgreSQL:
#•	Host: 34.173.103.16
#•	User: junior
#•	Password: |?7LXmg+FWL&,2(
#•	Port: 5432
#•	Database: postgres
#•	Tabela: public.venda

#Inicia a configuração para conexão com as tabelas do postgressql
connection = pg.connect("host=34.173.103.16 dbname=postgres user=junior password=|?7LXmg+FWL&,2(")

#Executa Query para retornar o resultado e armazenar um Dataframe Pandas
#Query com solução para os dados solicitados
QUERY = """SELECT * FROM public.venda"""
print(QUERY)
df_postgressql_pd = psql.read_sql_query(QUERY, connection)
end_consulta = DT.datetime.strftime(DT.datetime.now(tz_BR),'%Y-%m-%d %H:%M:%S')
print("Consulta realizada com Sucesso: "+end_consulta)
df_postgressql_pd
```
# Item 2 
### 2. Dataset como fonte de dados a API fornecida
```python
#comando para chamar a api e armazenar em um dataframe pandas
start = DT.datetime.strftime(DT.datetime.now(tz_BR),'%Y-%m-%d %H:%M:%S')
print("Processo iniciado em: "+start)
df_funcionarios = pd.DataFrame()
for number in range(1, 10):
    url = f'https://us-central1-bix-tecnologia-prd.cloudfunctions.net/api_challenge_junior?id={number}'
    resp = requests.get(url)
    funcionario = resp.text
    df_funcionario_api = pd.DataFrame({'funcionario': [funcionario],
                                       'id_funcionario': [number]})
    df_funcionarios = pd.concat([df_funcionarios,df_funcionario_api])
    
end_consulta = DT.datetime.strftime(DT.datetime.now(tz_BR),'%Y-%m-%d %H:%M:%S')
print("Consulta realizada com Sucesso: "+end_consulta)
df_funcionarios
```
# Item 3 
### 3. Dataset como fonte de dados Parquet File
```python
#Comando para leitura do parquet e armazenar em dataframe pandas
path_parquet_file = "C:\\Users\\caior_op46gft\\Desktop\\arquivo_parquet\\"
df_parquet_pd = pd.read_parquet(path_parquet_file+'categoria.parquet')
df_parquet_pd
```
# Item 4 
### 4.	Dataset final com todos os requisitos solicitados
```python
##comando para juntar
df_tabela_final= df_postgressql_pd.merge(df_funcionarios)\
                                  .merge(df_parquet_pd, left_on='id_categoria', right_on='id',\
                                         how='left').drop('id', axis=1)

df_tabela_final
```
![image](https://user-images.githubusercontent.com/73916591/193870896-cd4833b9-6d11-4c49-94e0-78a7268ae7ba.png)

# Cloud Function
