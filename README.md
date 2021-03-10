# Desafio Cognitivo AI
 Desafio para o processo seletivo de Cognitivo AI

Neste desafio foi treinado um modelo de predição de preço de locações, utilizando a base de dados de Arbnb do Rio de Janeiro, correspondente a informações de fevereiro de 2021. Disponível no link: http://insideairbnb.com/get-the-data.html, e anexas no arquivo 'listing.csv'.

O arquivo: price_regressor.ipynb contem todas as etapas utilizadas para esta modelagem, desde o pré processamento dos dados, tratamento de missings, extração de informações com regular_expressions, analises de PCA, treinamento de vários modelos, e analises out-of-sample para seleção do melhor modelo. Para simplificar a leitura do notebook, foi adicionada também uma versão em html. 'price_regressor.html'.

Os passos foram brevemente detalhados nesse Jupyter-notebook com vários comentários. No entanto algumas questões interessantes são respondidas diretamente aqui:

## Questionário

* **Como foi a definição da sua estratégia de modelagem?**

  _Primeiro identifiquei as colunas que contem informação que pode ser útil na modelagem. Fiz o pré-processamento dos dados, extraindo informações dos campos de texto via regex e tratando os campos categóricos com um OneHotEncoder manualmente para limitar o numero de features gerado. Após isso fiz um analises de PCA, onde observei que o numero de componentes principais com até um 95% de significância era próximo do numero inicial de features, isso me levou a concluir que existe baixa correlação entre o label e as features. Após isto comecei a modelagem. Testei três modelos. Primeiro um modelo Linear, pois modelos lineares são de rápido treinamento e alta explicabilidade. No entanto o resultado foi mais próximo de uma superfície constante ![img](images_for_readme/equation.png), o que coincide com a baixa correlação identificada por PCA. Assim passei a testar modelos não lineares, primeiro utilizando Supervised Vector Machines, que tenta aproximar a função através de superposição de funções com estrutura dada por um kernel. Escolhi o kernel Gaussiano pela sua generalidade, pois quase qualquer função  pode ser aproximada  por kernels gaussianos locais. Embora é um método poderoso, tem um tempo computacional alto. Finalmente testei uma das melhores otimizações do RandomForest e que costuma ter ótimo comportamento na maioria de problemas de regressão, isto é o XGBoost, com o qual, efetivamente, se obtiveram as melhores métricas._ 

* **Como foi definida a função de custo utilizada?**

  _Para o modelo linear foi utilizado o erro quadrático médio (Mean-Squared Error ) como função de custo, para o modelo de SVR foi utilizado a função de custo epsilon-insensitive. Para o modelo de Kernel Ringe foi utilizada a função de custo de Ringe, isto é, MSE com regularização L2. Finalmente para o algoritmo de XGBoost foi observado um resultado melhor quando foi utilizado desvio médio da distribuição de Tweedie com potencia = 1.5. vale destacar que esta melhora foi exposta ao fazer uma avaliação via GridSearchCV das funções de custo disponíveis para a regressão XGBoost. Esta função de custo permite dar menos relevância a magnitude dos dados, e maior destaque ao valor relativo entre o dado real e o predito._

* **Qual foi o critério utilizado na seleção do modelo final?**

  _Foram comparadas algumas métricas que determinam quão bom foi o resultado de cada modelo. Destaca-se o ![R2](images_for_readme/R2.png), que permite entender quanto da variância dos dados foi explicado pelo modelo, quanto mais próximo de 1, melhor. Também a velocidade de treinamento foi avaliada, o que no final levou a selecionar o modelo de XGBoost. Também para cada modelo foram realizadas varias alterações nos hiper-parâmetros via GridSearchCV, de modo a encontrar o valor dos parâmetros que permitisse ter o melhor ![R2](images_for_readme/R2.png) possível, o gráfico de resíduos também permitiu concluir que o melhor modelo foi o XGBoost_. 

* **Qual foi o critério utilizado para validação do modelo? Por que escolheu utilizar este método?**

  _Foi escolhido fazer a validação out-of-sample, separando o dataset original em duas partes, a de treino com 3/4 dos dados, e o restante 1/4 foi separado para teste. Os dados de teste não foram utilizados em nenhum momento durante o treinamento do modelo, unicamente foram utilizados para escorar o modelo no final. Assim pode se ver como o modelo se comporta com dados novos. Se o resultado for positivo, se espera que o modelo tenha o mesmo comportamento quando aplicado a novos dados._

* **Quais evidências você possui de que seu modelo é suficientemente bom?**

  _O fato de que a regressão lineal levasse a um hiperplano horizontal (![img](images_for_readme/equation.png)), e o resultado do analises de PCA, implica uma alta variância e uma baixa correlação (alta aleatoriedade dos dados). Por isto ter um resultado de ![img](images_for_readme/R2035.png) é muito positivo, pois significa que com o modelo construído podemos explicar 35% das variações dos dados. Também o gráficos de resíduos mostra uma grande melhora entre a regressão linear, que representa quase que propor sempre o preço médio como predição, e o modelo XGBoost, que traz uma menor dispersão no gráfico de resíduos._

