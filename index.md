# Exploração e visualização de dados com R

Ao trabalhar com variáveis numéricas é sempre uma boa prática verificar suas distribuições. Nesse estudo, o tempo de espera e duração das erupções do gêiser Old Faithful no Parque de Yellowstone, EUA, estão em escalas diferentes, logo faz sentido estudá-las em dois gráficos distintos. Isso pode ser feito usando um histograma ou um gráfico de densidade, o conjunto de dados faithful está disponível na base R, portanto, não precisa ser carregado.
```
eruptions waiting
       <dbl>   <dbl>
 1      3.6       79
 2      1.8       54
 3      3.33      74
 4      2.28      62
 5      4.53      85
 6      2.88      55
 7      4.7       88
 8      3.6       85
 9      1.95      51
10      4.35      85
# ... with 262 more rows
```

library (ggplot2)  <br/>
library (cowplot)  <br/>
library (tibble)  <br/>
library(dplyr)  <br/>

```
p1 <- faithful %>% 
  ggplot(aes(eruptions))+
  geom_histogram(color = "green", fill = "black")+
  ggtitle("Eruption distribution")

p2 <- faithful %>% 
  ggplot(aes(waiting))+
  geom_histogram(color = "green", fill = "black")+
  ggtitle("Waiting time (mins)")

  plot_grid(p1,p2, ncol = 1)
```
 


  
![image](https://user-images.githubusercontent.com/80591420/111692525-485e6e80-880e-11eb-999c-60fe6a0ea8db.png)

# Scatterplots  <br/>
O pr&oacute;ximo passo &eacute; estudar a rela&ccedil;&atilde;o entre as 2 vari&aacute;veis quantitativas e explorar se existe rela&ccedil;&atilde;o entre tempo de espera e tempo de erup&ccedil;&atilde;o. Utilizaremos aqui um scatterplot ou gráfico de dispersão:

![image](https://user-images.githubusercontent.com/80591420/111701055-02f36e80-8819-11eb-9df1-4434b68c2992.png)

Pode-se observar que os dados se enquadram em dois grupos: um com tempos de espera e erupção curtos e outro com tempos de espera e erupção longos. especificamente nesse caso nao há overplotting, os pontos encontram-se separados e o padrão formado é claro. Para refinar a visualização e auxiliar na identificação de tendência podemos adicionar uma regressão linear: 
```
ggplot(faithful, aes(x = waiting, y = eruptions)) +
geom_point() +
abs(x = "Waiting Time (mins)", y = "Eruption Duration (mins)") +
theme(text = element_text(size = 11))+
geom_smooth(method = "lm", col = 'blue')
  ```
  ![image](https://user-images.githubusercontent.com/80591420/111703744-d5102900-881c-11eb-95e3-a7cbbd1c8f03.png)
  
# Regressão Linear
Regressão é utilizada para analisar relações entre variáveis contínuas. Em sua forma mais básica, descreve a relação linear entre uma variável preditora, representada graficamente no eixo X, e uma variável resposta, representada no eixo Y.
Para tornar a discussão mais palpável utilizarmos O conjunto de dados peopleanalyticsdata: :sociological_data que representam uma amostra de informações obtidas de indivíduos que participaram de um estudo de pesquisa global e contém os seguintes campos  <br/>
*````annual_income_ppp:```` A renda anual do indivíduo em dólares americanos PPP ajustados  <br/>
*```average_wk_horas:``` O número médio de horas semanais trabalhadas pelo indivíduo  <br/>
*````education_months:````O número total de meses gastos pelo indivíduo na educação formal primária, secundária e terciária  <br/>
*```family_size:``` O tamanho da família de dependentes do indivíduo  <br/>

```
A tibble: 2,618 x 4
   annual_income_ppp average_wk_hrs education_months family_size
               <int>          <int>            <int>       <int>
 1             66417             50              157           5
 2             55124             50              156           5
 3             55124             50              155           5
 4             54238             50              154           5
 5             53573             50              150           4
 6             52688             51              147           5
 7             52245             51              143           5
 8             51138             51              141           5
 9             49810             51              137           4
10             49367             51              134           4
# ... with 2,608 more rows
```
Os dados parecem conforme o esperado, mas é claro que são apenas algumas linhas. Precisamos de uma rápida visão estatística e estrutural dos dados.  

```
base <- sociological_data %>%   
summary(base)
```
```
annual_income_ppp average_wk_hrs  education_months  family_size   
 Min.   :  2863    Min.   :30.00   Min.   : 40.0    Min.   : 0.00  
 1st Qu.: 62653    1st Qu.:39.00   1st Qu.:157.0    1st Qu.: 2.00  
 Median : 82140    Median :43.00   Median :184.0    Median : 3.00  
 Mean   : 76040    Mean   :44.19   Mean   :179.3    Mean   : 3.26  
 3rd Qu.: 90112    3rd Qu.:50.00   3rd Qu.:207.0    3rd Qu.: 4.00  
 Max.   :119564    Max.   :55.00   Max.   :280.0    Max.   :10.00  
 ```
Também podemos plotar nossas quatro variáveis teste em pares para ver quaisquer relações de interesse, conforme mostrado na Figura:

![image](https://user-images.githubusercontent.com/80591420/111870628-97b6b300-8964-11eb-997b-03d7c015bc58.png)

Na diagonal principal podemos ver as distribuições dos dados em colunas, e gráficos de dispersão e estatísticas de correlação fora da diagonal. Por exemplo, observa-se forte correlação entre education_months e annual_income_ppp.
Para visualizar nossa abordagem e melhorar nossa intuição, iniciaremos com uma regressão linear simples, que é o caso em que existe apenas uma única variável de entrada e variável de resultado.  <br/>


Primeiro, precisamos expressar o modelo que estamos tentando calcular como uma fórmula. Neste caso simples, queremos regredir o resultado
y= annual_income_ppp contra a entrada x= education_months portanto, usaríamos a notação de fórmula simples annual_income_ppp ~ education_months. Agora podemos usar a ```lm()``` função para calcular o modelo linear com base em nosso conjunto de dados e nossa fórmula.  <br/>

```
# calculate regressão
lm(formula = annual_income_ppp ~ education_months, data = base)

Residuals:
   Min     1Q Median     3Q    Max 
-57581  -6040   1268   8201  67367 

Coefficients:
                 Estimate Std. Error t value             Pr(>|t|)    
(Intercept)      4254.615   1235.314   3.444             0.000582 ***
education_months  401.355      6.724  59.687 < 0.0000000000000002 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 13530 on 2590 degrees of freedom
Multiple R-squared:  0.579,	Adjusted R-squared:  0.5789 
F-statistic:  3563 on 1 and 2590 DF,  p-value: < 0.00000000000000022

> anova(regression)
Analysis of Variance Table

Response: annual_income_ppp
                   Df       Sum Sq      Mean Sq F value                Pr(>F)    
education_months    1 652623551041 652623551041  3562.5 < 0.00000000000000022 ***
Residuals        2590 474463930178    183190707 
```
Isso nos diz que o modelo de melhor ajuste é aquele que minimiza os quadrados médios dos resíduos é 
y= 401.4x + 4254.6. Em outras palavras, pode-se esperar um valor de 4254.6 e que cada ponto adicional a ```education_months```   aumentará ```annual_income_ppp``` em 401.4
Outro ponto importante é R² = 0.579 e o valor de p > 0.05 indicando que ```education_months``` explica 58% do valor de ```annual_income_ppp```  <br/>

# Six Sigma
![image](https://user-images.githubusercontent.com/80591420/111908878-aa4eec00-8a39-11eb-97aa-5572c9eb584f.png)

Estatisticas descritivas utilizadas na construção do gráfico de Pareto  <br/>
```
                   | Frequency| Cum.Freq.| Percentage| Cum.Percent.|
|:------------------|---------:|---------:|----------:|------------:|
|Mal selada         |       322|       322|  51.273885|     51.27389|
|Pequenos furos     |       145|       467|  23.089172|     74.36306|
|Validade incorreta |        67|       534|  10.668790|     85.03185|
|Embalagem partida  |        53|       587|   8.439490|     93.47134|
|Embalagem rasgada  |        21|       608|   3.343949|     96.81529|
|Cores borradas     |        10|       618|   1.592357|     98.40764|
|Outros             |        10|       628|   1.592357|    100.00000|
 ```
 O gráfico de Pareto resultante indica que mal selada e Pequenos furos são os 
defeitos mais observados. Portanto, esses dois defeitos devem ser priorizados, pois, conjuntamente, perfazem aproximadamente 74, 36% das falhas. Logo, Validade incorreta poderia ser incluída, com os três tipos de defeitos respondendo por cerca de 85,03% das falhas totais.








