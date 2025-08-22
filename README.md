# 📈 Análise da Cotação do Dólar (2023–2025) via API do Banco Central

Este projeto tem como objetivo consumir dados da cotação do dólar diretamente da API do Banco Central do Brasil (BCB), referente ao período de **01/01/2023 até 31/07/2025**, transformar os dados em um `DataFrame` com `pandas`, e salvar em um arquivo `.csv`.

## 🔗 Acesso rápido

- 🔍 Google Colab, material de estudo: [Abrir no Colab](https://colab.research.google.com/drive/14f1k1rZvMabKVuy_gvVQ9BruGF_MxN50?usp=sharing)
- 🌐 API BCB (cotação por data):  
  [`CotacaoDolarDia`](https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarDia(dataCotacao=@dataCotacao)?@dataCotacao='01-01-2023'&$top=100&$format=json&$select=cotacaoCompra,cotacaoVenda,dataHoraCotacao)
- 🌐 API BCB (cotação por período):  
  [`CotacaoDolarPeriodo`](https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarPeriodo(dataInicial=@dataInicial,dataFinalCotacao=@dataFinalCotacao)?@dataInicial='01-01-2023'&@dataFinalCotacao='07-31-2025'&$top=100&$format=json&$select=cotacaoCompra,cotacaoVenda,dataHoraCotacao)

## ✅ Objetivos do projeto

- Acessar os dados da API PTAX do Banco Central
- Consultar a cotação do dólar no intervalo de 2023 até 2025
- Selecionar todas as variáveis disponíveis no recurso
- Converter os dados JSON para um `DataFrame` usando `pandas`
- Exportar o resultado para um arquivo CSV
- Tornar o processo reutilizável e automatizável

## ⚙️ Tecnologias utilizadas

- `Python`
- `pandas`
- `requests`
- `json`

## 📁 Estrutura do projeto
├── resultado.json              # Arquivo JSON gerado pela API
├── cotacao_dolar_2023_2025.csv # Arquivo CSV final
├── README.md                   # Este arquivo
└── script.py / notebook.ipynb  # Código de extração e transformação dos dados

## 🧾 Resultado esperado
O arquivo gerado cotacao_dolar_2023_2025.csv contém colunas como:

cotacaoCompra — valor da cotação de compra

cotacaoVenda — valor da cotação de venda

dataHoraCotacao — data e hora da cotação ##

## 📌 Exemplo de uso

```python
import pandas as pd
import requests
import json

# URL da API para o período completo
url = "https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/" \
      "CotacaoDolarPeriodo(dataInicial=@dataInicial,dataFinalCotacao=@dataFinalCotacao)?" \
      "@dataInicial='01-01-2023'&@dataFinalCotacao='07-31-2025'" \
      "&$top=10000&$format=json&$select=cotacaoCompra,cotacaoVenda,dataHoraCotacao"

# Função para realizar requisição à API e salvar JSON
def requisicao_api(link):
    resposta = requests.get(link)
    if resposta.status_code == 200:
        dados = resposta.json()
        print('Status Code:', resposta.status_code)
        with open('resultado.json', 'w', encoding='utf-8') as arquivo:
            json.dump(dados, arquivo, ensure_ascii=False, indent=4)
    else:
        print('Erro ao acessar API. Status Code:', resposta.status_code)

# Chamada da função
requisicao_api(url)

# Leitura e transformação dos dados
jsondata = pd.read_json('resultado.json')
data = jsondata['value']
dados_df = pd.json_normalize(data)

# Exportar para CSV
dados_df.to_csv('cotacao_dolar_2023_2025.csv', index=False)
