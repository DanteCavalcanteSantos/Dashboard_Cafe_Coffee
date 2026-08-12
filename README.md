# Dashboard do Café Brasileiro — para onde vai o café e por onde ele sai

Análise das exportações brasileiras de café com dados públicos do Cecafé e do IBGE,
cobrindo 2023 e 2024: volume por bloco econômico, receita cambial, mix por tipo de grão e
concentração portuária.

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

Período: 2022 a 2024, com série mensal em 2023 e 2024 — 50.443.037 sacas de 60 kg exportadas
em 2024. Boa parte veio em PDF e foi convertida para Excel antes da modelagem: sem
necessidade de código, o gargalo estava na estrutura, não no volume.

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

**Análise temporal em duas frentes**, calculada para cada um dos quatro tipos de grão:

- **YoY** — `YoY Receita Arabica Total (%)` e equivalentes para conillon, solúvel e torrado:
  mesmo mês contra o ano anterior, o que remove o efeito da sazonalidade da safra.
- **MoM** — `MoM Receita Conillon Total (%)` e equivalentes: mês contra mês, para movimento
  recente.

Cada indicador tem sua medida de detalhe (`Detalhe % YoY Arabica`, `Detalhe % MoM Torrado`)
usada nos visuais de drill.

**Duas páginas de relatório**

1. **Blocos Econômicos** — para onde o café brasileiro vai
2. **Tipos de Café** — arábica, conillon, solúvel e torrado

## Resultados

**A Europa é mais da metade do destino, e cresceu.** Em 2024 o bloco europeu importou
26.528.972 sacas de 60 kg, gerando US$ 6.621,8 milhões FOB — 52,6% do total, com alta de
40,6% em volume sobre 2023. A América do Norte veio em seguida, com 10.680.799 sacas e
US$ 2.650,1 milhões (21,2%), crescendo 46,2%. Somados, os países importadores concentram
91,8% do total.

**O crescimento foi de preço, não só de volume.** No consolidado, o volume subiu 28,5% de
2023 para 2024 (39,2 → 50,4 milhões de sacas), enquanto a receita cambial subiu 55,4%
(US$ 8,05 → 12,51 bilhões FOB). A receita por saca aumentou cerca de 21%: o Brasil vendeu
mais caro, não apenas mais. Na Europa isolada o efeito é ainda mais forte — 41% de volume
contra 69% de receita.

**Três países puxam a demanda.** No acumulado de 2024: Estados Unidos com 8.138.164 sacas,
Alemanha com 7.592.061 e Bélgica com 4.380.132.

**O crescimento está na borda, não no centro.** O Leste Europeu foi o bloco que mais avançou
em volume (+74,7%), enquanto América Central (−48,5%), América do Sul (−32,7%) e Mercosul
(−18,4%) recuaram. Mercados emergentes somam 19,9% de participação contra 71,9% dos
tradicionais.

**Arábica sustenta o resultado.** Em 2023 o arábica representou 78,6% do volume exportado e
82,2% da receita — a distância entre os dois percentuais mostra que é também o grão de maior
valor por saca.

**A logística tem um ponto único de concentração.** Santos/SP escoou 60,0% do volume de
despacho e 68,0% do volume de embarque em 2024 (contra 61,9% e 71,7% em 2023). Rio de
Janeiro aparece com 16,5% de despacho. A dependência caiu de um ano para o outro, mas
qualquer interrupção em Santos ainda atinge a maior parte da exportação.

> Conferência: as bases independentes — série por tipo de grão, série por bloco econômico e
> tabela de despacho por porto — fecham no mesmo total de 2024 (50.443.037 sacas) e de 2023
> (39.245.773 sacas, US$ 8.051,1 milhões). Os números acima saem direto de
> `Dados/Banco de dados/`.

## Observação sobre a base de origem

No arquivo de exportações por bloco econômico, a última coluna vem rotulada como *"Variação
(%) em comparação ao mesmo período de 2020"*, mas os valores correspondem à variação de
volume contra **2023**, não contra 2020 — conferido linha a linha (Europa +40,6%, América do
Norte +46,2%, Ásia +11,4%). O rótulo veio assim do Cecafé. Ao atualizar a base, vale
reconferir.

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

Para atualizar com dados novos, baixe a publicação mensal do Cecafé, converta o PDF para
Excel e mantenha o mesmo layout de colunas — as etapas do Power Query estão nomeadas por
tabela.

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
FOB, up 40.6%). Overall volume grew 28.5% while revenue grew 55.4%, meaning revenue per bag
rose roughly 21% — Brazil sold at a better price, not just in larger quantity. Arabica
accounted for 78.6% of volume and 82.2% of revenue. Santos/SP handled 60.0% of dispatch
volume and 68.0% of shipping volume, a single logistical concentration point.

Stack: Power BI, Power Query, DAX, Excel.
