# 📌 Extração
Este bloco de código realiza a extração e limpeza dos dados do dataset `TelecomX_Data.json`.

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

# Carregar os dados
url = "https://raw.githubusercontent.com/ingridcristh/challenge2-data-science/refs/heads/main/TelecomX_Data.json"
df = pd.read_json(url)

# Transformar colunas contendo dicionários em DataFrames separados
account_df = df["account"].apply(pd.Series)
df = pd.concat([df, account_df], axis=1)

internet_df = df["internet"].apply(pd.Series)
df = pd.concat([df, internet_df], axis=1)

phone_df = df["phone"].apply(pd.Series)
df = pd.concat([df, phone_df], axis=1)

customer_df = df["customer"].apply(pd.Series)
df = pd.concat([df, customer_df], axis=1)

# Converter variáveis categóricas para numéricas (exemplo: 'Churn')
df["Churn"] = df["Churn"].map({"Yes": 1, "No": 0})

# Selecionar apenas colunas numéricas
df_numeric = df.select_dtypes(include=["number"])

# Calcular matriz de correlação
corr_matrix = df_numeric.corr()

# Criar matriz de correlação visual
plt.figure(figsize=(10, 6))
sns.heatmap(corr_matrix, annot=True, cmap="coolwarm", fmt=".2f", linewidths=0.5)
plt.title("Matriz de Correlação das Variáveis Numéricas")
plt.show()

# 🔧 Transformação de Dados

Este bloco de código realiza a transformação dos dados, garantindo qualidade e consistência para análises futuras.

import requests
import pandas as pd

# URL do arquivo JSON
url = "https://raw.githubusercontent.com/jlima2020/challenge2-data-science/main/TelecomX_Data.json"

# Fazendo a requisição
response = requests.get(url)

# Verificando se a requisição foi bem-sucedida
if response.status_code == 200:
    data = response.json()

    # Convertendo JSON para DataFrame
    df = pd.DataFrame(data)

    # Visualizando as primeiras linhas
    print(df.head())

else:
    print(f"Erro ao acessar os dados: {response.status_code}")


# Verificar as primeiras linhas do dataset
print(df.head())

# Exibir informações sobre as colunas e tipos de dados
print(df.info())

# Verificar os tipos de dados de cada coluna
print(df.dtypes)

import pandas as pd

# Exibindo informações gerais do DataFrame
print("== Informações Gerais ==")
print(df.info())

# Visualizando os tipos de dados
print("\n== Tipos de Dados ==")
print(df.dtypes)

# Checando valores ausentes em cada coluna
print("\n== Valores Ausentes ==")
print(df.isnull().sum())

# Verificando registros duplicados
# Before checking for duplicates, identify columns that might contain unhashable types like dictionaries.
# We can iterate through object type columns and check the type of their elements.
object_cols = df.select_dtypes(include=['object']).columns
cols_to_drop_for_dup_check = []
for col in object_cols:
    try:
        # Attempt to apply a hashable operation to see if it fails
        df[col].apply(hash)
    except TypeError:
        print(f"Coluna '{col}' parece conter tipos não hashable (como dicionários).")
        # If this column is not essential for duplicate checking, consider excluding it.
        # For simplicity, let's assume we can drop such columns for the duplicate check.
        cols_to_drop_for_dup_check.append(col)


# Check for duplicates, excluding columns identified as potentially containing unhashable types.
# If cols_to_drop_for_dup_check is empty, df_for_dup_check will be the original df.
df_for_dup_check = df.drop(columns=cols_to_drop_for_dup_check, errors='ignore')

# Now perform the duplicate check on the potentially modified DataFrame
dup_count = df_for_dup_check.duplicated().sum()
print(f"\n== Registros Duplicados: {dup_count} ==")


# Checando inconsistências em colunas categóricas (usando pandas.unique)
# We should only perform unique check on columns that are actually categorical strings,
# not potentially dictionary columns.
categorical_columns = df.select_dtypes(include=['object']).columns
for col in categorical_columns:
    # Skip columns we identified as having unhashable types if we are not processing them
    if col in cols_to_drop_for_dup_check:
         print(f"\nSkipping unique value check for column '{col}' due to unhashable types.")
         continue
    unique_values = df[col].unique()
    print(f"\nColuna '{col}' - Valores Únicos:")
    print(unique_values)

# Se houver colunas com datas, é importante padronizá-las.
# Suponha que haja uma coluna 'data_registro' que precise ser convertida.
if 'data_registro' in df.columns:
    # Converter a coluna para o tipo datetime
    df['data_registro'] = pd.to_datetime(df['data_registro'], errors='coerce')

    # Normalizando a data (removendo a parte do tempo)
    df['data_registro_normalizada'] = df['data_registro'].dt.normalize()

    print("\n== Exemplo de Normalização de Datas ==")
    print(df[['data_registro', 'data_registro_normalizada']].head())

# 📊 Carga e Análise dos Dados

Este bloco de código realiza a carga e análise dos dados, ajudando a entender a base de dados **ETL** e a identificar padrões que possam explicar a **diminuição de clientes**.

## 📌 Análise da Evasão de Clientes

Abaixo, um gráfico que mostra a distribuição dos clientes que permaneceram ou saíram.

```python
import matplotlib.pyplot as plt
import seaborn as sns

# Verificar se a coluna 'Churn' existe no DataFrame
if 'Churn' in df_cleaned.columns:
    # Contar os valores de churn
    churn_counts = df_cleaned['Churn'].value_counts()

    # Criar o gráfico de barras
    plt.figure(figsize=(8, 6))
    sns.barplot(x=churn_counts.index, y=churn_counts.values, palette="pastel")
    plt.xlabel("Status do Cliente")
    plt.ylabel("Número de Clientes")
    plt.title("Distribuição de Clientes por Evasão")
    plt.xticks(ticks=[0, 1], labels=["Permaneceu", "Saiu"])
    plt.show()
else:
    print("Erro: A coluna 'Churn' não foi encontrada no DataFrame.")
