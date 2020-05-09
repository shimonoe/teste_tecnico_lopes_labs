# Teste Data Science - Lopes Labs
[
![Python version](https://img.shields.io/badge/Python-3.6%20|%203.7-informational?style=for-the-badge&logo=python)](https://www.python.org/downloads/)

Este repositório contém as respostas para o teste de Data Science do processo seletivo da Lopes Labs.

## Questões a serem respondidas:
 1. Como foi a definição da sua estratégia de modelagem?
 2. Como foi definida a função de custo utilizada?
 3. Qual foi o critério utilizado na seleção do modelo final?
 4. Qual foi o critério utilizado para validação do modelo? Por que escolheu utilizar este método?
 5. Quais evidências você possui de que seu modelo é suficientemente bom?

### 1. Estratégia de modelagem
A abordagem para o problema segue um *pipeline* comum de modelagem. Dividido em estágios, cada artefato gerado é passado para o seguinte, em contextos bem definidos. O *pipeline* é composto pela sequência:

0. Definição do tipo de problema (regressão ou classificação);
1. Preparação dos dados:
1.1 Carregamento dos dados;
1.2 Busca por valores faltantes;
1.3 Verificação de consistência dos tipos das colunas;
2. Análise dos dados:
2.1 Correlação entre as *features*;
2.2 Detecção de *outliers*;
3. Pré-processamento:
3.1 Transformação de variáveis categóricas;
3.2 *Feature engineering*;
3.3 Normalização das *features*;
3.4 Visualização das *features* transformadas;
4. Treino e Teste;
4.1 Métricas para a separação das partições;
4.2 Separação das partições;
4.3 Correção do desbalanço de classes na partição de treino;
4.4 Escolha do melhor modelo por métricas base;
4.5 Otimização de hiperparâmetros;
4.6 Validação cruzada do modelo;
4.7 Métricas de qualidade;
4.8 Explicabilidade do modelo;
 
### 2. Função de custo do modelo e da otimização
Foram utilizadas duas métricas distintas na modelagem. A primeira delas está relacionada ao modelo escolhido, do *ExtraTreeClassifier*, que utiliza uma função denominada *Gini impurity* para calcular a taxa de erro ao classificar uma *label*. O valor 0 representa a melhor separação entre as classes, ao passo em que o valor 1 representa a pior. O modelo tenta encontrar a árvore com menor valor do *Gini impurity* no *ensemble* treinado. A segunda métrica, utilizada na otimização, foi o coeficiente de correlação de Matthews. Com a finalidade de buscar os melhores parâmetros a serem utilizados no modelo. Por utilizar o *falso positivo* e *falso negativo* no numerador e no denominador da função, o efeito dessas métricas é mais visível no valor final. Isso se traduz em um *score* que busca explicar se o modelo também é bom em identificar os casos negativos e positivos. Uma alternativa seria utilizar o *F1 score*, mas nessa métrica só o efeito dos elementos negativos são diluídos, focando apenas nos "acertos" do modelo.

### 3. Escolha do modelo final
A escolha do modelo final foi feita com base em alguns *scores* medidos ao final de cada treino. As métricas escolhidas foram **Accuracy**, **Precision**, **Recall**, **F1**, **Matthews Correlation Coeficient**, **Logloss**, e **ROC/AUC score**. 
Os modelos testados foram: **XGBClassifier**, **LGBMClassifier**, **CatBoostClassifier**, **ExtraTreesClassifier**, **GradientBoostingClassifier**, **KNeighborsClassifier**, **MLPClassifier** e **SVC**.

O modelo recebe um voto caso a sua métrica for a melhor dentre os outros. No final, o modelo com mais votos vence.

### 4. Critério de validação
Após a escolha do modelo, realizei uma etapa de otimização dos hiperparâmetros com a finalidade de buscar a melhoria dos *scores* do modelo base. Então, o modelo otimizado passou por uma validação cruzada, com cinco partições da base de dados sem o *oversample*, onde cada *fold* teve as mesmas métricas colhidas para comparação posterior. O valor médio de cada métrica foi calculado para ser comparado com o modelo otimizado. Como não há variação significativa dentro das métricas base e as particionadas, o modelo foi considerado válido.
Esta abordagem foi escolhida devido a dois fatores. O primeiro está relacionado ao número de *labels* do *dataset*. Apesar de alguns cuidados na etapa de separação do treino e teste, podem ocorrer alguns casos em que as partições carregam algumas características que levem ao *overfitting*. O segundo motivo é o desbalanço entre as *labels*. A menor classe possui apenas 5 exemplos, e a maior tem mais de 2 mil. Resultando em um problema que leva a generalização do modelo a apenas nos termos mais frequentes. 

### 5. O modelo é suficientemente bom?
Através das métricas colhidas após a validação cruzada, é possível inferir algumas hipóteses sobre este modelo. A primeira delas vai de encontro com o discutido no *notebook* do modelo, que se considerarmos que um vinho é de menor qualidade caso ele faça parte dos 80% da amostra, temos uma *label* secundária que transforma o problema de classificação *multilabel* em binário. Pelo gráfico de erro de predição e o SHAP para as *features* mais importantes, conseguimos ver claramente que o *dataset* poderia ser dividido dessa maneira.
A segunda delas é a de que os vinhos na faixa de qualidade 5 e 6 são muito similares, segundo a matriz de confusão do modelo. Levando a crer que existem poucas diferenças entre essas duas categorias.
Para o problema original de categorização em *labels* entre 3 e 9, o modelo não performa como o desejado. Porém, se reduzirmos o problema em um classificador binário ou em menos classes (baixa qualidade, média e alta) o modelo teria uma performance melhor, pois há fortes indícios pela matriz de confusão de que essa divisão existe.

