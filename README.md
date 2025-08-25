# 1- Entendimento do negócio

As lojas físicas de supermercado estão sempre em uma situação delicada com relação à previsão de compras e vendas.

Se a previsão é maior que a demanda, os supermercadistas ficam com produtos perecíveis em excesso.

Assim como demanda superior à previsão leva ao esgotamento rápido de itens populares, resultando em perda de receita e clientes insatisfeitos.

O problema se torna mais complexo à medida que os varejistas adicionam novas localidades com necessidades únicas, novos produtos, gostos sazonais em constante mudança e marketing de produtos imprevisível.

A MarketShow, uma grande rede de supermercados, sabe bem disso. Eles operam alguns supermercados, com mais de 200.000 produtos diferentes em suas prateleiras.

Atualmente, eles dependem de métodos de previsão utilizando pouquíssimos dados como insumo além de pouca automação para executar os planos.

Dessa forma, a MarketShow gostaria de substituir o modelo preditivo vigente por outro mais performático visando ter previsões com maior precisão. O modelo deve ser capaz de prever a demanda dos próximos 15 dias.


## Objetivo

Criar um modelo superior ao modelo já existente, que seja capaz de prever a demanda dos próximos 15 dias.


# 2- Entendimento dos dados

Temos um total de 6 planilhas nos fornecendo diferentes informações, que estão relacionadas entre si:

- train - Possui os dados dos produtos e quantidade de vendas que serão utilizados durante o treinamento do modelo

- test - Possui os dados dos produtos e quantidade de vendas que serão utilizados durante a validação do modelo

- oil - É uma base que possui a data e o preço do petróleo, sendo essa uma variável externa que impacta no produto

- holidays_events - Possui informações sobre datas de feriados, locais e descrições, o que pode impactar na demanda de determinados produtos

- stores - Possui informações das lojas, como cidade, estado, tipo e o cluster que elas pertencem

- transactions - Apresenta informações das transações, incluindo numero da loja e a data em que ocorreram


## Análise Exploratória dos Dados

### Detalhamento da base de dados

- A base de treino principal possui 205095 linhas e 6 colunas

- Esta base não possui nenhum valor nulo

- Nesse base temos:

 - 5 lojas diferentes

 - 33 familias de produtos

 - Possui dados entre Janeiro de 2014 e Maio de 2017

### Informações das vendas

- É possível notar que até Junho de 2015 as vendas estavam bem instáveis, variando em mais de 1 milhão por mês em alguns meses

- Esse comportamento pode ser notado em todas as lojas individualmente

- A loja 3 é a loja que apresenta o maior número de vendas durante todo o período analisado

### Informações dos produtos

- Os produtos mais vendidos são os produtos categorizados como "Grocery I", que geralmente são produtos alimentícios e de uso diário de supermercado, por exemplo arroz, feijão, açucar, óleo e enlatados

- O segundo tipo de produto mais vendido são "Beverages", que são refrigerantes, sucos, chás e outros produtos dentro dessa categoria

- O top 3 representa uma quantidade significativa das vendas totais do super mercado, onde o 4º produto mais vendido tem menos da metade das vendas do 3º

### Promoções

- As vendas com promoções tem aumentado significativamente durante os anos, chegando a quase 94% do total de vendas realizadas em 2017

### Preço do Petróleo

- É possível notar que o preço do petróleo estava bem alto durante os meses que tiveram um forte variação no total de vendas, então esse é um fator externo que pode ter impactado no preço dos produtos e afetado diretamente as vendas.


# 3- Preparação dos dados

## Join das bases

- Todas as bases externas foram agregadas as bases de treino e teste

- Também foi alterado o tipo da coluna data para datetime

## Feature Engineering

Nessa etapa foram criadas algumas variáveis para que o modelo pudesse ser mais assertivo.

- Colunas lag - são variáveis criadas a partir dos próprios valores históricos da série temporal, deslocados no tempo

- Colunas média móvel - são variáveis que mostram a média da demanda em um período passado, ajudando a capturar a tendência recente da série

- Colunas relacionadas a datas - Ano, Mês, Dia, Dia da semana, Final de semana

## Criação da coluna target do modelo

- Primeiro a base foi divididade entre dados de treino e validação, para evitar data leakage

- Após isso foi criada a coluna target onde essa coluna somava a quantidade de vendas dos próximos 15 dias (excluindo o dia atual)

## Tratamento dos valores nulos

- As variáveis que continham valores nulos foram tratadas individualmente, de acordo com o que representavam, por exemplo, na coluna "feriado" o que não estava definido com feriado foi completado com "Dia normal"

# 4- Modelagem - Modelo Baseline

- Os modelos foram treinados utilizando a base mais básica, sem a adição de novas variáveis.

- Primeiro o modelo vigente foi avaliado, utilizando a pipeline de tratamento dos dados e treinamento do modelo fornecida

- Após a avaliação do modelo vigente foi montada uma pipeline para tratamento dos dados e treinamento do modelo baseline:

 - Foi utilizado o StandardScaler para tratamento das colunas numéricas

 - Foi utilizado o OneHotEncoder para tratamento das colunas categóricas

 - Foram treinados diversos algoritmos de machine learning, como Regressão Linear, LightGBM, CatBoost, GradientBoosting e XGBoost

- Os modelos foram avaliados e comparados utilizando as métricas RMSE e MAE

- O melhor modelo foi salvo


# 5- Modelagem - Aperfeiçoamento do modelo

- Os modelos foram treinado utilizando a base completa, com todas as novas variáveis criadas

- Todos os modelos foram treinados novamente utilizando a base completa e depois em alguns períodos específicos

- Além dos algoritmos anteriores, também foi treinado um modelo utilizando LSTM

- Foi utilizado o Optuna para tunar os hiperparâmetros de todos os algoritmos testados

- Foi criado um Voting Ensemble com os modelos tunados

- Foi realizada uma análise de distribuição de erros de todos os algoritmos analisados



# 6- Modelagem - Criação de modelos segmentados por loja

- Foi criado um modelo para específico para cada loja, utilizando somente os dados daquela loja no treinamento

- Esse modelo foi comparado com o melhor modelo treinado com a base completa

- Foi salvo o modelo que apresentou o melhor desempenho em cada loja

- Foi realizada uma comparação geral entre os modelos segmentados e o modelo único, onde o modelo único conseguiu o melhor desempenho.


# 7- Conclusão

A análise evidenciou que o modelo vigente da MarketShow é limitado, pois utiliza poucos dados e baixa automação, resultando em previsões menos precisas e maiores riscos de excesso ou ruptura de estoque.

A aplicação de técnicas avançadas de machine learning (como LightGBM, CatBoost e LSTM), aliadas ao uso de variáveis externas (preço do petróleo, feriados, transações e promoções), aumentou a acurácia das previsões de demanda para os próximos 15 dias.


# 8- Recomendações de negócio

- Adotar o modelo único otimizado como padrão em todas as lojas.

- Integrar previsões ao planejamento de compras e estoque, priorizando produtos de maior impacto.

- Monitorar e re-treinar periodicamente o modelo para capturar mudanças de mercado e sazonalidade.

- Simular promoções e ajustar pricing para reduzir perdas e maximizar margens.

- Evoluir para previsões por famílias de produtos e clusters de lojas em futuros projetos.




















