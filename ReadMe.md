# Dashboard Café | Dashboard Coffee

## Objetivos do Projeto | The Project Objective

O objetivo do projeto é trazer analise dos dados sobre o café brasileiro, dados de exportações e valores de frete da mercadoria café.

Também foi determinado praticar modelagem de dados no Power Query (treinamento).


The project aims to analyze data on Brazilian coffee exports and the freight costs of this commodity.

The project also included Power Query data modeling exercises during training.

## Técnicas aplicadas no Projeto. | Techniques applied in the Project.

Criação de duas Páginas de dados

1 Página - Blocos Econômicos

2 Página - Tipos de Café

Creation of two pages data 

1 Page - Economic Blocks

2 Page - Types of Coffee

### 1 - Coleta de Dados | Data collection

## Foram coletados dados sobre o café nos links:

Cecafé - https://www.cecafe.com.br/indicadores-de-mercado/ipep-arabica/

IBGE - https://www.ibge.gov.br/estatisticas/economicas/agricultura-e-pecuaria/9201-levantamento-sistematico-da-producao-agricola.html?=&t=resultados

Cecafé - https://www.cecafe.com.br/dados-estatisticos/exportacoes-brasileiras/

Alguns dados vieram em pdf, portanto, transformei em excel, sem a necessidade de código em python.

Alguns vídeos que auxiliaram na modelagem de dados.

## 2 - Modelagem de dados | Modeling Data

Na coleta dos dados dos links informados acimas, foram realizadas a conversão de PDF para Excel e posteriormente no Power Query ajustadas.

### Tabelas carregadas:

- Relatorio_Mensal Geral
- Relatorio_Mensal 2024
- Valor_FOB
- Exportacoes Continentes Grupos e Blocos - 2023
- Exportacoes Continentes Grupos e Blocos - 2024


- Relatorio_Mensal Geral

  *O que foi feito:*
  
  Alterado o tipo dos dados
  Removido as linahs com dados faltantes.
  Efetuada a consulta acrescentada 
 
- Relatorio_Mensal 2024; Valor_FOB

  *O que foi feito:*
  
  Alterado o tipo dos dados
  Renomeada as colunas

- Exportacoes Continentes Grupos e Blocos - 2023; Exportacoes Continentes Grupos e Blocos - 2024

  *O que foi feito:*

  Ajustado a Data
  Alterado o tipo dos dados
  Colunas vazias removidas


## Medidas utilizadas:

- Receitas

Para entender a soma de todos os valores por tipo de café, utilizei a medida a seguinte medida:

Ex:
    SUM('Relatorio_Mensal Geral'[ARÁBICA])

Para a receita cambial dividi em duas medidas, pois existe duas tabelas diferentes. Preferi não efetuar a "Consulta acrescentada" no Power bi, decorrente a ter mais colunas que não poderia remover ou replicar para a outra tabela. Utilizei tabelas tanto de 2023 e 2024.

Ex:
    Receita_Cambial_2023 = SUM('Exportacoes Continentes Grupos e Blocos - 2023'[Receita Cambial US$ FOB mi 2023]) 


- Atualização

Para acompanhamento da atualização dos dados, criei um cartão para ter esta visualização.

Ex:
    Cartao_Atualizacao = "Atualização em " & SELECTEDVALUE('Atualização'[Data e Hora])

- Análise Temporal

Para analise temporal utilizei medidas comparando valores de ano passado até a data atual. Também utilizado analises temporais de ano sobre ano. 
Houve também comparações de Meses até a data atual e comparações de meses sobre meses.








