# Análise das Cotações de Moedas Estrangeiras (2023–2025) via API do Banco Central

Este projeto tem como objetivo consumir dados de **cinco moedas estrangeiras** diretamente da API do **Banco Central do Brasil (BCB)**, referente ao período de **01/01/2023 até 31/07/2025**. O projeto transforma os dados em `DataFrames` com `pandas`, e salva os resultados em arquivos `.csv`, um para cada moeda.

## Moedas analisadas

- **Iene Japonês (JPY)**
- **Dólar Canadense (CAD)**
- **Dólar Americano (USD)**
- **Franco Suíço (CHF)**
- **Euro (EUR)**

## Acesso rápido

- 🔍 Google Colab, material de estudo: [Abrir no Colab](https://colab.research.google.com/drive/14f1k1rZvMabKVuy_gvVQ9BruGF_MxN50?usp=sharing)
- 🌐 API BCB (cotação por data):  
  [`CotacaoDolarDia`](https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarDia(dataCotacao=@dataCotacao)?@dataCotacao='01-01-2023'&$top=100&$format=json&$select=cotacaoCompra,cotacaoVenda,dataHoraCotacao)
- 🌐 API BCB (cotação por período):  
  [`CotacaoDolarPeriodo`](https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarPeriodo(dataInicial=@dataInicial,dataFinalCotacao=@dataFinalCotacao)?@dataInicial='01-01-2023'&@dataFinalCotacao='07-31-2025'&$top=100&$format=json&$select=cotacaoCompra,cotacaoVenda,dataHoraCotacao)

## Objetivos do projeto

- Acessar os dados da API PTAX do Banco Central do Brasil
- Consultar a cotação de 5 moedas estrangeiras no intervalo de 2023 até 2025
- Coletar variáveis relevantes: `paridadeCompra`, `paridadeVenda`, `cotacaoCompra`, `cotacaoVenda`, `dataHoraCotacao` e `tipoBoletim`
- Converter os dados JSON para `DataFrames` usando `pandas`
- Exportar os resultados para arquivos `.csv` organizados por moeda
- Automatizar e modularizar o processo

## Tecnologias utilizadas

- `Python`
- `pandas`
- `requests`
- `json`

## Estrutura do projeto

├── resultado_<MOEDA>.json # Dados brutos em JSON para cada moeda

├── cotacao_<MOEDA>_2023_2025.csv # Arquivos CSV com as cotações de cada moeda

├── README.md # Este arquivo

└── script.py / notebook.ipynb # Script ou notebook para coleta e processamento

## 🧾 Resultado esperado

Cada arquivo `.csv` contém as seguintes colunas:

- `paridadeCompra` — paridade de compra
- `paridadeVenda` — paridade de venda
- `cotacaoCompra` — valor da cotação de compra
- `cotacaoVenda` — valor da cotação de venda
- `dataHoraCotacao` — data e hora da cotação
- `tipoBoletim` — tipo de boletim (ex: "Fechamento")

## 📌 Exemplo de uso

```python
import pandas as pd
import requests
import json

# Moeda e intervalo de datas
moeda = "USD"  # Pode ser 'USD', 'EUR', 'JPY', 'CAD', 'CHF'
data_inicial = "01-01-2023"
data_final = "07-31-2025"

# URL da API para a moeda desejada
url = f"https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/" \
      f"CotacaoMoedaPeriodo(moeda=@moeda,dataInicial=@dataInicial,dataFinalCotacao=@dataFinalCotacao)?" \
      f"@moeda='{moeda}'&@dataInicial='{data_inicial}'&@dataFinalCotacao='{data_final}'" \
      f"&$top=10000&$format=json&" \
      f"$select=paridadeCompra,paridadeVenda,cotacaoCompra,cotacaoVenda,dataHoraCotacao,tipoBoletim"

# Requisição à API
resposta = requests.get(url)
if resposta.status_code == 200:
    dados = resposta.json()
    with open(f'resultado_{moeda}.json', 'w', encoding='utf-8') as arquivo:
        json.dump(dados, arquivo, ensure_ascii=False, indent=4)

    # Processamento dos dados
    df = pd.json_normalize(dados['value'])
    df.to_csv(f'cotacao_{moeda}_2023_2025.csv', index=False)
else:
    print(f"Erro ao acessar API para {moeda}. Status:", resposta.status_code)
´´´