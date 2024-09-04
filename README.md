# Projeto Power BI - Modelo de Dados em Star Schema

## Descrição do Projeto

Este projeto foi desenvolvido para criar um modelo de dados eficiente usando o star schema (esquema estrela) no Power BI. Utilizamos a tabela única de amostra `Financials_origem` para criar tabelas de dimensão e fato, permitindo análises aprofundadas de vendas, lucros, descontos e outros indicadores de desempenho.

## Objetivo

O objetivo deste projeto é demonstrar a construção de um modelo de dados otimizado, aplicando melhores práticas de modelagem com o star schema, usando tabelas de dimensão e uma tabela fato para facilitar a análise de dados financeiros e comerciais.

## Arquitetura do Projeto

![Diagrama do Esquema Estrela]

O modelo de dados foi organizado em um esquema estrela com as seguintes tabelas:

1. **Tabela Fato**
   - `F_Vendas`: Contém dados agregados de vendas, lucro, descontos e unidades vendidas.

2. **Tabelas Dimensão**
   - `D_Produtos`: Detalha informações sobre produtos, incluindo média de unidades vendidas, valores de venda, valor máximo e mínimo de venda.
   - `D_Produtos_Detalhes`: Contém informações detalhadas sobre preços, unidades vendidas, e faixas de desconto para cada produto.
   - `D_Descontos`: Contém faixas de desconto e os valores de desconto associados.
   - `D_Calendário`: Tabela de calendário criada para análises de tempo com anos, meses, trimestres, etc.

## Etapas de Construção do Projeto

### 1. Criação das Tabelas de Dimensão e Fato

A partir da tabela `Financials_origem`, foram criadas as seguintes tabelas:

- **`D_Produtos`**:

  ```DAX
  D_Produtos = 
  SUMMARIZE(
      Financials_origem,
      Financials_origem[Product],
      "Produto", MAX(Financials_origem[Product]),
      "Média de Unidades Vendidas", AVERAGE(Financials_origem[Units Sold]),
      "Média do Valor de Vendas", AVERAGE(Financials_origem[Sale Price]),
      "Mediana do Valor de Vendas", MEDIAN(Financials_origem[Sale Price]),
      "Valor Máximo de Venda", MAX(Financials_origem[Sale Price]),
      "Valor Mínimo de Venda", MIN(Financials_origem[Sale Price])
  )
  ```
  
  - **`D_Descontos`**:

  ```DAX
  D_Descontos = 
  SUMMARIZE(
    Financials_origem,
    Financials_origem[Discount Band],
    "ID_Desconto", RANKX(ALL(Financials_origem[Discount Band]), [Discount Band],, ASC, DENSE),
    "Desconto", AVERAGE(Financials_origem[Discounts])
  )

  ```
  
  - **`D_Calendario`**:

  ```DAX
   D_Calendário = 
  ADDCOLUMNS(
    CALENDAR(MIN(Financials_origem[Date]), MAX(Financials_origem[Date])),
    "Ano", YEAR([Date]),
    "Mês", MONTH([Date]),
    "Nome do Mês", FORMAT([Date], "MMMM"),
    "Trimestre", "Q" & QUARTER([Date]),
    "Semana do Ano", WEEKNUM([Date])
  )
   ```
  
- **`FFvendas`**:

  ```DAX
   F_Vendas = 
  SUMMARIZE(
    Financials_origem,
    Financials_origem[Product], 
    Financials_origem[Discount Band], 
    Financials_origem[Segment], 
    Financials_origem[Country], 
    "Unidades Vendidas", SUM(Financials_origem[Units Sold]),
    "Preço de Venda", AVERAGE(Financials_origem[Sale Price]),
    "Desconto", SUM(Financials_origem[Discounts]),
    "Vendas", SUM(Financials_origem[Gross Sales]),
    "Lucro", SUM(Financials_origem[Profit]),
    "Data", MAX(Financials_origem[Date])
  ) 
   ```
## 2. Configuração de Relacionamentos
Os seguintes relacionamentos foram configurados para garantir a integridade e eficácia do modelo de dados:

 - Relacionamento entre F_Vendas e D_Produtos: Many-to-One com D_Produtos.
 - Relacionamento entre F_Vendas e D_Descontos: Many-to-One com D_Descontos.
 - Relacionamento entre F_Vendas e D_Calendário: Many-to-One com D_Calendário.
## 3. Criação de Medidas
Medidas foram criadas para análises avançadas, como:

 - **`Total de Vendas`**:
```DAX
  TotalVendas = SUM(F_Vendas[Vendas])
```

 - **`Total de Lucro`**:
```DAX
  TotalLucro = SUM(F_Vendas[Lucro])
```
 - **`Lucro po Unidade Vendida`**:
```DAX
  LucroPorUnidade = DIVIDE([TotalLucro], SUM(F_Vendas[Unidades Vendidas]))
```

## 4. Criação de Relatórios e Visualizações
Diversos relatórios e dashboards foram criados usando gráficos de barras, linhas, e tabelas, permitindo análises detalhadas de vendas, lucro e desempenho ao longo do tempo.
## Funcionalidades e Técnicas Utilizadas
 - Modelagem Dimensional: Construção de um modelo de dados eficiente usando o star schema.
 - Funções DAX: Utilização de funções como SUMMARIZE, MEDIAN, RANKX, CALENDAR, ADDCOLUMNS, e funções de agregação para criar tabelas calculadas e medidas.
 - Relatórios Interativos: Criação de dashboards interativos com gráficos dinâmicos e slicers.
## Como Executar o Projeto
 1. Abra o arquivo .pbix no Power BI Desktop.
 2. Explore o modelo de dados e os relatórios criados.
 3. Personalize conforme necessário para adicionar mais funcionalidades ou adaptar a outros conjuntos de dados.
## Conclusão
Este projeto demonstra a criação de um modelo de dados eficiente utilizando o star schema no Power BI, facilitando a análise de dados financeiros e de vendas. A configuração correta dos relacionamentos e o uso estratégico de funções DAX permitem análises dinâmicas e relatórios ricos em informações.

