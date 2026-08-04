# Dashboard do Café Brasileiro — para onde vai o café e por onde ele sai

Análise das exportações brasileiras de café com dados públicos do Cecafé e do IBGE,
cobrindo 2023 e 2024: volume por bloco econômico, receita cambial, mix por tipo de grão e
concentração portuária.

<!-- Exporte as páginas do Power BI para img/dashboard.png e descomente:
![Dashboard do Café](img/dashboard.png)
-->

## Problema

O café é uma das principais commodities de exportação do país, mas o dado público chega
fragmentado: um PDF por relatório mensal, um arquivo por ano, um recorte por tipo de grão e
outro por porto de embarque. Quem precisa responder "estamos crescendo em qual mercado, e
esse crescimento é volume ou preço?" gasta mais tempo juntando planilha do que analisando.

O projeto consolida essas fontes em um modelo único e responde:

- Quais blocos econômicos concentram a demanda e como isso mudou de 2023 para 2024?
- O crescimento da receita cambial vem de mais sacas ou de preço melhor?
- Qual a dependência logística dos portos de embarque?

## Dados

Fontes públicas, sem dado proprietário:

| Fonte | Conteúdo |
|---|---|
| [Cecafé — Exportações brasileiras](https://www.cecafe.com.br/dados-estatisticos/exportacoes-brasileiras/) | volume e receita cambial por continente, grupo e bloco econômico |
| [Cecafé — IPEP Arábica](https://www.cecafe.com.br/indicadores-de-mercado/ipep-arabica/) | indicador de preço do arábica |
| [IBGE — Levantamento Sistemático da Produção Agrícola](https://www.ibge.gov.br/estatisticas/economicas/agricultura-e-pecuaria/9201-levantamento-sistematico-da-producao-agricola.html) | área, produção e rendimento médio da safra |

Período: 2022 a 2024. Boa parte veio em PDF e foi convertida para Excel antes da modelagem —
sem necessidade de código, o gargalo estava na estrutura, não no volume.

**Tabelas carregadas**

| Tabela | Tratamento no Power Query |
|---|---|
| Relatório Mensal Geral | tipagem, remoção de linhas com dados faltantes, consulta acrescentada |
| Relatório Mensal 2024 | tipagem e renomeação de colunas |
| Valor FOB | tipagem e renomeação de colunas |
| Exportações por Continente/Bloco 2023 | ajuste de data, tipagem, remoção de colunas vazias |
| Exportações por Continente/Bloco 2024 | ajuste de data, tipagem, remoção de colunas vazias |

## Solução

**Modelagem em Power Query.** As duas tabelas de exportação por bloco econômico (2023 e
2024) foram mantidas separadas de propósito: a de 2024 traz colunas que a de 2023 não tem, e
uma consulta acrescentada exigiria descartar informação ou replicar coluna vazia. A decisão
foi preservar as duas e resolver a comparação na camada de medidas.

**Medidas DAX**

Receita por tipo de grão:

```dax
Receita_Arabica = SUM('Relatorio_Mensal Geral'[ARÁBICA])
```

Receita cambial separada por ano, consequência da decisão de modelagem acima:

```dax
Receita_Cambial_2023 =
    SUM('Exportacoes Continentes Grupos e Blocos - 2023'[Receita Cambial US$ FOB mi 2023])
```

Cartão de controle de atualização, para o leitor saber a idade do dado:

```dax
Cartao_Atualizacao = "Atualização em " & SELECTEDVALUE('Atualização'[Data e Hora])
```

Análise temporal comparando ano contra ano e mês contra mês, além de acumulado até a data.

**Duas páginas de relatório**

1. **Blocos Econômicos** — para onde o café brasileiro vai
2. **Tipos de Café** — arábica, conillon, solúvel e torrado

## Resultados

**A Europa é mais da metade do destino, e cresceu.** Em 2024 o bloco europeu importou
26.528.972 sacas de 60 kg, gerando US$ 6.621,8 milhões FOB — 52,6% do total, com alta de
40,6% na comparação com o mesmo período do ciclo anterior. A América do Norte veio em
seguida, com 10.680.799 sacas e US$ 2.650,1 milhões (21,2%), crescendo 46,2%.

**O crescimento foi de preço, não só de volume.** Em 2023 a Europa respondia por 18.862.114
sacas e US$ 3.919,3 milhões. De 2023 para 2024 o volume subiu cerca de 41%, enquanto a
receita cambial subiu 69%. A diferença entre as duas taxas é preço por saca — o Brasil
vendeu mais caro, não apenas mais.

**Três países puxam a demanda.** No acumulado de 2024: Estados Unidos com 8.138.164 sacas,
Alemanha com 7.592.061 e Bélgica com 4.380.132.

**A logística tem um ponto único de concentração.** Santos/SP escoou 60,0% do volume de
despacho e 68,0% do volume de embarque em 2024 (contra 61,9% e 71,7% em 2023). Rio de
Janeiro aparece com 16,5% de despacho. A dependência caiu de um ano para o outro, mas
qualquer interrupção em Santos ainda atinge a maior parte da exportação.

## Stack

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![Power Query](https://img.shields.io/badge/Power_Query-217346?style=flat&logo=microsoft&logoColor=white)
![DAX](https://img.shields.io/badge/DAX-F2C811?style=flat&logo=powerbi&logoColor=black)
![Excel](https://img.shields.io/badge/Excel-217346?style=flat&logo=microsoftexcel&logoColor=white)

## Como reproduzir

```bash
git clone https://github.com/DanteCavalcanteSantos/Dashboard_Cafe_Coffee.git
```

Abra `Dashboard Café Brasileiro.pbix` no Power BI Desktop e atualize as fontes apontando
para a pasta `Dados/` local.

## Estrutura

```
├── Dashboard Café Brasileiro.pbix   painel com as duas páginas de análise
├── Dados/
│   ├── Banco de dados/              CSVs tratados que alimentam o modelo
│   ├── *.xls, *.xlsx                planilhas convertidas dos relatórios Cecafé
│   └── *.pdf                        relatórios originais do Cecafé
├── Doc/Imagens/                     material visual do painel
└── Links uteis.docx                 fontes e referências de apoio
```

## Contato

Dante Cavalcante — análise de dados e Business Intelligence
📧 dante.cavalcante96@gmail.com

---

# English

**Brazilian Coffee Dashboard — where the coffee goes and which port it leaves from**

Analysis of Brazilian coffee exports using public Cecafé and IBGE data for 2023–2024:
volume by economic bloc, foreign exchange revenue, mix by bean type and port concentration.

Key findings: Europe took 26,528,972 60kg bags in 2024 (52.6% of the total, US$ 6,621.8M
FOB, up 40.6%), while revenue grew faster than volume — 69% against 41% — meaning Brazil
sold at a better price, not just in larger quantity. Santos/SP handled 60.0% of dispatch
volume, a single logistical concentration point.

Stack: Power BI, Power Query, DAX, Excel.
