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

Pode-se observar que os dados se enquadram em dois grupos: um com tempos de espera e erupção curtos e outro com tempos de espera e erupção longos. Especificamente nesse caso não há overplotting, os pontos encontram-se separados e o padrão formado é claro. Para refinar a visualização e auxiliar na identificação de tendência podemos adicionar uma regressão linear: 
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
Para tornar a discussão mais palpável utilizaremos o conjunto de dados peopleanalyticsdata: :sociological_data que representam uma amostra de informações obtidas de indivíduos que participaram de um estudo de pesquisa global e contém os seguintes campos  <br/>
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

### Gráfico de Pareto

É uma variação importante do histograma para dados categóricos. Esse gráfico é comumente empregado nos esforços de melhoria de qualidade, em que as categorias geralmente representam tipos diferentes de defeitos, modos de falha ou problemas nos produtos/processos. As categorias são ordenadas de modo que a categoria com a maior frequência ficará à esquerda, seguida pela categoria com a segunda maior frequência, e assim por diante. Esses diagramas exibem a “Lei de Pareto”; ou seja, a maioria dos defeitos pode ser creditada apenas a poucas categorias. Verifique as informações referentes a defeitos encontrados em embalagens:
```

## Registros de defeitos encontrados em embalgens
-------------------------------
        Defeito     |Frequência| 
|:------------------|---------:|
|Mal selada         |       322|      
|Pequenos furos     |       145|       
|Validade incorreta |        67|    
|Embalagem partida  |        53|       
|Embalagem rasgada  |        21|       
|Cores borradas     |        10|       
|Outros             |        10|      
-------------------------------
Fonte: Francisco, L., A.r., D., H., F., e L., F. (2013), Controle Estatístico de Processos - Uma Abordagem Prática para Cursos de Engenharia e Administração, Grupo GEN

```

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
 O gráfico de Pareto resultante indica que embalagem mal selada e com pequenos furos são os 
defeitos mais observados. Portanto, esses dois defeitos devem ser priorizados, pois, conjuntamente, perfazem aproximadamente 74, 36% das falhas. Logo, validade incorreta poderia ser incluída, com os três tipos de defeitos respondendo por cerca de 85,03% das falhas totais.

## Diagrama de causa e efeito em R 


Para melhorar os processos de soldagem de uma empresa fictícia, uma comissão composta pelas partes interessadas foi formada. Essas pessoas conduzem uma discussão (brainstorming) e produzem um diagrama de causa e efeito. Como resultado da sessão de discussão, a comissão identifica, por tentativa, as seguintes variáveis como potenciais influenciadoras:

1. Densidade de fluxo  <br/>
2. Temperatura de refluxo  <br/>
3. Velocidade do rolo  <br/>
4. Ângulo do rolo  <br/>
5. Altura da pasta  <br/>
6. Pressão do rolo  <br/>
7. Método de carregamento da placa  <br/>

```
library(qcc)  
cause.and.effect(cause=list(Medicao=c("Temperatura", "Altura da pasta", "Densidade"), 
                            Material=c("Solda contaminada", "Soldabilidade", "Orientação"),  
                            Mao_de_obra=c("Alinhamento da palheta", "Carregamento da palheta"), 
                            Meio_Ambiente=c("Temperatura"),  
                            Metodo=c("Quantidade", "Densidade relativa", "Tipo"),  
                            Maquina=c("Pressão do rolo", "Velocidade do rolo", "Ângulo do rolo")), 
                            effect="Defeitos de soldagem")  
  ```
                 
                 
  ![image](https://user-images.githubusercontent.com/80591420/112541664-6b4ecc80-8d92-11eb-8954-52201f9e4b25.png)
  
  
## Nuvem de palavras
  
Os métodos de mineração de texto nos permitem destacar as palavras-chave usadas com mais frequência em um parágrafo de texto. Pode-se criar uma nuvem de palavrasntambém conhecida como nuvem de texto ou nuvem de tag, que é uma representação visual dos dados.
O procedimento de criação de nuvens de palavras é simples em R. O pacote text mining (tm) e o pacote gerador de nuvem de palavras (wordcloud) estão disponíveis em R para nos ajudar a analisar textos e visualizar rapidamente as palavras-chave como uma nuvem de palavras.

library("tm")  <br/>
library("SnowballC")  <br/>
library("wordcloud")  <br/>
library("RColorBrewer")  <br/>
library(rio)  

```
text <- readLines(file.choose())
mandela <- text
docs <- Corpus(VectorSource(mandela))
toSpace <- content_transformer(function (x , pattern ) gsub(pattern, " ", x))
docs <- tm_map(docs, toSpace, "/")
docs <- tm_map(docs, toSpace, "\\|")
docs <- tm_map(docs, content_transformer(tolower))
docs <- tm_map(docs, removeNumbers)
docs <- tm_map(docs, removeWords, stopwords("english"))
docs <- tm_map(docs, removeWords, c("no", "yes")
docs <- tm_map(docs, removePunctuation)
docs <- tm_map(docs, stripWhitespace)
dtm <- TermDocumentMatrix(docs)
m <- as.matrix(dtm)
v <- sort(rowSums(m),decreasing=TRUE)
d <- data.frame(word = names(v),freq=v)
head(d, 10)
set.seed(1337)
wordcloud(words = d$word, freq = d$freq, min.freq = 1,
          max.words=200, random.order=FALSE, rot.per=0.47, 
          colors= "black")
```
![image](https://user-images.githubusercontent.com/80591420/112725406-d7e2dc00-8ef6-11eb-85fb-5f897801cfc5.png)

A nuvem de palavras acima evidencia que “world”, “great”, “african” e “south” são as 4 palavras mais importantes no discurso de Nelson Mandela em Harvard.

```
|            |word        | freq|
|:-----------|:-----------|----:|
|world       |world       |   21|
|great       |great       |   11|
|african     |african     |    7|
|south       |south       |    7|
|institution |institution |    6|
|africa      |africa      |    6|
|will        |will        |    6|
|one         |one         |    5|
|also        |also        |    5|
|century     |century     |    4|
```
Podemos traçar as frequências em que ocorrem as palavras
```
ggplot(discu) +
  aes(x = word, weight = freq) +
  geom_bar(fill = "#000000") +
  labs(x = "Words", y = "Freq", title = "Discurso Mandela") +
  theme_minimal()
  ```
![image](https://user-images.githubusercontent.com/80591420/112727936-9f95ca80-8f03-11eb-89e7-7d424dfca12d.png)

# [Google Trends](https://trends.google.com.br/trends/?geo=BR) com R
 
O Google Trends é um recurso de tendências de pesquisa que mostra a frequência com que um determinado termo é inserido no mecanismo de pesquisa do Google em relação ao volume total de pesquisa do site em um determinado período de tempo e pode ser usado para pesquisas comparativas de palavras-chave e também descobrir picos acionados por eventos no volume de pesquisas de palavras-chave. Esta abordagem é sobre como traçar e plotar os dados do Google Trends utilizando o pacote ```gtrendsR```.  <br/>
```
library(dplyr)
library(ggplot2)
library(gtrendsR)
library(maps)
library(ggpmisc)
library(knitr)

desenho <- gtrends("caverna do dragão",
                          geo = "BR",
                          time = "all")
names(desenho)

names(desenho)
[1] "interest_over_time"  "interest_by_country" "interest_by_
" 
[4] "interest_by_dma"     "interest_by_city"    "related_topics"     
[7] "related_queries" 
```

Os resultados são armazenados em uma lista com nomes autoexplicativos. Abaixo, o interesse em “caverna do dragão” estratificado por estado e cidade e classificado do maior para o menor.
```
kable(desenho$interest_by_region %>%
  arrange(desc(hits)) %>%
    head(5))
  
|location                     | hits|keyword           |geo |gprop |
|:----------------------------|----:|:-----------------|:---|:-----|
|State of Pernambuco          |  100|caverna do dragão |BR  |web   |
|State of Amapa               |   98|caverna do dragão |BR  |web   |
|State of Rio Grande do Norte |   94|caverna do dragão |BR  |web   |
|State of Bahia               |   93|caverna do dragão |BR  |web   |
|State of Ceará               |   89|caverna do dragão |BR  |web   |

kable(desenho$interest_by_city %>%
  arrange(desc(hits)) %>%
    head(5))
    
|location   | hits|keyword           |geo |gprop |
|:----------|----:|:-----------------|:---|:-----|
|Santos     |  100|caverna do dragão |BR  |web   |
|Natal      |   97|caverna do dragão |BR  |web   |
|São Luiz   |   96|caverna do dragão |BR  |web   |
|Recife     |   93|caverna do dragão |BR  |web   |
|Salvador   |   91|caverna do dragão |BR  |web   |
```
Aqui a plotagem 
```
ggplot(desenho$interest_over_time, aes(x= date, y= hits))+
  geom_line()+
  ggtitle("Procura pelo termo 'Caverna do Dragão'")
  ```
  
![image](https://user-images.githubusercontent.com/80591420/112877709-790b9700-909d-11eb-853b-16d1f68796ca.png)

O gráfico apresenta três picos, em 2004 e 2011 quando ocorreram reprises do desenho, o outro pico em 2019 quando uma propaganda de carros relacionada ao tema fez sucesso no Brasil.
```
ggplot() +
  geom_map(data = world,
  map = world,
  aes(x = long, y = lat, map_id = region),
  fill="#ffffff", color="#ffffff", size=0.15) +
  geom_map(data = my_df,
  map = world,
  aes(fill = hits, map_id = region),
  color="#ffffff", size=0.15) +
  scale_fill_continuous(low = 'grey', high = 'red') +
  theme(axis.ticks = element_blank(),
  axis.text = element_blank(),
  axis.title = element_blank())
  interest_over_time
  ```
![image](https://user-images.githubusercontent.com/80591420/112878397-43b37900-909e-11eb-91f0-144387ae2c08.png)


Como esperado, os principais interessados na palavra-chave “caverna do dragão” estão em países que falam português, Brasil na América do Sul, alguns países africanos e Portugal na europa.

# [Análise exploratória dos dados](http://localhost:22117/session/EDA_Report.html) com R.


# Data science

sociological_data é dataframe de dados fictícios de uma pesquisa sociológica relacionada aos níveis de renda em várias regiões do mundo.<br/>

Uso: dados_ sociológicos<br/>
````anual_income_ppp````A renda anual do indivíduo em dólares americanos PPP ajustados <br/>
````average_wk_hrs````O número médio de horas semanais trabalhadas pelo indivíduo<br/>
`````region `````A região do mundo onde o indivíduo vive<br/>
````job_type````Se o indivíduo trabalha em uma profissão qualificada ou não especializada<br/>
````gender```` O gênero do indivíduo<br/>
````family_size````O tamanho da família de dependentes do indivíduo<br/>
````work_distance````A distância entre a residência do indivíduo e o local de trabalho em quilômetros<br/>
````languages````O número de idiomas falados fluentemente pelo indivíduo<br/>

summary(sociological_data)<br/>
```
Call:
lm(formula = annual_income_ppp ~ ., data = sociological_data)

Residuals:
   Min     1Q Median     3Q    Max 
-64707  -5104    -66   5351  35211 

Coefficients:
                                       Estimate Std. Error t value Pr(>|t|)    
(Intercept)                            59904.23    5382.46  11.130  < 2e-16 ***
average_wk_hrs                           -98.41      59.20  -1.662 0.096612 .  
education_months                         143.16      10.72  13.356  < 2e-16 ***
regionCentral Asia                    -11304.33    3069.41  -3.683 0.000237 ***
regionEastern Asia                      -299.83    3077.31  -0.097 0.922393    
regionEastern Europe                   -9017.38    2793.04  -3.229 0.001264 ** 
regionLatin America and the Caribbean   -327.64    2819.62  -0.116 0.907505    
regionMelanesia                        -4492.99    3078.55  -1.459 0.144598    
regionMicronesia                      -13264.10    3751.10  -3.536 0.000415 ***
regionNorthern Africa                    489.11    3140.16   0.156 0.876238    
regionNorthern America                 12923.06    3672.29   3.519 0.000443 ***
regionNorthern Europe                   -112.65    2670.06  -0.042 0.966352    
regionPolynesia                        -2428.23    3276.50  -0.741 0.458717    
regionSouth-eastern Asia               -6348.53    3022.13  -2.101 0.035793 *  
regionSouthern Asia                    -3276.90    3020.01  -1.085 0.278024    
regionSouthern Europe                   4634.46    2719.88   1.704 0.088551 .  
regionSub-Saharan Africa              -21528.30    2908.22  -7.403 1.95e-13 ***
regionWestern Asia                      1873.39    2858.57   0.655 0.512312    
regionWestern Europe                    7236.01    2730.88   2.650 0.008119 ** 
job_typeUnskilled                      -6126.74     909.98  -6.733 2.16e-11 ***
genderM                                10181.62     785.91  12.955  < 2e-16 ***
family_size                               16.16     172.44   0.094 0.925341    
work_distance                            -42.68     122.73  -0.348 0.728055    
languages                              -1727.83    2977.93  -0.580 0.561838    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 9688 on 2012 degrees of freedom
  (582 observations deleted due to missingness)
Multiple R-squared:  0.8039,	Adjusted R-squared:  0.8017 
F-statistic: 358.7 on 23 and 2012 DF,  p-value: < 2.2e-16
```
Para um determinado preditor, a estatística t avalia se há ou não associação significativa entre o preditor e a variável de resultado, ou seja, se o coeficiente beta do preditor é significativamente diferente de zero.

Pode-se ver que o número total de meses gastos pelo indivíduo na educação formal, região, genero e o tipo de trabalho estão significativamente associadas a renda anual do indivíduo em dólares americanos, as demais variáveis não associadas serão retiradas do modelo.

 plot(allEffects(modelo1), grid = T)

  
![image](https://user-images.githubusercontent.com/80591420/119045995-8acd3480-b992-11eb-8b3d-9616957a34ec.png)

Fica claro quais variáveis apresentam maior influencia através do gráfico de efeitos, o indivíduo com mais horas de estudo, mão de obra qualificada e do genero masculino recebem maiores salários.

![image](https://user-images.githubusercontent.com/80591420/119045700-2f9b4200-b992-11eb-9b9e-56a14365172e.png)
Para obter uma verificação abrangente package````performance```` fornece muitas funções para verificar as suposições do modelo, como ````check_collinearity()````, ````check_normality()````ou ````check_heteroscedasticity()````.
A multicolinearidade não deve ser confundida com uma forte correlação bruta entre preditores. O que importa é a associação entre uma ou mais variáveis preditoras, condicional às outras variáveis do modelo . Em suma, multicolinearidade significa que, uma vez que você conhece o efeito de um preditor, o valor de conhecer o outro preditor é bastante baixo. Assim, um dos preditores não ajuda muito em termos de melhor compreensão do modelo ou previsão do resultado. Como consequência, se a multicolinearidade for um problema, o modelo parece sugerir que os preditores em questão não parecem estar associados de forma confiável com o resultado (McElreath 2020, capítulo 6.1 ).
````check_normality()````chama stats::shapiro.test e verifica os resíduos padronizados (ou resíduos estudentizados para modelos mistos) para distribuição normal. Observe que este teste formal quase sempre produz resultados significativos para a distribuição de resíduos e inspeção visual (por exemplo, gráficos QQ) são preferíveis.


Observação

summary(modelo2)
```
Call:
lm(formula = annual_income_ppp ~ education_months + region + 
    gender + job_type, data = sociological_data)

Residuals:
   Min     1Q Median     3Q    Max 
-66359  -5046     25   4886  33889 

Coefficients:
                                        Estimate Std. Error t value Pr(>|t|)    
(Intercept)                            57436.286   2555.934  22.472  < 2e-16 ***
education_months                         134.002      8.386  15.980  < 2e-16 ***
regionCentral Asia                    -10549.775   2200.182  -4.795 1.72e-06 ***
regionEastern Asia                      -877.617   2275.111  -0.386 0.699716    
regionEastern Europe                   -9042.417   1947.126  -4.644 3.59e-06 ***
regionLatin America and the Caribbean   1128.249   1961.885   0.575 0.565285    
regionMelanesia                        -4669.154   2273.918  -2.053 0.040139 *  
regionMicronesia                       -9629.471   2558.368  -3.764 0.000171 ***
regionNorthern Africa                  -1585.937   2157.115  -0.735 0.462277    
regionNorthern America                 13942.300   2991.376   4.661 3.31e-06 ***
regionNorthern Europe                   -436.582   1857.451  -0.235 0.814194    
regionPolynesia                        -1381.672   2523.092  -0.548 0.584007    
regionSouth-eastern Asia               -3974.156   2056.779  -1.932 0.053442 .  
regionSouthern Asia                    -4531.998   2136.889  -2.121 0.034031 *  
regionSouthern Europe                   4405.960   1893.994   2.326 0.020081 *  
regionSub-Saharan Africa              -21480.602   2023.338 -10.616  < 2e-16 ***
regionWestern Asia                      2439.525   1983.281   1.230 0.218793    
regionWestern Europe                    6403.548   1914.595   3.345 0.000836 ***
genderM                                10103.649    653.319  15.465  < 2e-16 ***
job_typeUnskilled                      -8102.711    801.130 -10.114  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 9385 on 2572 degrees of freedom
  (26 observations deleted due to missingness)
Multiple R-squared:  0.799,	Adjusted R-squared:  0.7975 
F-statistic: 538.1 on 19 and 2572 DF,  p-value: < 2.2e-16
```
![image](https://user-images.githubusercontent.com/80591420/119048282-66268c00-b995-11eb-88eb-3a41b84c1f6f.png)

![image](https://user-images.githubusercontent.com/80591420/119048484-a980fa80-b995-11eb-96c4-c314de3aa0a1.png)

model_performance(modelo1) 
```
AIC       |       BIC |    R2 | R2 (adj.) |     RMSE |    Sigma
---------------------------------------------------------------
43179.244 | 43319.713 | 0.804 |     0.802 | 9630.810 | 9688.080
```
model_performance(modelo2) 
```
AIC       |       BIC |    R2 | R2 (adj.) |     RMSE |    Sigma
---------------------------------------------------------------
54795.040 | 54918.104 | 0.799 |     0.798 | 9348.681 | 9384.959
```
plot(compare_performance(modelo1, modelo2))<br/>
Curiosamente podemos comparar modelos entre si e traçar imediatamente o que ocorre:

![image](https://user-images.githubusercontent.com/80591420/119050503-56f50d80-b998-11eb-995a-fe639575781d.png)
Observa-se a partir do gráfico que o modelo 1 é melhor para previsões, por conseguinte o modelo 2  descreve melhor os dados, é possível verficar a presença de outliers e por ai vai...

## Gráficos

library(VennDiagram)<br/>
```
venn.plot <- draw.quad.venn
  area1 = 72,
  area2 = 86,
  area3 = 50,
  area4 = 52,
  n12 = 44,
  n13 = 27,
  n14 = 32,
  n23 = 38,
  n24 = 32,
  n34 = 20,
  n123 = 18,
  n124 = 17,
  n134 = 11,
  n234 = 13,
  n1234 = 6,
  category = c("Primeiro", "Segundo", "Rerceiro", "Quarto"),
  fill = c("orange", "red", "green", "blue"),
  lty = "dashed",
  cex = 2,
  cat.cex = 2,
  cat.col = c("orange", "red", "green", "blue")
)
```
![image](https://user-images.githubusercontent.com/80591420/119235072-1071f100-bb07-11eb-97d4-7938ec535188.png)

library(ggplot2)<br/>
library(dplyr)<br/>
require(maps)<br/>
require(viridis)<br/>
```
mapa_mundi <- map_data("world")
ggplot(world_map, aes(x = long, y = lat, group = group)) +
geom_polygon(fill="lightblue", colour = "white")
```
![image](https://user-images.githubusercontent.com/80591420/119235211-bcb3d780-bb07-11eb-92d7-be9fa8d73961.png)

library(ggplot2)
```
head(midwest)
ggplot(midwest, aes(x=area, y= poptotal))+
  geom_point(aes(col=state, size = popdensity))
xlim(c(0,0.05))+
  ylim(c(0,5000))
  ```
  ![image](https://user-images.githubusercontent.com/80591420/119235324-4cf21c80-bb08-11eb-827a-5be47ffb9473.png)


library(ggplot2)
```
head(diamonds)
ggplot(diamonds, aes(carat, colour = cut, fill = cut)) +
  geom_density(bw = 0.2)+
  theme_538()
```

![image](https://user-images.githubusercontent.com/80591420/119235391-b70ac180-bb08-11eb-9939-f254d91723c3.png)


library(grid)<br>
library(ggplot2)
```
ggplot(data = mtcars, aes(x = wt, y = mpg)) +
  geom_rect(xmin = 3.5, ymin = -Inf, xmax = 4, ymax = Inf,
            fill = "lightgray") +
  geom_point() + 
  theme_bw()
  ```
![image](https://user-images.githubusercontent.com/80591420/119235512-49ab6080-bb09-11eb-9cc6-91966ee70eec.png)

library(ggplot2)
```
ggplot(economics, aes(x = date, y = psavert)) +
  geom_area(fill="lightblue", color="black") +
  labs(title = "Poupança",
       x = "anos",
       y = "Poupança pessoal")
 ```
![image](https://user-images.githubusercontent.com/80591420/119235681-1fa66e00-bb0a-11eb-8b83-8c650e48a69e.png)

library(ggplot2)
```
head(iris)
ggplot(iris, aes(Sepal.Length)) +
  geom_histogram(aes(fill = Species, color = Species), bins = 15, 
                 position = "identity", alpha = 0.2) +
  scale_fill_viridis_d() +
  scale_color_viridis_d()
  ```


![image](https://user-images.githubusercontent.com/80591420/119235834-b6732a80-bb0a-11eb-8a27-c46531a75f58.png)


library(ggpubr)
```
head(mtcars)

ggqqplot(mtcars, x = "mpg",
         ggtheme = theme_bw())
```
![image](https://user-images.githubusercontent.com/80591420/119235991-782a3b00-bb0b-11eb-8f0a-3bde0806b366.png)

<!DOCTYPE html>

<html  dir="ltr" lang="pt-br" xml:lang="pt-br">
<head>
    <title>Teste seus conhecimentos do módulo</title>
    <link rel="shortcut icon" href="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme/1627934520/favicon" />
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<meta name="keywords" content="moodle, Teste seus conhecimentos do módulo" />
<link rel="stylesheet" type="text/css" href="https://ufprvirtual.ufpr.br/theme/yui_combo.php?rollup/3.17.2/yui-moodlesimple-min.css" /><script id="firstthemesheet" type="text/css">/** Required in order to fix style inclusion problems in IE with YUI **/</script><link rel="stylesheet" type="text/css" href="https://ufprvirtual.ufpr.br/theme/styles.php/ufprvirtual/1627934520_1/all" />
<script>
//<![CDATA[
var M = {}; M.yui = {};
M.pageloadstarttime = new Date();
M.cfg = {"wwwroot":"https:\/\/ufprvirtual.ufpr.br","sesskey":"qZFwbRAj8P","sessiontimeout":"7200","themerev":"1627934520","slasharguments":1,"theme":"ufprvirtual","iconsystemmodule":"core\/icon_system_fontawesome","jsrev":"1627934520","admin":"admin","svgicons":true,"usertimezone":"Am\u00e9rica\/S\u00e3o_Paulo","contextid":840935,"langrev":1628319965,"templaterev":"1627934520"};var yui1ConfigFn = function(me) {if(/-skin|reset|fonts|grids|base/.test(me.name)){me.type='css';me.path=me.path.replace(/\.js/,'.css');me.path=me.path.replace(/\/yui2-skin/,'/assets/skins/sam/yui2-skin')}};
var yui2ConfigFn = function(me) {var parts=me.name.replace(/^moodle-/,'').split('-'),component=parts.shift(),module=parts[0],min='-min';if(/-(skin|core)$/.test(me.name)){parts.pop();me.type='css';min=''}
if(module){var filename=parts.join('-');me.path=component+'/'+module+'/'+filename+min+'.'+me.type}else{me.path=component+'/'+component+'.'+me.type}};
YUI_config = {"debug":false,"base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/3.17.2\/","comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","combine":true,"filter":null,"insertBefore":"firstthemesheet","groups":{"yui2":{"base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/2in3\/2.9.0\/build\/","comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","combine":true,"ext":false,"root":"2in3\/2.9.0\/build\/","patterns":{"yui2-":{"group":"yui2","configFn":yui1ConfigFn}}},"moodle":{"name":"moodle","base":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?m\/1627934520\/","combine":true,"comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","ext":false,"root":"m\/1627934520\/","patterns":{"moodle-":{"group":"moodle","configFn":yui2ConfigFn}},"filter":null,"modules":{"moodle-core-notification":{"requires":["moodle-core-notification-dialogue","moodle-core-notification-alert","moodle-core-notification-confirm","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-core-notification-dialogue":{"requires":["base","node","panel","escape","event-key","dd-plugin","moodle-core-widget-focusafterclose","moodle-core-lockscroll"]},"moodle-core-notification-alert":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-confirm":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-exception":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-ajaxexception":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-blocks":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification"]},"moodle-core-chooserdialogue":{"requires":["base","panel","moodle-core-notification"]},"moodle-core-actionmenu":{"requires":["base","event","node-event-simulate"]},"moodle-core-lockscroll":{"requires":["plugin","base-build"]},"moodle-core-dragdrop":{"requires":["base","node","io","dom","dd","event-key","event-focus","moodle-core-notification"]},"moodle-core-formchangechecker":{"requires":["base","event-focus","moodle-core-event"]},"moodle-core-languninstallconfirm":{"requires":["base","node","moodle-core-notification-confirm","moodle-core-notification-alert"]},"moodle-core-handlebars":{"condition":{"trigger":"handlebars","when":"after"}},"moodle-core-popuphelp":{"requires":["moodle-core-tooltip"]},"moodle-core-tooltip":{"requires":["base","node","io-base","moodle-core-notification-dialogue","json-parse","widget-position","widget-position-align","event-outside","cache-base"]},"moodle-core-event":{"requires":["event-custom"]},"moodle-core-maintenancemodetimer":{"requires":["base","node"]},"moodle-core_availability-form":{"requires":["base","node","event","event-delegate","panel","moodle-core-notification-dialogue","json"]},"moodle-backup-backupselectall":{"requires":["node","event","node-event-simulate","anim"]},"moodle-backup-confirmcancel":{"requires":["node","node-event-simulate","moodle-core-notification-confirm"]},"moodle-course-formatchooser":{"requires":["base","node","node-event-simulate"]},"moodle-course-categoryexpander":{"requires":["node","event-key"]},"moodle-course-util":{"requires":["node"],"use":["moodle-course-util-base"],"submodules":{"moodle-course-util-base":{},"moodle-course-util-section":{"requires":["node","moodle-course-util-base"]},"moodle-course-util-cm":{"requires":["node","moodle-course-util-base"]}}},"moodle-course-management":{"requires":["base","node","io-base","moodle-core-notification-exception","json-parse","dd-constrain","dd-proxy","dd-drop","dd-delegate","node-event-delegate"]},"moodle-course-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-course-coursebase","moodle-course-util"]},"moodle-form-passwordunmask":{"requires":[]},"moodle-form-dateselector":{"requires":["base","node","overlay","calendar"]},"moodle-form-shortforms":{"requires":["node","base","selector-css3","moodle-core-event"]},"moodle-question-chooser":{"requires":["moodle-core-chooserdialogue"]},"moodle-question-preview":{"requires":["base","dom","event-delegate","event-key","core_question_engine"]},"moodle-question-searchform":{"requires":["base","node"]},"moodle-availability_completion-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_date-form":{"requires":["base","node","event","io","moodle-core_availability-form"]},"moodle-availability_grade-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_group-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_grouping-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_profile-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-mod_assign-history":{"requires":["node","transition"]},"moodle-mod_attendance-groupfilter":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-recordings":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_bigbluebuttonbn-modform":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-imports":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-broker":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_bigbluebuttonbn-rooms":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_chamado-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-mod_chamado-chamadobase","moodle-mod_chamado-util-base","moodle-mod_chamado-util-page","moodle-mod_chamado-util-slot","moodle-course-util"]},"moodle-mod_chamado-chamadobase":{"requires":["base","node"]},"moodle-mod_chamado-autosave":{"requires":["base","node","event","event-valuechange","node-event-delegate","io-form"]},"moodle-mod_chamado-util":{"requires":["node","moodle-core-actionmenu"],"use":["moodle-mod_chamado-util-base"],"submodules":{"moodle-mod_chamado-util-base":{},"moodle-mod_chamado-util-slot":{"requires":["node","moodle-mod_chamado-util-base"]},"moodle-mod_chamado-util-page":{"requires":["node","moodle-mod_chamado-util-base"]}}},"moodle-mod_chamado-toolboxes":{"requires":["base","node","event","event-key","io","moodle-mod_chamado-chamadobase","moodle-mod_chamado-util-slot","moodle-core-notification-ajaxexception"]},"moodle-mod_chamado-modform":{"requires":["base","node","event"]},"moodle-mod_chamado-questionchooser":{"requires":["moodle-core-chooserdialogue","moodle-mod_chamado-util","querystring-parse"]},"moodle-mod_mediagallery-base":{"requires":["base","node","selector-css3","dd-constrain","dd-proxy","dd-drop","dd-plugin","moodle-core-notification","event"]},"moodle-mod_mediagallery-mediabox":{"requires":["base","node","selector-css3"]},"moodle-mod_quiz-questionchooser":{"requires":["moodle-core-chooserdialogue","moodle-mod_quiz-util","querystring-parse"]},"moodle-mod_quiz-modform":{"requires":["base","node","event"]},"moodle-mod_quiz-util":{"requires":["node","moodle-core-actionmenu"],"use":["moodle-mod_quiz-util-base"],"submodules":{"moodle-mod_quiz-util-base":{},"moodle-mod_quiz-util-slot":{"requires":["node","moodle-mod_quiz-util-base"]},"moodle-mod_quiz-util-page":{"requires":["node","moodle-mod_quiz-util-base"]}}},"moodle-mod_quiz-quizbase":{"requires":["base","node"]},"moodle-mod_quiz-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-mod_quiz-quizbase","moodle-mod_quiz-util-base","moodle-mod_quiz-util-page","moodle-mod_quiz-util-slot","moodle-course-util"]},"moodle-mod_quiz-toolboxes":{"requires":["base","node","event","event-key","io","moodle-mod_quiz-quizbase","moodle-mod_quiz-util-slot","moodle-core-notification-ajaxexception"]},"moodle-mod_quiz-autosave":{"requires":["base","node","event","event-valuechange","node-event-delegate","io-form"]},"moodle-message_airnotifier-toolboxes":{"requires":["base","node","io"]},"moodle-filter_glossary-autolinker":{"requires":["base","node","io-base","json-parse","event-delegate","overlay","moodle-core-event","moodle-core-notification-alert","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-filter_mathjaxloader-loader":{"requires":["moodle-core-event"]},"moodle-editor_atto-editor":{"requires":["node","transition","io","overlay","escape","event","event-simulate","event-custom","node-event-html5","node-event-simulate","yui-throttle","moodle-core-notification-dialogue","moodle-core-notification-confirm","moodle-editor_atto-rangy","handlebars","timers","querystring-stringify"]},"moodle-editor_atto-plugin":{"requires":["node","base","escape","event","event-outside","handlebars","event-custom","timers","moodle-editor_atto-menu"]},"moodle-editor_atto-menu":{"requires":["moodle-core-notification-dialogue","node","event","event-custom"]},"moodle-editor_atto-rangy":{"requires":[]},"moodle-report_eventlist-eventfilter":{"requires":["base","event","node","node-event-delegate","datatable","autocomplete","autocomplete-filters"]},"moodle-report_loglive-fetchlogs":{"requires":["base","event","node","io","node-event-delegate"]},"moodle-gradereport_grader-gradereporttable":{"requires":["base","node","event","handlebars","overlay","event-hover"]},"moodle-gradereport_history-userselector":{"requires":["escape","event-delegate","event-key","handlebars","io-base","json-parse","moodle-core-notification-dialogue"]},"moodle-tool_capability-search":{"requires":["base","node"]},"moodle-tool_lp-dragdrop-reorder":{"requires":["moodle-core-dragdrop"]},"moodle-tool_monitor-dropdown":{"requires":["base","event","node"]},"moodle-assignfeedback_editpdf-editor":{"requires":["base","event","node","io","graphics","json","event-move","event-resize","transition","querystring-stringify-simple","moodle-core-notification-dialog","moodle-core-notification-alert","moodle-core-notification-warning","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-atto_accessibilitychecker-button":{"requires":["color-base","moodle-editor_atto-plugin"]},"moodle-atto_accessibilityhelper-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_align-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_bold-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_charmap-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_clear-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_collapse-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_emojipicker-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_emoticon-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_equation-button":{"requires":["moodle-editor_atto-plugin","moodle-core-event","io","event-valuechange","tabview","array-extras"]},"moodle-atto_h5p-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_html-button":{"requires":["promise","moodle-editor_atto-plugin","moodle-atto_html-beautify","moodle-atto_html-codemirror","event-valuechange"]},"moodle-atto_html-beautify":{},"moodle-atto_html-codemirror":{"requires":["moodle-atto_html-codemirror-skin"]},"moodle-atto_image-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_indent-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_italic-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_link-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_managefiles-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_managefiles-usedfiles":{"requires":["node","escape"]},"moodle-atto_media-button":{"requires":["moodle-editor_atto-plugin","moodle-form-shortforms"]},"moodle-atto_noautolink-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_orderedlist-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_recordrtc-button":{"requires":["moodle-editor_atto-plugin","moodle-atto_recordrtc-recording"]},"moodle-atto_recordrtc-recording":{"requires":["moodle-atto_recordrtc-button"]},"moodle-atto_rtl-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_strike-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_styles-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_subscript-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_superscript-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_table-button":{"requires":["moodle-editor_atto-plugin","moodle-editor_atto-menu","event","event-valuechange"]},"moodle-atto_teamsmeeting-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_title-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_underline-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_undo-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_unorderedlist-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_wiris-button":{"requires":["moodle-editor_atto-plugin","get"]}}},"gallery":{"name":"gallery","base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/gallery\/","combine":true,"comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","ext":false,"root":"gallery\/1627934520\/","patterns":{"gallery-":{"group":"gallery"}}}},"modules":{"core_filepicker":{"name":"core_filepicker","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/repository\/filepicker.js","requires":["base","node","node-event-simulate","json","async-queue","io-base","io-upload-iframe","io-form","yui2-treeview","panel","cookie","datatable","datatable-sort","resize-plugin","dd-plugin","escape","moodle-core_filepicker","moodle-core-notification-dialogue"]},"core_comment":{"name":"core_comment","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/comment\/comment.js","requires":["base","io-base","node","json","yui2-animation","overlay","escape"]},"mathjax":{"name":"mathjax","fullpath":"https:\/\/cdn.jsdelivr.net\/npm\/mathjax@2.7.8\/MathJax.js?delayStartupUntil=configured"},"core_question_flags":{"name":"core_question_flags","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/question\/flags.js","requires":["base","dom","event-delegate","io-base"]},"core_question_engine":{"name":"core_question_engine","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/question\/qengine.js","requires":["node","event"]},"mod_quiz":{"name":"mod_quiz","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/mod\/quiz\/module.js","requires":["base","dom","event-delegate","event-key","core_question_engine","moodle-core-formchangechecker"]}}};
M.yui.loader = {modules: {}};

//]]>
</script>



<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-164264713-2"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'UA-164264713-2');
</script>

    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=0, minimal-ui">

    <link href="https://fonts.googleapis.com/css?family=Poppins" rel="stylesheet">
</head>

<body  id="page-mod-quiz-attempt" class="format-timelines  path-mod path-mod-quiz chrome dir-ltr lang-pt_br yui-skin-sam yui3-skin-sam ufprvirtual-ufpr-br pagelayout-incourse course-16981 context-840935 cmid-413386 category-790 ">

<div id="page-wrapper">

    <div>
    <a class="sr-only sr-only-focusable" href="#maincontent">Ir para o conteúdo principal</a>
</div><script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/babel-polyfill/polyfill.min.js"></script>
<script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/polyfills/polyfill.js"></script>
<script src="https://ufprvirtual.ufpr.br/theme/yui_combo.php?rollup/3.17.2/yui-moodlesimple-min.js"></script><script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/javascript-static.js"></script>
<script>
//<![CDATA[
document.body.className += ' jsenabled';
//]]>
</script>



    
    <nav class="fixed-top navbar navbar-light navbar-expand moodle-has-zindex">
        
      
      <nav class="fixed-top"> 
          <div id="barra-brasil" style="background:#7F7F7F; height: 20px; padding:0 0 0 10px;display:block;">
              <ul id="menu-barra-temp" style="list-style:none;">
                  <li style="display:inline; float:left;padding-right:10px; margin-right:10px; border-right:1px solid #EDEDED">
                      <a href="http://brasil.gov.br" style="font-family:sans,sans-serif; text-decoration:none; color:white;">Portal do Governo Brasileiro</a>
                  </li>
              </ul>
          </div>
      </nav>        
            <div  data-region="drawer-toggle" class="navdraweropen-secundary">
                <button aria-expanded="false" aria-controls="nav-drawer" type="button" class="btn nav-link float-sm-left mr-1" data-action="toggle-drawer" data-side="left" data-preference="drawer-open-nav"><i class="slicon-menu"></i><span class="sr-only">Painel lateral</span></button>
            </div>
        
        <a href="https://ufprvirtual.ufpr.br" class="navbar-brand has-logo
                ">
                <span class="logo d-none d-sm-inline">
                    <img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/logo" alt="UFPR Virtual">
                </span>
                   
            
                <span class="site-name">UFPR Virtual</span> 
            
        </a>
    
        <ul class="navbar-nav d-none d-md-flex custom-menus">
            <!-- custom_menu -->
            
            <!-- page_heading_menu -->
            
        </ul>
        <ul class="nav navbar-nav ml-auto">
            <div class="d-none d-lg-block">
                
            </div>
             <li class="nav-item access-menu">
                 <span class="dropdown nav-item">
                     <a class="dropdown nav-link" href="javascript:(function(){d=document;jf=d.createElement('script');jf.src=('https:'==document.location.protocol?'https://ssl.atbar.org/c':'http://c.atbar.org')+'/ATBar2/ATBar.min.user.js';jf.type='text/javascript';jf.id='ToolBar';d.getElementsByTagName('head')[0].appendChild(jf);})();" title="Launch ATbar to adjust this webpage, have it read aloud and other functions.">
                         <img class="icon " alt="Acessibilidade" title="Acessibilidade" src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/acessibilidadeicon" />
                     </a>
                 </span>
             </li>   
            <li class="nav-item lang-menu">
                <div class="dropdown nav-item">
    <a class="dropdown-toggle nav-link" id="drop-down-6111e96fb64e46111e96faa15a8" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false" href="#">
        <i class="slicon-globe"></i>
    </a>
    <div class="dropdown-menu" aria-labelledby="drop-down-6111e96fb64e46111e96faa15a8">
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/attempt.php?attempt=441792&amp;cmid=413386&amp;lang=en" title="English ‎(en)‎">English ‎(en)‎</a>
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/attempt.php?attempt=441792&amp;cmid=413386&amp;lang=fr" title="Français ‎(fr)‎">Français ‎(fr)‎</a>
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/attempt.php?attempt=441792&amp;cmid=413386&amp;lang=pt_br" title="Português - Brasil ‎(pt_br)‎">Português - Brasil ‎(pt_br)‎</a>
    </div>
</div>
            </li>
    
            <div class="popover-region collapsed popover-region-notifications"
    id="nav-notification-popover-container" data-userid="12740"
    data-region="popover-region">
    <div class="popover-region-toggle nav-link"
        data-region="popover-region-toggle"
        role="button"
        aria-controls="popover-region-container-6111e96fb73fa6111e96faa15a9"
        aria-haspopup="true"
        aria-label="Mostrar janela de notificações sem as novas notificações"
        tabindex="0">
                <i class="slicon-bell" title="Alternar menu de notificações"></i>
        <div class="count-container hidden" data-region="count-container">0</div>

    </div>
    <div 
        id="popover-region-container-6111e96fb73fa6111e96faa15a9"
        class="popover-region-container"
        data-region="popover-region-container"
        aria-expanded="false"
        aria-hidden="true"
        aria-label="Janela de notificação"
        role="region">
        <div class="popover-region-header-container">
            <h3 class="popover-region-header-text" data-region="popover-region-header-text">Notificações</h3>
            <div class="popover-region-header-actions" data-region="popover-region-header-actions">        <div class="hover-tooltip-container">
                    <a class="mark-all-read-button"
                    href="#"
                    title="Marcar tudo como lido"
                    data-action="mark-all-read"
                    role="button">
                    <span class="normal-icon"><i class="slicon-check" alt="Marcar tudo como lido"></i></span>
                    <span class="loading-icon icon-no-margin"><i class="icon fa fa-circle-o-notch fa-spin fa-fw "  title="Carregando" aria-label="Carregando"></i></span>
                </a>

    <div class="hover-tooltip">
        Marcar tudo como lido
    </div>
</div>
        <div class="hover-tooltip-container">
                    <a href="https://ufprvirtual.ufpr.br/message/notificationpreferences.php?userid=12740"
                    title="Preferências de notificação">
                    <i class="slicon-settings" alt="Preferências de notificação"></i>
                </a>

    <div class="hover-tooltip">
        Preferências de notificação
    </div>
</div>
</div>
        </div>
        <div class="popover-region-content-container" data-region="popover-region-content-container">
            <div class="popover-region-content" data-region="popover-region-content">
                        <div class="all-notifications"
            data-region="all-notifications"
            role="log"
            aria-busy="false"
            aria-atomic="false"
            aria-relevant="additions"></div>
        <div class="empty-message" tabindex="0" data-region="empty-message">Você não tem nenhuma notificação</div>

            </div>
            <span class="loading-icon icon-no-margin"><i class="icon fa fa-circle-o-notch fa-spin fa-fw "  title="Carregando" aria-label="Carregando"></i></span>
        </div>
                <a class="see-all-link"
                    href="https://ufprvirtual.ufpr.br/message/output/popup/notifications.php">
                    <div class="popover-region-footer-container">
                        <div class="popover-region-seeall-text">Mostrar todos</div>
                    </div>
                </a>
    </div>
</div><div class="popover-region collapsed" data-region="popover-region-messages">
    <a id="message-drawer-toggle-6111e96fb89346111e96faa15a10" class="nav-link d-inline-block popover-region-toggle position-relative" href="#"
            role="button">
        <i class="icon fa slicon-bubble fa-fw "  title="Alternar menu de mensagens" aria-label="Alternar menu de mensagens"></i>
        <div class="count-container hidden" data-region="count-container"
        aria-label="Há 0 conversas não lidas">0</div>
    </a>
    <span class="sr-only sr-only-focusable" data-region="jumpto" tabindex="-1"></span></div>
    
            <li class="usermenu"><div class="action-menu moodle-actionmenu nowrap-items d-inline" id="action-menu-1" data-enhance="moodle-core-actionmenu">

        <div class="menubar d-flex " id="action-menu-1-menubar" role="menubar">

            


                <div class="action-menu-trigger">
                    <div class="dropdown">
                        <a href="#" tabindex="0" class="d-inline-block  dropdown-toggle icon-no-margin" id="action-menu-toggle-1" aria-label="" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false" aria-controls="action-menu-1-menu">
                            
                            <span class="userbutton"><span class="usertext"></span><span class="avatars"><span class="avatar current"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/u/f2" class="userpicture defaultuserpic" width="35" height="35" alt="" /></span></span></span>
                                
                            <b class="caret"></b>
                        </a>
                            <div class="dropdown-menu dropdown-menu-right menu  align-tr-br" id="action-menu-1-menu" data-rel="menu-content" aria-labelledby="action-menu-toggle-1" role="menu" data-align="tr-br">
                                                                <a href="https://ufprvirtual.ufpr.br/user/profile.php?id=12740" class="dropdown-item text-username menu-action" role="menuitem" aria-labelledby="actionmenuaction-1">
                                <i class="icon fa slicon-user fa-fw "  title="Lincoln Souza Linhares" aria-label="Lincoln Souza Linhares"></i>
                                <span class="menu-action-text" id="actionmenuaction-1">Lincoln Souza Linhares</span>
                        </a>
                    <div class="dropdown-divider" role="presentation"><span class="filler">&nbsp;</span></div>
                                                                <a href="https://ufprvirtual.ufpr.br/my/" class="dropdown-item menu-action" role="menuitem" data-title="mymoodle,admin" aria-labelledby="actionmenuaction-2">
                                <i class="icon fa slicon-speedometer fa-fw "  title="Painel" aria-label="Painel"></i>
                                <span class="menu-action-text" id="actionmenuaction-2">Painel</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/user/profile.php?id=12740" class="dropdown-item menu-action" role="menuitem" data-title="profile,moodle" aria-labelledby="actionmenuaction-3">
                                <i class="icon fa slicon-user fa-fw "  title="Perfil" aria-label="Perfil"></i>
                                <span class="menu-action-text" id="actionmenuaction-3">Perfil</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/grade/report/overview/index.php" class="dropdown-item menu-action" role="menuitem" data-title="grades,grades" aria-labelledby="actionmenuaction-4">
                                <i class="icon fa slicon-book-open fa-fw "  title="Notas" aria-label="Notas"></i>
                                <span class="menu-action-text" id="actionmenuaction-4">Notas</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/message/index.php" class="dropdown-item menu-action" role="menuitem" data-title="messages,message" aria-labelledby="actionmenuaction-5">
                                <i class="icon fa slicon-bubble fa-fw "  title="Mensagens" aria-label="Mensagens"></i>
                                <span class="menu-action-text" id="actionmenuaction-5">Mensagens</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/user/preferences.php" class="dropdown-item menu-action" role="menuitem" data-title="preferences,moodle" aria-labelledby="actionmenuaction-6">
                                <i class="icon fa slicon-wrench fa-fw "  title="Preferências" aria-label="Preferências"></i>
                                <span class="menu-action-text" id="actionmenuaction-6">Preferências</span>
                        </a>
                    <div class="dropdown-divider" role="presentation"><span class="filler">&nbsp;</span></div>
                                                                <a href="https://ufprvirtual.ufpr.br/login/logout.php?sesskey=qZFwbRAj8P" class="dropdown-item menu-action" role="menuitem" data-title="logout,moodle" aria-labelledby="actionmenuaction-7">
                                <i class="icon fa slicon-logout fa-fw "  title="Sair" aria-label="Sair"></i>
                                <span class="menu-action-text" id="actionmenuaction-7">Sair</span>
                        </a>
                            </div>
                    </div>
                </div>

        </div>

</div></li>
        </ul>
    </nav>

    <div id="page" class="container-fluid">
        <header id="page-header" class="row">
    <div class="col-12 pt-3 pb-3">
        <div class="card ">
            <div class="card-body ">
                <div class="d-flex align-items-center">
                    <div class="mr-auto">
                        <div class="page-context-header"><div class="page-header-headings"><h1>ERE3 - Cálculo2</h1></div></div>
                    </div>

                    <div class="header-actions-container flex-shrink-0" data-region="header-actions-container">
                    </div>
                </div>
                <div class="d-flex flex-wrap">
                    <div id="page-navbar">
                        <nav role="navigation">
    <ol class="breadcrumb">
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/my/" >Painel</a></li>
                <li class="breadcrumb-item"><span class="no-link">Minhas salas</span></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/course/view.php?id=16981" title="ERE3 - Cálculo2">ERE3_Calculo2_2021</a></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=3" >Módulo 2 - Cálculo Diferencial</a></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/mod/quiz/view.php?id=413386" title="Questionário">Teste seus conhecimentos do módulo</a></li>
    </ol>
</nav>
                    </div>
                    <div class="ml-auto d-flex">
                        
                    </div>
                    <div id="course-header">
                        
                    </div>
                </div>
            </div>
        </div>
    </div>
</header>

        <div id="page-content" class="row">
            <div id="region-main-box" class="col-md-9 col-sm-12 col-12">
                <section id="region-main">
                    <div class="card">
                        <div class="card-body">
                            <span class="notifications" id="user-notifications"></span>
                            <div role="main"><span id="maincontent"></span><form action="https://ufprvirtual.ufpr.br/mod/quiz/processattempt.php?cmid=413386" method="post" enctype="multipart/form-data" accept-charset="utf-8" id="responseform"><div><div id="question-530200-5" class="que multichoice deferredfeedback answersaved"><div class="info"><h3 class="no">Questão <span class="qno">1</span></h3><div class="state">Resposta salva</div><div class="grade">Vale 2,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q530200:5_:flagged" value="0" /><input type="checkbox" id="q530200:5_:flaggedcheckbox" name="q530200:5_:flagged" value="1" /><input type="hidden" value="qaid=3104103&amp;qubaid=530200&amp;qid=35642767&amp;slot=5&amp;checksum=ecea953067f983ee089d536dd2c6c518&amp;sesskey=qZFwbRAj8P&amp;newstate=" class="questionflagpostdata" /><label id="q530200:5_:flaggedlabel" for="q530200:5_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q530200:5_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q530200:5_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation">Seja <span class="nolink">\( c(t)=(x(t),y(t)) \)</span> a curva no plano definida por <span class="nolink">\( x(t) = \mathrm{ln}(4t + 1) \)</span> e <span class="nolink">\( y(t) = \mathrm{ln}(4t + 1) \)</span>. Seja <span class="nolink">\( f(x,y) \)</span> uma função de duas variáveis tal que <span class="nolink">\( \vec{\nabla} f(0,0) = (4 , 4) \)</span>. Então <span class="nolink">\( \dfrac{d}{dt} f(x(t),y(t))\Bigg \rvert_{t=0} \)</span> é igual a:</span></div><div class="ablock"><div class="prompt">Escolha uma opção:</div><div class="answer"><div class="r0"><input type="radio" name="q530200:5_answer" value="0" id="q530200:5_answer0" aria-labelledby="q530200:5_answer0_label" /><div class="d-flex w-100" id="q530200:5_answer0_label" data-region="answer-label"><span class="answernumber">a. </span><div class="flex-fill ml-1"><span class="filter_mathjaxloader_equation"><span class="nolink">\( 32 \)</span></span></div></div> </div>
<div class="r1"><input type="radio" name="q530200:5_answer" value="1" id="q530200:5_answer1" aria-labelledby="q530200:5_answer1_label" checked="checked" /><div class="d-flex w-100" id="q530200:5_answer1_label" data-region="answer-label"><span class="answernumber">b. </span><div class="flex-fill ml-1"><span class="filter_mathjaxloader_equation"><span class="nolink">\( 34 \)</span></span></div></div> </div>
<div class="r0"><input type="radio" name="q530200:5_answer" value="2" id="q530200:5_answer2" aria-labelledby="q530200:5_answer2_label" /><div class="d-flex w-100" id="q530200:5_answer2_label" data-region="answer-label"><span class="answernumber">c. </span><div class="flex-fill ml-1"><span class="filter_mathjaxloader_equation"><span class="nolink">\( 30 \)</span></span></div></div> </div>
<div class="r1"><input type="radio" name="q530200:5_answer" value="3" id="q530200:5_answer3" aria-labelledby="q530200:5_answer3_label" /><div class="d-flex w-100" id="q530200:5_answer3_label" data-region="answer-label"><span class="answernumber">d. </span><div class="flex-fill ml-1"><span class="filter_mathjaxloader_equation"><span class="nolink">\( 2 \)</span></span></div></div> </div>
<div class="r0"><input type="radio" name="q530200:5_answer" value="4" id="q530200:5_answer4" aria-labelledby="q530200:5_answer4_label" /><div class="d-flex w-100" id="q530200:5_answer4_label" data-region="answer-label"><span class="answernumber">e. </span><div class="flex-fill ml-1"><span class="filter_mathjaxloader_equation"><span class="nolink">\( 28 \)</span></span></div></div> </div>
</div><div id="q530200:5_clearchoice" class="qtype_multichoice_clearchoice"><input type="radio" name="q530200:5_answer" id="q530200:5_answer-1" value="-1" class="sr-only" aria-hidden="true" /><label for="q530200:5_answer-1"><a tabindex="0" role="button" class="btn btn-link ml-3 mt-n1 mb-n1" href="#">Limpar minha escolha</a></label></div></div></div></div></div><div id="question-530200-6" class="que numerical deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">2</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 2,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q530200:6_:flagged" value="0" /><input type="checkbox" id="q530200:6_:flaggedcheckbox" name="q530200:6_:flagged" value="1" /><input type="hidden" value="qaid=3104104&amp;qubaid=530200&amp;qid=37372420&amp;slot=6&amp;checksum=865c6f4a5ca003bd5ae92274ca17aa99&amp;sesskey=qZFwbRAj8P&amp;newstate=" class="questionflagpostdata" /><label id="q530200:6_:flaggedlabel" for="q530200:6_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q530200:6_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q530200:6_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p>Dada <span class="nolink">\( f(x,y) = {\rm sen}(x) + {\rm cos}(y) \)</span> e <span class="nolink">\( \vec v \)</span> um vetor unitário formando ângulo de <span class="nolink">\( \theta \)</span> com o eixo <span class="nolink">\( x \)</span>. Qual deve ser o valor de <span class="nolink">\(\theta \)</span>, (com <span class="nolink">\( 0 \leqslant \theta \leqslant 360 \)</span> ) para que a derivada direcional <span class="nolink">\(\displaystyle{\frac{\partial f}{\partial \vec v}(0,\pi / 2)} \)</span> seja igual a <span class="nolink">\( \sqrt{2} \)</span>?&nbsp;<br></p><p><br></p><p>(OBS: A resposta deve ser dada em graus e deve conter apenas um número inteiro. Por exemplo, caso a medida do ângulo seja 210 graus, então a resposta deverá conter apenas o número inteiro 210.)</p></span></div><div class="ablock form-inline"><label for="q530200:6_answer">Resposta:</label><span class="answer"><input type="text" name="q530200:6_answer" id="q530200:6_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-530200-3" class="que numerical deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">3</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 2,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q530200:3_:flagged" value="0" /><input type="checkbox" id="q530200:3_:flaggedcheckbox" name="q530200:3_:flagged" value="1" /><input type="hidden" value="qaid=3104101&amp;qubaid=530200&amp;qid=35644132&amp;slot=3&amp;checksum=0fe421ef627f078636947fdbbccda705&amp;sesskey=qZFwbRAj8P&amp;newstate=" class="questionflagpostdata" /><label id="q530200:3_:flaggedlabel" for="q530200:3_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q530200:3_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q530200:3_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p>Seja <span class="nolink">\(f(x,y) = x^4 y^2\)</span>. Determine o valor de <span class="nolink">\(K\)</span> para que <span class="nolink">\(f\)</span> satisfaça a equação&nbsp;&nbsp; diferencial <br><span class="nolink">\(<br>&nbsp;x\frac{\partial f}{\partial x} + <br>&nbsp;y\frac{\partial f}{\partial y} <br>&nbsp;=<br>&nbsp;Kf \, .<br>\)</span><br></p></span></div><div class="ablock form-inline"><label for="q530200:3_answer">Resposta:</label><span class="answer"><input type="text" name="q530200:3_answer" id="q530200:3_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-530200-2" class="que numerical deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">4</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q530200:2_:flagged" value="0" /><input type="checkbox" id="q530200:2_:flaggedcheckbox" name="q530200:2_:flagged" value="1" /><input type="hidden" value="qaid=3104100&amp;qubaid=530200&amp;qid=35643914&amp;slot=2&amp;checksum=cf27b0d737c84c93fb0ed52a084a10cf&amp;sesskey=qZFwbRAj8P&amp;newstate=" class="questionflagpostdata" /><label id="q530200:2_:flaggedlabel" for="q530200:2_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q530200:2_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q530200:2_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p>Calcule o valor do limite <span class="nolink">\(&nbsp; \displaystyle{ {\rm lim}_{(x,y)\to(0,0)} \frac{-48 xy}{2x^2+2y^2} } \)</span> quando nos aproximamos de <span class="nolink">\( (0,0)\)</span> pelo caminho <span class="nolink">\( \gamma(t)=(-t,t)\)</span>.<br></p></span></div><div class="ablock form-inline"><label for="q530200:2_answer">Resposta:</label><span class="answer"><input type="text" name="q530200:2_answer" id="q530200:2_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-530200-1" class="que multichoice deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">5</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q530200:1_:flagged" value="0" /><input type="checkbox" id="q530200:1_:flaggedcheckbox" name="q530200:1_:flagged" value="1" /><input type="hidden" value="qaid=3104099&amp;qubaid=530200&amp;qid=35644194&amp;slot=1&amp;checksum=30fe3cfa9bf9a23e5689a617ed510711&amp;sesskey=qZFwbRAj8P&amp;newstate=" class="questionflagpostdata" /><label id="q530200:1_:flaggedlabel" for="q530200:1_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q530200:1_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q530200:1_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation">O domínio natural da função de duas variáveis <span class="nolink">\( f(x,y) = \frac{\sqrt{ 4 x^{2}+y^{2} }}{ x y } \)</span> é</span></div><div class="ablock"><div class="prompt">Escolha uma opção:</div><div class="answer"><div class="r0"><input type="radio" name="q530200:1_answer" value="0" id="q530200:1_answer0" aria-labelledby="q530200:1_answer0_label" /><div class="d-flex w-100" id="q530200:1_answer0_label" data-region="answer-label"><span class="answernumber">a. </span><div class="flex-fill ml-1">O plano menos uma reta.</div></div> </div>
<div class="r1"><input type="radio" name="q530200:1_answer" value="1" id="q530200:1_answer1" aria-labelledby="q530200:1_answer1_label" /><div class="d-flex w-100" id="q530200:1_answer1_label" data-region="answer-label"><span class="answernumber">b. </span><div class="flex-fill ml-1">Um círculo.</div></div> </div>
<div class="r0"><input type="radio" name="q530200:1_answer" value="2" id="q530200:1_answer2" aria-labelledby="q530200:1_answer2_label" /><div class="d-flex w-100" id="q530200:1_answer2_label" data-region="answer-label"><span class="answernumber">c. </span><div class="flex-fill ml-1">O plano menos duas retas que se interceptam.</div></div> </div>
<div class="r1"><input type="radio" name="q530200:1_answer" value="3" id="q530200:1_answer3" aria-labelledby="q530200:1_answer3_label" /><div class="d-flex w-100" id="q530200:1_answer3_label" data-region="answer-label"><span class="answernumber">d. </span><div class="flex-fill ml-1">O exterior de um círculo.</div></div> </div>
<div class="r0"><input type="radio" name="q530200:1_answer" value="4" id="q530200:1_answer4" aria-labelledby="q530200:1_answer4_label" /><div class="d-flex w-100" id="q530200:1_answer4_label" data-region="answer-label"><span class="answernumber">e. </span><div class="flex-fill ml-1">O plano menos duas retas paralelas.</div></div> </div>
</div><div id="q530200:1_clearchoice" class="qtype_multichoice_clearchoice sr-only" aria-hidden="true"><input type="radio" name="q530200:1_answer" id="q530200:1_answer-1" value="-1" class="sr-only" aria-hidden="true" checked="checked" /><label for="q530200:1_answer-1"><a tabindex="-1" role="button" class="btn btn-link ml-3 mt-n1 mb-n1" href="#">Limpar minha escolha</a></label></div></div></div></div></div><div id="question-530200-4" class="que calculatedsimple deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">6</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 2,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q530200:4_:flagged" value="0" /><input type="checkbox" id="q530200:4_:flaggedcheckbox" name="q530200:4_:flagged" value="1" /><input type="hidden" value="qaid=3104102&amp;qubaid=530200&amp;qid=35644068&amp;slot=4&amp;checksum=f6ea0f20c297167481627611906426ec&amp;sesskey=qZFwbRAj8P&amp;newstate=" class="questionflagpostdata" /><label id="q530200:4_:flaggedlabel" for="q530200:4_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q530200:4_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q530200:4_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p>Um tanque aberto em forma de cone&nbsp; invertido foi medido visando estimar a quantidade de tinta necessária para pintá-lo, e foram obtidos os seguintes resultados:<br><br>&nbsp;- A abertura superior do tanque tem radio de 6 metros.<br>&nbsp;<br>&nbsp;- A profundidade é 12 metros.<br>&nbsp;<br>Cada uma destas medidas tem uma incerteza de 0,3 metros. Use a aproximação linear para estimar, até duas casas decimais, o erro máximo em metros cometido no cálculo da área total da superfície do tanque.<br><br>Observações: <br><br>- Lembre que o erro é em valor absoluto; assim, sempre será um número&nbsp; positivo.<br><br>- Área de um cone de raio da base <span class="nolink">\(r\)</span> e altura <span class="nolink">\(h\)</span>:&nbsp; <span class="nolink">\(\pi r\sqrt{r^2 + h^2}\)</span><br><br>- Pode usar&nbsp; a aproximação <span class="nolink">\(\pi \approx 3,14 \)</span>.<br><br>- Na caixa de resposta, só coloque o número, <b>não</b> as unidades.<br><br></p></span></div><div class="ablock form-inline"><label for="q530200:4_answer">Resposta:</label><span class="answer"><input type="text" name="q530200:4_answer" id="q530200:4_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div class="submitbtns"><input type="submit" name="next" value="Finalizar tentativa ..." class="mod_quiz-next-nav btn btn-primary" /></div><input type="hidden" name="attempt" value="441792" /><input type="hidden" name="thispage" value="0" id="followingpage" /><input type="hidden" name="nextpage" value="-1" /><input type="hidden" name="timeup" value="0" id="timeup" /><input type="hidden" name="sesskey" value="qZFwbRAj8P" /><input type="hidden" name="scrollpos" value="" id="scrollpos" /><input type="hidden" name="slots" value="5,6,3,2,1,4" /></div></form><div id="connection-error" style="display: none;" role="alert"><p>Conexão de rede perdida. (Salvamento automático falhou). Anote quaisquer respostas registradas nesta página nos últimos minutos e tente conectar-se novamente. Quando a conexão for restabelecida, suas respostas devem ser salvas e esta mensagem irá desaparecer.</p>
</div><div id="connection-ok" style="display: none;" role="alert"><p>Conexão de rede restaurada. Você pode continuar com segurança.</p>
</div></div>
                            
                        </div>
                    </div>
                </section>
            </div>
                <div class="col-md-3 col-sm-12 col-12">
                    <aside id="block-region-side-pre" class="block-region" data-blockregion="side-pre" data-droptarget="1"><a href="#sb-1" class="sr-only sr-only-focusable">Pular &lt;span id=&quot;mod_quiz_navblock_title&quot;&gt;Navegação do questionário&lt;/span&gt;</a>

<section id="mod_quiz_navblock"
     class=" block block_fake  card mb-3"
     role="navigation"
     data-block="_fake"
          aria-labelledby="instance-fakeid-6111e96faa9e4-header"
     >

    <div class="card-body p-3">

            <h5 id="instance-fakeid-6111e96faa9e4-header" class="card-title d-inline"><span id="mod_quiz_navblock_title">Navegação do questionário</span></h5>


        <div class="card-text content mt-3">
            <div id="quiznojswarning">Atenção: esses links não vão salvar as suas respostas. Use o botão 'Próximo' na parte inferior da página.</div><div class="qn_buttons clearfix multipages"><a class="qnbutton answersaved free btn thispage" id="quiznavbutton5" title="Resposta salva" data-quiz-page="0" href="#"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>1<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton notyetanswered free btn thispage" id="quiznavbutton6" title="Ainda não respondida" data-quiz-page="0" href="#question-530200-6"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>2<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton notyetanswered free btn thispage" id="quiznavbutton3" title="Ainda não respondida" data-quiz-page="0" href="#question-530200-3"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>3<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton notyetanswered free btn thispage" id="quiznavbutton2" title="Ainda não respondida" data-quiz-page="0" href="#question-530200-2"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>4<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton notyetanswered free btn thispage" id="quiznavbutton1" title="Ainda não respondida" data-quiz-page="0" href="#question-530200-1"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>5<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton notyetanswered free btn thispage" id="quiznavbutton4" title="Ainda não respondida" data-quiz-page="0" href="#question-530200-4"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>6<span class="accesshide"> Esta página <span class="flagstate"></span></span></a></div><div class="othernav"><a class="endtestlink aalink" href="https://ufprvirtual.ufpr.br/mod/quiz/summary.php?attempt=441792&amp;cmid=413386">Finalizar tentativa ...</a><div id="quiz-timer" role="timer" aria-atomic="true" aria-relevant="text">Tempo restante <span id="quiz-time-left"></span></div></div>
            <div class="footer"></div>
            
        </div>

    </div>

</section>

  <span id="sb-1"></span></aside>
                </div>
        </div>
    </div>

    <div id="nav-drawer" data-region="drawer" class="hidden-print moodle-has-zindex closed" aria-hidden="true" tabindex="-1">
            <div id="btn-navdraweropen" data-region="drawer-toggle" class="d-inline-block mr-3 drawer-toggle">
                <button aria-expanded="false" aria-controls="nav-drawer" type="button" class="btn nav-link float-sm-left mr-1" data-action="toggle-drawer" data-side="left" data-preference="drawer-open-nav"><i class="slicon-menu"></i><span class="sr-only">Painel lateral</span></button>
            </div>
        <ul class="list-group metismenu">
                    <li class="list-group-item" data-key="course-sections">
                        <a class="m-l-0 has-arrow" href="javascript:void(0);">
                            <span class="text">Seções da disciplina</span>
                        </a>
                        <ul>
                                    <li class="list-group-item list-group-item-action " data-key="111164">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=1">
                                            <span class="text">Apresentação</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111165">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=2">
                                            <span class="text">Módulo 1 - Funções Vetoriais</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111166">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=3">
                                            <span class="text">Módulo 2 - Cálculo Diferencial</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111167">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=4">
                                            <span class="text">Módulo 3 - Máximos e Mínimos</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111180">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=5">
                                            <span class="text">Módulo 4 - Integração</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111181">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=6">
                                            <span class="text">Módulo 5 - Cálculo Vetorial</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="114035">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=7">
                                            <span class="text">Segundas Chamadas e Exame Final</span>
                                        </a>
                                    </li>
                        </ul>
                    </li>
                        <li class="list-group-item list-group-item-action " data-key="participants">
                            <a href="https://ufprvirtual.ufpr.br/user/index.php?id=16981">
                                <span class="text m-l-0">Participantes</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="certificates">
                            <a href="https://ufprvirtual.ufpr.br/theme/moove/certificates.php?id=16981">
                                <span class="text m-l-0">Certificados</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="competencies">
                            <a href="https://ufprvirtual.ufpr.br/admin/tool/lp/coursecompetencies.php?courseid=16981">
                                <span class="text m-l-0">Competências</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="grades">
                            <a href="https://ufprvirtual.ufpr.br/grade/report/index.php?id=16981">
                                <span class="text m-l-0">Notas</span>
                            </a>
                        </li>
                </ul>
                <ul class="list-group metismenu">
                        <li class="list-group-item list-group-item-action " data-key="myhome">
                            <a href="https://ufprvirtual.ufpr.br/my/">
                                <span class="text m-l-0">Painel</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="home">
                            <a href="https://ufprvirtual.ufpr.br/?redirect=0">
                                <span class="text m-l-0">Página inicial do site</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="calendar">
                            <a href="https://ufprvirtual.ufpr.br/calendar/view.php?view=month&amp;course=16981">
                                <span class="text m-l-0">Calendário</span>
                            </a>
                        </li>
                    <li class="list-group-item" data-key="mycourses">
                        <a class="m-l-0 has-arrow" href="javascript:void(0);">
                            <span class="text">Minhas salas</span>
                        </a>
                        <ul>
                                    <li class="list-group-item list-group-item-action " data-key="14547">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=14547">
                                            <span class="text">PE2_CMCALCULO2_2020</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="4053">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=4053">
                                            <span class="text">CE084_2020_EST_352965</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="1876">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=1876">
                                            <span class="text">CM042_2020_EST_352963</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="10045">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=10045">
                                            <span class="text">PE_CE095_2020_357893</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="16981">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981">
                                            <span class="text">ERE3_Calculo2_2021</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="18951">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=18951">
                                            <span class="text">20202_CE083_394464</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="6123">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=6123">
                                            <span class="text">Estudantes</span>
                                        </a>
                                    </li>
                        </ul>
                    </li>
                        <li class="list-group-item list-group-item-action " data-key="privatefiles">
                            <a href="https://ufprvirtual.ufpr.br/user/files.php">
                                <span class="text m-l-0">Meus arquivos</span>
                            </a>
                        </li>
        </ul>    </div>

</div>


<div id="top-footer">
  <div class="container-fluid">
    <div class="row">
        <div class="col-md-7 contact">
            
        </div>
        <div class="col-md-5 social">
            <div class="plugins_standard_footer_html"><div class="tool_dataprivacy"><a href="https://ufprvirtual.ufpr.br/admin/tool/dataprivacy/summary.php">Resumo de retenção de dados</a></div><div><p style="margin-bottom:0px">Obter o aplicativo para dispositivos móveis</p><a title="Obter o aplicativo para dispositivos móveis" href="https://play.google.com/store/apps/details?id=br.ufpr.ufprvirtual&amp;hl=pt-pt"><img src='https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/google_play_badge_ptbr' style='height:72px'></img></a><a title="Obter o aplicativo para dispositivos móveis" href="https://apps.apple.com/br/app/ufpr-virtual/id1529174904"><img src='https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/app_store_badge_ptbr' style='height:50px'></img></a></div></div>
        </div>
    </div>
  </div>
</div>


  
  <div class="footer" >
      
      <div class="texto_rodape large-2 medium-2" >
              <span >
                  <img id="image-footer" src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/logo_white">
              </span>
  
      </div>
      <div class="texto_rodape large-8 medium-8">
          <br>CIPEAD - Coordenadoria de Integração de Políticas de Educação a Distância da Universidade Federal do Paraná
          <br>Praça Santos Andrade, 50 - Centro - Telefone:(41)3310-2657 - CEP:80.020-300 - Curitiba/PR
      </div>
      <div class="texto_rodape large-2 medium-2 social_media" >
          <a href="https://www.facebook.com/Cipead-UFPR-505827566179117/" 
              onclick="javascript:window.open('https://www.facebook.com/Cipead-UFPR-505827566179117/'); 
              return false;">
              <img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/facebook_logo_white" title="CIPEAD no Facebook" alt="CIPEAD no Facebook" width="45" height="45">
          </a>
       </div>
    
  </div>
  <div id="sub-footer" class="footer" >
      <div class="copyright texto_rodape large-2 medium-2">
              Direitos autorais - ícones: <a href="https://www.flaticon.com/" style="color:#fff">Flat Icon</a>
              
              
      </div>  
  </div>

<script>
//<![CDATA[
var require = {
    baseUrl : 'https://ufprvirtual.ufpr.br/lib/requirejs.php/1627934520/',
    // We only support AMD modules with an explicit define() statement.
    enforceDefine: true,
    skipDataMain: true,
    waitSeconds : 0,

    paths: {
        jquery: 'https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/jquery/jquery-3.5.1.min',
        jqueryui: 'https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/jquery/ui-1.12.1/jquery-ui.min',
        jqueryprivate: 'https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/requirejs/jquery-private'
    },

    // Custom jquery config map.
    map: {
      // '*' means all modules will get 'jqueryprivate'
      // for their 'jquery' dependency.
      '*': { jquery: 'jqueryprivate' },
      // Stub module for 'process'. This is a workaround for a bug in MathJax (see MDL-60458).
      '*': { process: 'core/first' },

      // 'jquery-private' wants the real jQuery module
      // though. If this line was not here, there would
      // be an unresolvable cyclic dependency.
      jqueryprivate: { jquery: 'jquery' }
    }
};

//]]>
</script>
<script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/requirejs/require.min.js"></script>
<script>
//<![CDATA[
M.util.js_pending("core/first");require(['core/first'], function() {
require(['core/prefetch']);
;
require(["media_videojs/loader"], function(loader) {
    loader.setUp(function(videojs) {
        videojs.options.flash.swf = "https://ufprvirtual.ufpr.br/media/player/videojs/videojs/video-js.swf";
videojs.addLanguage('pt-BR', {
  "Audio Player": "Reprodutor de áudio",
  "Video Player": "Reprodutor de vídeo",
  "Play": "Tocar",
  "Pause": "Pausar",
  "Replay": "Tocar novamente",
  "Current Time": "Tempo",
  "Duration": "Duração",
  "Remaining Time": "Tempo Restante",
  "Stream Type": "Tipo de Stream",
  "LIVE": "AO VIVO",
  "Loaded": "Carregado",
  "Progress": "Progresso",
  "Progress Bar": "Barra de progresso",
  "progress bar timing: currentTime={1} duration={2}": "{1} de {2}",
  "Fullscreen": "Tela Cheia",
  "Non-Fullscreen": "Tela Normal",
  "Mute": "Mudo",
  "Unmute": "Habilitar Som",
  "Playback Rate": "Velocidade",
  "Subtitles": "Legendas",
  "subtitles off": "Sem Legendas",
  "Captions": "Anotações",
  "captions off": "Sem Anotações",
  "Chapters": "Capítulos",
  "Descriptions": "Descrições",
  "descriptions off": "sem descrições",
  "Audio Track": "Faixa de áudio",
  "Volume Level": "Nível de volume",
  "You aborted the media playback": "Você parou a execução do vídeo.",
  "A network error caused the media download to fail part-way.": "Um erro na rede causou falha durante o download da mídia.",
  "The media could not be loaded, either because the server or network failed or because the format is not supported.": "A mídia não pode ser carregada, por uma falha de rede ou servidor ou o formato não é suportado.",
  "No compatible source was found for this media.": "Nenhuma fonte foi encontrada para esta mídia.",
  "The media playback was aborted due to a corruption problem or because the media used features your browser did not support.": "A reprodução foi interrompida devido à um problema de mídia corrompida ou porque a mídia utiliza funções que seu navegador não suporta.",
  "The media is encrypted and we do not have the keys to decrypt it.": "A mídia está criptografada e não temos as chaves para descriptografar.",
  "Play Video": "Tocar Vídeo",
  "Close": "Fechar",
  "Close Modal Dialog": "Fechar Diálogo Modal",
  "Modal Window": "Janela Modal",
  "This is a modal window": "Isso é uma janela-modal",
  "This modal can be closed by pressing the Escape key or activating the close button.": "Esta janela pode ser fechada pressionando a tecla de Escape.",
  ", opens captions settings dialog": ", abre as configurações de legendas de comentários",
  ", opens subtitles settings dialog": ", abre as configurações de legendas",
  ", opens descriptions settings dialog": ", abre as configurações",
  ", selected": ", selecionada",
  "captions settings": "configurações de legendas de comentários",
  "subtitles settings": "configurações de legendas",
  "descriptions settings": "configurações das descrições",
  "Text": "Texto",
  "White": "Branco",
  "Black": "Preto",
  "Red": "Vermelho",
  "Green": "Verde",
  "Blue": "Azul",
  "Yellow": "Amarelo",
  "Magenta": "Magenta",
  "Cyan": "Ciano",
  "Background": "Plano-de-Fundo",
  "Window": "Janela",
  "Transparent": "Transparente",
  "Semi-Transparent": "Semi-Transparente",
  "Opaque": "Opaco",
  "Font Size": "Tamanho da Fonte",
  "Text Edge Style": "Estilo da Borda",
  "None": "Nenhum",
  "Raised": "Elevado",
  "Depressed": "Acachapado",
  "Uniform": "Uniforme",
  "Dropshadow": "Sombra de projeção",
  "Font Family": "Família da Fonte",
  "Proportional Sans-Serif": "Sans-Serif(Sem serifa) Proporcional",
  "Monospace Sans-Serif": "Sans-Serif(Sem serifa) Monoespaçada",
  "Proportional Serif": "Serifa Proporcional",
  "Monospace Serif": "Serifa Monoespaçada",
  "Casual": "Casual",
  "Script": "Script",
  "Small Caps": "Maiúsculas Pequenas",
  "Reset": "Redefinir",
  "restore all settings to the default values": "restaurar todas as configurações aos valores padrão",
  "Done": "Salvar",
  "Caption Settings Dialog": "Caíxa-de-Diálogo das configurações de Legendas",
  "Beginning of dialog window. Escape will cancel and close the window.": "Iniciando a Janela-de-Diálogo. Pressionar Escape irá cancelar e fechar a janela.",
  "End of dialog window.": "Fim da Janela-de-Diálogo",
  "{1} is loading.": "{1} está carregando."
});

    });
});;

require(['jquery', 'core/custom_interaction_events'], function($, CustomEvents) {
    CustomEvents.define('#single_select6111e96faa15a7', [CustomEvents.events.accessibleChange]);
    $('#single_select6111e96faa15a7').on(CustomEvents.events.accessibleChange, function() {
        var ignore = $(this).find(':selected').attr('data-ignore');
        if (typeof ignore === typeof undefined) {
            $('#single_select_f6111e96faa15a6').submit();
        }
    });
});
;

require(['jquery', 'message_popup/notification_popover_controller'], function($, controller) {
    var container = $('#nav-notification-popover-container');
    var controller = new controller(container);
    controller.registerEventListeners();
    controller.registerListNavigationEventListeners();
});
;

require(
[
    'jquery',
    'core_message/message_popover'
],
function(
    $,
    Popover
) {
    var toggle = $('#message-drawer-toggle-6111e96fb89346111e96faa15a10');
    Popover.init(toggle);
});
;

require(['theme_boost/loader']);
require(['theme_boost/drawer'], function(mod) {
    mod.init();
});
;
M.util.js_pending('qtype_multichoice/answers'); require(['qtype_multichoice/answers'], function(amd) {amd.init("question-530200-5"); M.util.js_complete('qtype_multichoice/answers');});;
M.util.js_pending('qtype_multichoice/clearchoice'); require(['qtype_multichoice/clearchoice'], function(amd) {amd.init("question-530200-5", "q530200:5_clearchoice"); M.util.js_complete('qtype_multichoice/clearchoice');});;
M.util.js_pending('qtype_multichoice/answers'); require(['qtype_multichoice/answers'], function(amd) {amd.init("question-530200-1"); M.util.js_complete('qtype_multichoice/answers');});;
M.util.js_pending('qtype_multichoice/clearchoice'); require(['qtype_multichoice/clearchoice'], function(amd) {amd.init("question-530200-1", "q530200:1_clearchoice"); M.util.js_complete('qtype_multichoice/clearchoice');});;
M.util.js_pending('core/notification'); require(['core/notification'], function(amd) {amd.init(840935, [], true); M.util.js_complete('core/notification');});;
M.util.js_pending('core/log'); require(['core/log'], function(amd) {amd.setConfig({"level":"warn"}); M.util.js_complete('core/log');});;
M.util.js_pending('core/page_global'); require(['core/page_global'], function(amd) {amd.init(); M.util.js_complete('core/page_global');});M.util.js_complete("core/first");
});
//]]>
</script>
<script>
//<![CDATA[
M.str = {"moodle":{"lastmodified":"\u00daltima atualiza\u00e7\u00e3o","name":"Nome","error":"Erro","info":"Informa\u00e7\u00e3o","yes":"Sim","no":"N\u00e3o","cancel":"Cancelar","changesmadereallygoaway":"Voc\u00ea fez altera\u00e7\u00f5es. Est\u00e1 certo de que quer sair e abandonar suas altera\u00e7\u00f5es?","confirm":"Confirmar","areyousure":"Voc\u00ea tem certeza?","closebuttontitle":"Fechar","unknownerror":"Erro desconhecido","file":"Arquivo","url":"URL"},"repository":{"type":"Tipo","size":"Tamanho","invalidjson":"palavra JSON inv\u00e1lida","nofilesattached":"Nenhum arquivo anexado","filepicker":"Seletor de arquivos","logout":"Sair","nofilesavailable":"Nenhum arquivo dispon\u00edvel","norepositoriesavailable":"Desculpe, nenhum dos seus reposit\u00f3rios atuais pode retornar arquivos no formato solicitado.","fileexistsdialogheader":"Arquivo existe","fileexistsdialog_editor":"Um arquivo com este nome j\u00e1 foi anexado ao texto que voc\u00ea est\u00e1 editando.","fileexistsdialog_filemanager":"Um arquivo com este nome j\u00e1 foi anexado","renameto":"Renomear para \"{$a}\"","referencesexist":"Existem {$a} links para esse arquivo","select":"Selecione"},"admin":{"confirmdeletecomments":"Voc\u00ea est\u00e1 prestes a excluir coment\u00e1rios, tem certeza?","confirmation":"Confirma\u00e7\u00e3o"},"question":{"flagged":"Marcada"},"quiz":{"functiondisabledbysecuremode":"Esta funcionalidade est\u00e1 desativada no momento","startattempt":"Iniciar tentativa","timesup":"Acabou o tempo de dura\u00e7\u00e3o!"},"debug":{"debuginfo":"Informa\u00e7\u00f5es de depura\u00e7\u00e3o","line":"Linha","stacktrace":"Rastreamento de pilha"},"langconfig":{"labelsep":":&nbsp;"}};
//]]>
</script>
<script>
//<![CDATA[
(function() {Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.configure({"mathjaxconfig":"MathJax.Hub.Config({\r\n    config: [\"Accessible.js\", \"Safe.js\"],\r\n    errorSettings: { message: [\"!\"] },\r\n    skipStartupTypeset: true,\r\n    messageStyle: \"none\"\r\n});\r\n","lang":"pt-br"});
});
Y.use("moodle-mod_quiz-autosave",function() {M.mod_quiz.autosave.init("60");
});
 M.util.js_pending('random6111e96faa15a1'); Y.use('core_question_flags', function(Y) { M.core_question_flags.init(Y, "https:\/\/ufprvirtual.ufpr.br\/question\/toggleflag.php", [{"src":"https:\/\/ufprvirtual.ufpr.br\/theme\/image.php\/ufprvirtual\/core\/1627934520\/i\/unflagged","title":"Marcar quest\u00e3o para refer\u00eancia futura","alt":"N\u00e3o marcada"},{"src":"https:\/\/ufprvirtual.ufpr.br\/theme\/image.php\/ufprvirtual\/core\/1627934520\/i\/flagged","title":"Remover marca\u00e7\u00e3o","alt":"Marcada"}], ["Marcar quest\u00e3o","Remover marca\u00e7\u00e3o"]);  M.util.js_complete('random6111e96faa15a1'); });
 M.util.js_pending('random6111e96faa15a2'); Y.use('mod_quiz', function(Y) { M.mod_quiz.init_attempt_form(Y);  M.util.js_complete('random6111e96faa15a2'); });
 M.util.js_pending('random6111e96faa15a3'); Y.use('mod_quiz', function(Y) { M.mod_quiz.timer.init(Y, 517, false);  M.util.js_complete('random6111e96faa15a3'); });
 M.util.js_pending('random6111e96faa15a4'); Y.use('mod_quiz', function(Y) { M.mod_quiz.nav.init(Y);  M.util.js_complete('random6111e96faa15a4'); });
M.util.help_popups.setup(Y);
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
 M.util.js_pending('random6111e96faa15a20'); Y.on('domready', function() { M.util.js_complete("init");  M.util.js_complete('random6111e96faa15a20'); });
})();
//]]>
</script>


</body>
</html>


<!DOCTYPE html>

<html  dir="ltr" lang="pt-br" xml:lang="pt-br">
<head>
    <title>Exame Final -demais cursos</title>
    <link rel="shortcut icon" href="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme/1627934520/favicon" />
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<meta name="keywords" content="moodle, Exame Final -demais cursos" />
<link rel="stylesheet" type="text/css" href="https://ufprvirtual.ufpr.br/theme/yui_combo.php?rollup/3.17.2/yui-moodlesimple-min.css" /><script id="firstthemesheet" type="text/css">/** Required in order to fix style inclusion problems in IE with YUI **/</script><link rel="stylesheet" type="text/css" href="https://ufprvirtual.ufpr.br/theme/styles.php/ufprvirtual/1627934520_1/all" />
<script>
//<![CDATA[
var M = {}; M.yui = {};
M.pageloadstarttime = new Date();
M.cfg = {"wwwroot":"https:\/\/ufprvirtual.ufpr.br","sesskey":"XFg5yMJK6p","sessiontimeout":"7200","themerev":"1627934520","slasharguments":1,"theme":"ufprvirtual","iconsystemmodule":"core\/icon_system_fontawesome","jsrev":"1627934520","admin":"admin","svgicons":true,"usertimezone":"Am\u00e9rica\/S\u00e3o_Paulo","contextid":1029309,"langrev":1628579167,"templaterev":"1627934520"};var yui1ConfigFn = function(me) {if(/-skin|reset|fonts|grids|base/.test(me.name)){me.type='css';me.path=me.path.replace(/\.js/,'.css');me.path=me.path.replace(/\/yui2-skin/,'/assets/skins/sam/yui2-skin')}};
var yui2ConfigFn = function(me) {var parts=me.name.replace(/^moodle-/,'').split('-'),component=parts.shift(),module=parts[0],min='-min';if(/-(skin|core)$/.test(me.name)){parts.pop();me.type='css';min=''}
if(module){var filename=parts.join('-');me.path=component+'/'+module+'/'+filename+min+'.'+me.type}else{me.path=component+'/'+component+'.'+me.type}};
YUI_config = {"debug":false,"base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/3.17.2\/","comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","combine":true,"filter":null,"insertBefore":"firstthemesheet","groups":{"yui2":{"base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/2in3\/2.9.0\/build\/","comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","combine":true,"ext":false,"root":"2in3\/2.9.0\/build\/","patterns":{"yui2-":{"group":"yui2","configFn":yui1ConfigFn}}},"moodle":{"name":"moodle","base":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?m\/1627934520\/","combine":true,"comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","ext":false,"root":"m\/1627934520\/","patterns":{"moodle-":{"group":"moodle","configFn":yui2ConfigFn}},"filter":null,"modules":{"moodle-core-notification":{"requires":["moodle-core-notification-dialogue","moodle-core-notification-alert","moodle-core-notification-confirm","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-core-notification-dialogue":{"requires":["base","node","panel","escape","event-key","dd-plugin","moodle-core-widget-focusafterclose","moodle-core-lockscroll"]},"moodle-core-notification-alert":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-confirm":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-exception":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-ajaxexception":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-blocks":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification"]},"moodle-core-chooserdialogue":{"requires":["base","panel","moodle-core-notification"]},"moodle-core-actionmenu":{"requires":["base","event","node-event-simulate"]},"moodle-core-lockscroll":{"requires":["plugin","base-build"]},"moodle-core-dragdrop":{"requires":["base","node","io","dom","dd","event-key","event-focus","moodle-core-notification"]},"moodle-core-formchangechecker":{"requires":["base","event-focus","moodle-core-event"]},"moodle-core-languninstallconfirm":{"requires":["base","node","moodle-core-notification-confirm","moodle-core-notification-alert"]},"moodle-core-handlebars":{"condition":{"trigger":"handlebars","when":"after"}},"moodle-core-popuphelp":{"requires":["moodle-core-tooltip"]},"moodle-core-tooltip":{"requires":["base","node","io-base","moodle-core-notification-dialogue","json-parse","widget-position","widget-position-align","event-outside","cache-base"]},"moodle-core-event":{"requires":["event-custom"]},"moodle-core-maintenancemodetimer":{"requires":["base","node"]},"moodle-core_availability-form":{"requires":["base","node","event","event-delegate","panel","moodle-core-notification-dialogue","json"]},"moodle-backup-backupselectall":{"requires":["node","event","node-event-simulate","anim"]},"moodle-backup-confirmcancel":{"requires":["node","node-event-simulate","moodle-core-notification-confirm"]},"moodle-course-formatchooser":{"requires":["base","node","node-event-simulate"]},"moodle-course-categoryexpander":{"requires":["node","event-key"]},"moodle-course-util":{"requires":["node"],"use":["moodle-course-util-base"],"submodules":{"moodle-course-util-base":{},"moodle-course-util-section":{"requires":["node","moodle-course-util-base"]},"moodle-course-util-cm":{"requires":["node","moodle-course-util-base"]}}},"moodle-course-management":{"requires":["base","node","io-base","moodle-core-notification-exception","json-parse","dd-constrain","dd-proxy","dd-drop","dd-delegate","node-event-delegate"]},"moodle-course-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-course-coursebase","moodle-course-util"]},"moodle-form-passwordunmask":{"requires":[]},"moodle-form-dateselector":{"requires":["base","node","overlay","calendar"]},"moodle-form-shortforms":{"requires":["node","base","selector-css3","moodle-core-event"]},"moodle-question-chooser":{"requires":["moodle-core-chooserdialogue"]},"moodle-question-preview":{"requires":["base","dom","event-delegate","event-key","core_question_engine"]},"moodle-question-searchform":{"requires":["base","node"]},"moodle-availability_completion-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_date-form":{"requires":["base","node","event","io","moodle-core_availability-form"]},"moodle-availability_grade-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_group-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_grouping-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_profile-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-mod_assign-history":{"requires":["node","transition"]},"moodle-mod_attendance-groupfilter":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-recordings":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_bigbluebuttonbn-modform":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-imports":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-broker":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_bigbluebuttonbn-rooms":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_chamado-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-mod_chamado-chamadobase","moodle-mod_chamado-util-base","moodle-mod_chamado-util-page","moodle-mod_chamado-util-slot","moodle-course-util"]},"moodle-mod_chamado-chamadobase":{"requires":["base","node"]},"moodle-mod_chamado-autosave":{"requires":["base","node","event","event-valuechange","node-event-delegate","io-form"]},"moodle-mod_chamado-util":{"requires":["node","moodle-core-actionmenu"],"use":["moodle-mod_chamado-util-base"],"submodules":{"moodle-mod_chamado-util-base":{},"moodle-mod_chamado-util-slot":{"requires":["node","moodle-mod_chamado-util-base"]},"moodle-mod_chamado-util-page":{"requires":["node","moodle-mod_chamado-util-base"]}}},"moodle-mod_chamado-toolboxes":{"requires":["base","node","event","event-key","io","moodle-mod_chamado-chamadobase","moodle-mod_chamado-util-slot","moodle-core-notification-ajaxexception"]},"moodle-mod_chamado-modform":{"requires":["base","node","event"]},"moodle-mod_chamado-questionchooser":{"requires":["moodle-core-chooserdialogue","moodle-mod_chamado-util","querystring-parse"]},"moodle-mod_mediagallery-base":{"requires":["base","node","selector-css3","dd-constrain","dd-proxy","dd-drop","dd-plugin","moodle-core-notification","event"]},"moodle-mod_mediagallery-mediabox":{"requires":["base","node","selector-css3"]},"moodle-mod_quiz-questionchooser":{"requires":["moodle-core-chooserdialogue","moodle-mod_quiz-util","querystring-parse"]},"moodle-mod_quiz-modform":{"requires":["base","node","event"]},"moodle-mod_quiz-util":{"requires":["node","moodle-core-actionmenu"],"use":["moodle-mod_quiz-util-base"],"submodules":{"moodle-mod_quiz-util-base":{},"moodle-mod_quiz-util-slot":{"requires":["node","moodle-mod_quiz-util-base"]},"moodle-mod_quiz-util-page":{"requires":["node","moodle-mod_quiz-util-base"]}}},"moodle-mod_quiz-quizbase":{"requires":["base","node"]},"moodle-mod_quiz-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-mod_quiz-quizbase","moodle-mod_quiz-util-base","moodle-mod_quiz-util-page","moodle-mod_quiz-util-slot","moodle-course-util"]},"moodle-mod_quiz-toolboxes":{"requires":["base","node","event","event-key","io","moodle-mod_quiz-quizbase","moodle-mod_quiz-util-slot","moodle-core-notification-ajaxexception"]},"moodle-mod_quiz-autosave":{"requires":["base","node","event","event-valuechange","node-event-delegate","io-form"]},"moodle-message_airnotifier-toolboxes":{"requires":["base","node","io"]},"moodle-filter_glossary-autolinker":{"requires":["base","node","io-base","json-parse","event-delegate","overlay","moodle-core-event","moodle-core-notification-alert","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-filter_mathjaxloader-loader":{"requires":["moodle-core-event"]},"moodle-editor_atto-editor":{"requires":["node","transition","io","overlay","escape","event","event-simulate","event-custom","node-event-html5","node-event-simulate","yui-throttle","moodle-core-notification-dialogue","moodle-core-notification-confirm","moodle-editor_atto-rangy","handlebars","timers","querystring-stringify"]},"moodle-editor_atto-plugin":{"requires":["node","base","escape","event","event-outside","handlebars","event-custom","timers","moodle-editor_atto-menu"]},"moodle-editor_atto-menu":{"requires":["moodle-core-notification-dialogue","node","event","event-custom"]},"moodle-editor_atto-rangy":{"requires":[]},"moodle-report_eventlist-eventfilter":{"requires":["base","event","node","node-event-delegate","datatable","autocomplete","autocomplete-filters"]},"moodle-report_loglive-fetchlogs":{"requires":["base","event","node","io","node-event-delegate"]},"moodle-gradereport_grader-gradereporttable":{"requires":["base","node","event","handlebars","overlay","event-hover"]},"moodle-gradereport_history-userselector":{"requires":["escape","event-delegate","event-key","handlebars","io-base","json-parse","moodle-core-notification-dialogue"]},"moodle-tool_capability-search":{"requires":["base","node"]},"moodle-tool_lp-dragdrop-reorder":{"requires":["moodle-core-dragdrop"]},"moodle-tool_monitor-dropdown":{"requires":["base","event","node"]},"moodle-assignfeedback_editpdf-editor":{"requires":["base","event","node","io","graphics","json","event-move","event-resize","transition","querystring-stringify-simple","moodle-core-notification-dialog","moodle-core-notification-alert","moodle-core-notification-warning","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-atto_accessibilitychecker-button":{"requires":["color-base","moodle-editor_atto-plugin"]},"moodle-atto_accessibilityhelper-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_align-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_bold-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_charmap-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_clear-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_collapse-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_emojipicker-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_emoticon-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_equation-button":{"requires":["moodle-editor_atto-plugin","moodle-core-event","io","event-valuechange","tabview","array-extras"]},"moodle-atto_h5p-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_html-button":{"requires":["promise","moodle-editor_atto-plugin","moodle-atto_html-beautify","moodle-atto_html-codemirror","event-valuechange"]},"moodle-atto_html-beautify":{},"moodle-atto_html-codemirror":{"requires":["moodle-atto_html-codemirror-skin"]},"moodle-atto_image-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_indent-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_italic-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_link-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_managefiles-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_managefiles-usedfiles":{"requires":["node","escape"]},"moodle-atto_media-button":{"requires":["moodle-editor_atto-plugin","moodle-form-shortforms"]},"moodle-atto_noautolink-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_orderedlist-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_recordrtc-button":{"requires":["moodle-editor_atto-plugin","moodle-atto_recordrtc-recording"]},"moodle-atto_recordrtc-recording":{"requires":["moodle-atto_recordrtc-button"]},"moodle-atto_rtl-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_strike-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_styles-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_subscript-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_superscript-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_table-button":{"requires":["moodle-editor_atto-plugin","moodle-editor_atto-menu","event","event-valuechange"]},"moodle-atto_teamsmeeting-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_title-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_underline-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_undo-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_unorderedlist-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_wiris-button":{"requires":["moodle-editor_atto-plugin","get"]}}},"gallery":{"name":"gallery","base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/gallery\/","combine":true,"comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","ext":false,"root":"gallery\/1627934520\/","patterns":{"gallery-":{"group":"gallery"}}}},"modules":{"core_filepicker":{"name":"core_filepicker","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/repository\/filepicker.js","requires":["base","node","node-event-simulate","json","async-queue","io-base","io-upload-iframe","io-form","yui2-treeview","panel","cookie","datatable","datatable-sort","resize-plugin","dd-plugin","escape","moodle-core_filepicker","moodle-core-notification-dialogue"]},"core_comment":{"name":"core_comment","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/comment\/comment.js","requires":["base","io-base","node","json","yui2-animation","overlay","escape"]},"mathjax":{"name":"mathjax","fullpath":"https:\/\/cdn.jsdelivr.net\/npm\/mathjax@2.7.8\/MathJax.js?delayStartupUntil=configured"},"core_question_flags":{"name":"core_question_flags","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/question\/flags.js","requires":["base","dom","event-delegate","io-base"]},"core_question_engine":{"name":"core_question_engine","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/question\/qengine.js","requires":["node","event"]},"mod_quiz":{"name":"mod_quiz","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/mod\/quiz\/module.js","requires":["base","dom","event-delegate","event-key","core_question_engine","moodle-core-formchangechecker"]}}};
M.yui.loader = {modules: {}};

//]]>
</script>



<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-164264713-2"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'UA-164264713-2');
</script>

    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=0, minimal-ui">

    <link href="https://fonts.googleapis.com/css?family=Poppins" rel="stylesheet">
</head>

<body  id="page-mod-quiz-attempt" class="format-timelines  path-mod path-mod-quiz chrome dir-ltr lang-pt_br yui-skin-sam yui3-skin-sam ufprvirtual-ufpr-br pagelayout-incourse course-16981 context-1029309 cmid-475948 category-790 ">

<div id="page-wrapper">

    <div>
    <a class="sr-only sr-only-focusable" href="#maincontent">Ir para o conteúdo principal</a>
</div><script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/babel-polyfill/polyfill.min.js"></script>
<script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/polyfills/polyfill.js"></script>
<script src="https://ufprvirtual.ufpr.br/theme/yui_combo.php?rollup/3.17.2/yui-moodlesimple-min.js"></script><script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/javascript-static.js"></script>
<script>
//<![CDATA[
document.body.className += ' jsenabled';
//]]>
</script>



    
    <nav class="fixed-top navbar navbar-light navbar-expand moodle-has-zindex">
        
      
      <nav class="fixed-top"> 
          <div id="barra-brasil" style="background:#7F7F7F; height: 20px; padding:0 0 0 10px;display:block;">
              <ul id="menu-barra-temp" style="list-style:none;">
                  <li style="display:inline; float:left;padding-right:10px; margin-right:10px; border-right:1px solid #EDEDED">
                      <a href="http://brasil.gov.br" style="font-family:sans,sans-serif; text-decoration:none; color:white;">Portal do Governo Brasileiro</a>
                  </li>
              </ul>
          </div>
      </nav>        
            <div  data-region="drawer-toggle" class="navdraweropen-secundary">
                <button aria-expanded="false" aria-controls="nav-drawer" type="button" class="btn nav-link float-sm-left mr-1" data-action="toggle-drawer" data-side="left" data-preference="drawer-open-nav"><i class="slicon-menu"></i><span class="sr-only">Painel lateral</span></button>
            </div>
        
        <a href="https://ufprvirtual.ufpr.br" class="navbar-brand has-logo
                ">
                <span class="logo d-none d-sm-inline">
                    <img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/logo" alt="UFPR Virtual">
                </span>
                   
            
                <span class="site-name">UFPR Virtual</span> 
            
        </a>
    
        <ul class="navbar-nav d-none d-md-flex custom-menus">
            <!-- custom_menu -->
            
            <!-- page_heading_menu -->
            
        </ul>
        <ul class="nav navbar-nav ml-auto">
            <div class="d-none d-lg-block">
                
            </div>
             <li class="nav-item access-menu">
                 <span class="dropdown nav-item">
                     <a class="dropdown nav-link" href="javascript:(function(){d=document;jf=d.createElement('script');jf.src=('https:'==document.location.protocol?'https://ssl.atbar.org/c':'http://c.atbar.org')+'/ATBar2/ATBar.min.user.js';jf.type='text/javascript';jf.id='ToolBar';d.getElementsByTagName('head')[0].appendChild(jf);})();" title="Launch ATbar to adjust this webpage, have it read aloud and other functions.">
                         <img class="icon " alt="Acessibilidade" title="Acessibilidade" src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/acessibilidadeicon" />
                     </a>
                 </span>
             </li>   
            <li class="nav-item lang-menu">
                <div class="dropdown nav-item">
    <a class="dropdown-toggle nav-link" id="drop-down-6113064a538bc6113064a47f528" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false" href="#">
        <i class="slicon-globe"></i>
    </a>
    <div class="dropdown-menu" aria-labelledby="drop-down-6113064a538bc6113064a47f528">
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/attempt.php?attempt=443316&amp;cmid=475948&amp;lang=en" title="English ‎(en)‎">English ‎(en)‎</a>
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/attempt.php?attempt=443316&amp;cmid=475948&amp;lang=fr" title="Français ‎(fr)‎">Français ‎(fr)‎</a>
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/attempt.php?attempt=443316&amp;cmid=475948&amp;lang=pt_br" title="Português - Brasil ‎(pt_br)‎">Português - Brasil ‎(pt_br)‎</a>
    </div>
</div>
            </li>
    
            <div class="popover-region collapsed popover-region-notifications"
    id="nav-notification-popover-container" data-userid="12740"
    data-region="popover-region">
    <div class="popover-region-toggle nav-link"
        data-region="popover-region-toggle"
        role="button"
        aria-controls="popover-region-container-6113064a546796113064a47f529"
        aria-haspopup="true"
        aria-label="Mostrar janela de notificações sem as novas notificações"
        tabindex="0">
                <i class="slicon-bell" title="Alternar menu de notificações"></i>
        <div class="count-container hidden" data-region="count-container">0</div>

    </div>
    <div 
        id="popover-region-container-6113064a546796113064a47f529"
        class="popover-region-container"
        data-region="popover-region-container"
        aria-expanded="false"
        aria-hidden="true"
        aria-label="Janela de notificação"
        role="region">
        <div class="popover-region-header-container">
            <h3 class="popover-region-header-text" data-region="popover-region-header-text">Notificações</h3>
            <div class="popover-region-header-actions" data-region="popover-region-header-actions">        <div class="hover-tooltip-container">
                    <a class="mark-all-read-button"
                    href="#"
                    title="Marcar tudo como lido"
                    data-action="mark-all-read"
                    role="button">
                    <span class="normal-icon"><i class="slicon-check" alt="Marcar tudo como lido"></i></span>
                    <span class="loading-icon icon-no-margin"><i class="icon fa fa-circle-o-notch fa-spin fa-fw "  title="Carregando" aria-label="Carregando"></i></span>
                </a>

    <div class="hover-tooltip">
        Marcar tudo como lido
    </div>
</div>
        <div class="hover-tooltip-container">
                    <a href="https://ufprvirtual.ufpr.br/message/notificationpreferences.php?userid=12740"
                    title="Preferências de notificação">
                    <i class="slicon-settings" alt="Preferências de notificação"></i>
                </a>

    <div class="hover-tooltip">
        Preferências de notificação
    </div>
</div>
</div>
        </div>
        <div class="popover-region-content-container" data-region="popover-region-content-container">
            <div class="popover-region-content" data-region="popover-region-content">
                        <div class="all-notifications"
            data-region="all-notifications"
            role="log"
            aria-busy="false"
            aria-atomic="false"
            aria-relevant="additions"></div>
        <div class="empty-message" tabindex="0" data-region="empty-message">Você não tem nenhuma notificação</div>

            </div>
            <span class="loading-icon icon-no-margin"><i class="icon fa fa-circle-o-notch fa-spin fa-fw "  title="Carregando" aria-label="Carregando"></i></span>
        </div>
                <a class="see-all-link"
                    href="https://ufprvirtual.ufpr.br/message/output/popup/notifications.php">
                    <div class="popover-region-footer-container">
                        <div class="popover-region-seeall-text">Mostrar todos</div>
                    </div>
                </a>
    </div>
</div><div class="popover-region collapsed" data-region="popover-region-messages">
    <a id="message-drawer-toggle-6113064a55c866113064a47f5210" class="nav-link d-inline-block popover-region-toggle position-relative" href="#"
            role="button">
        <i class="icon fa slicon-bubble fa-fw "  title="Alternar menu de mensagens" aria-label="Alternar menu de mensagens"></i>
        <div class="count-container hidden" data-region="count-container"
        aria-label="Há 0 conversas não lidas">0</div>
    </a>
    <span class="sr-only sr-only-focusable" data-region="jumpto" tabindex="-1"></span></div>
    
            <li class="usermenu"><div class="action-menu moodle-actionmenu nowrap-items d-inline" id="action-menu-1" data-enhance="moodle-core-actionmenu">

        <div class="menubar d-flex " id="action-menu-1-menubar" role="menubar">

            


                <div class="action-menu-trigger">
                    <div class="dropdown">
                        <a href="#" tabindex="0" class="d-inline-block  dropdown-toggle icon-no-margin" id="action-menu-toggle-1" aria-label="" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false" aria-controls="action-menu-1-menu">
                            
                            <span class="userbutton"><span class="usertext"></span><span class="avatars"><span class="avatar current"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/u/f2" class="userpicture defaultuserpic" width="35" height="35" alt="" /></span></span></span>
                                
                            <b class="caret"></b>
                        </a>
                            <div class="dropdown-menu dropdown-menu-right menu  align-tr-br" id="action-menu-1-menu" data-rel="menu-content" aria-labelledby="action-menu-toggle-1" role="menu" data-align="tr-br">
                                                                <a href="https://ufprvirtual.ufpr.br/user/profile.php?id=12740" class="dropdown-item text-username menu-action" role="menuitem" aria-labelledby="actionmenuaction-1">
                                <i class="icon fa slicon-user fa-fw "  title="Lincoln Souza Linhares" aria-label="Lincoln Souza Linhares"></i>
                                <span class="menu-action-text" id="actionmenuaction-1">Lincoln Souza Linhares</span>
                        </a>
                    <div class="dropdown-divider" role="presentation"><span class="filler">&nbsp;</span></div>
                                                                <a href="https://ufprvirtual.ufpr.br/my/" class="dropdown-item menu-action" role="menuitem" data-title="mymoodle,admin" aria-labelledby="actionmenuaction-2">
                                <i class="icon fa slicon-speedometer fa-fw "  title="Painel" aria-label="Painel"></i>
                                <span class="menu-action-text" id="actionmenuaction-2">Painel</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/user/profile.php?id=12740" class="dropdown-item menu-action" role="menuitem" data-title="profile,moodle" aria-labelledby="actionmenuaction-3">
                                <i class="icon fa slicon-user fa-fw "  title="Perfil" aria-label="Perfil"></i>
                                <span class="menu-action-text" id="actionmenuaction-3">Perfil</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/grade/report/overview/index.php" class="dropdown-item menu-action" role="menuitem" data-title="grades,grades" aria-labelledby="actionmenuaction-4">
                                <i class="icon fa slicon-book-open fa-fw "  title="Notas" aria-label="Notas"></i>
                                <span class="menu-action-text" id="actionmenuaction-4">Notas</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/message/index.php" class="dropdown-item menu-action" role="menuitem" data-title="messages,message" aria-labelledby="actionmenuaction-5">
                                <i class="icon fa slicon-bubble fa-fw "  title="Mensagens" aria-label="Mensagens"></i>
                                <span class="menu-action-text" id="actionmenuaction-5">Mensagens</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/user/preferences.php" class="dropdown-item menu-action" role="menuitem" data-title="preferences,moodle" aria-labelledby="actionmenuaction-6">
                                <i class="icon fa slicon-wrench fa-fw "  title="Preferências" aria-label="Preferências"></i>
                                <span class="menu-action-text" id="actionmenuaction-6">Preferências</span>
                        </a>
                    <div class="dropdown-divider" role="presentation"><span class="filler">&nbsp;</span></div>
                                                                <a href="https://ufprvirtual.ufpr.br/login/logout.php?sesskey=XFg5yMJK6p" class="dropdown-item menu-action" role="menuitem" data-title="logout,moodle" aria-labelledby="actionmenuaction-7">
                                <i class="icon fa slicon-logout fa-fw "  title="Sair" aria-label="Sair"></i>
                                <span class="menu-action-text" id="actionmenuaction-7">Sair</span>
                        </a>
                            </div>
                    </div>
                </div>

        </div>

</div></li>
        </ul>
    </nav>

    <div id="page" class="container-fluid">
        <header id="page-header" class="row">
    <div class="col-12 pt-3 pb-3">
        <div class="card ">
            <div class="card-body ">
                <div class="d-flex align-items-center">
                    <div class="mr-auto">
                        <div class="page-context-header"><div class="page-header-headings"><h1>ERE3 - Cálculo2</h1></div></div>
                    </div>

                    <div class="header-actions-container flex-shrink-0" data-region="header-actions-container">
                    </div>
                </div>
                <div class="d-flex flex-wrap">
                    <div id="page-navbar">
                        <nav role="navigation">
    <ol class="breadcrumb">
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/my/" >Painel</a></li>
                <li class="breadcrumb-item"><span class="no-link">Minhas salas</span></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/course/view.php?id=16981" title="ERE3 - Cálculo2">ERE3_Calculo2_2021</a></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=7" >Segundas Chamadas e Exame Final</a></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/mod/quiz/view.php?id=475948" title="Questionário">Exame Final -demais cursos</a></li>
    </ol>
</nav>
                    </div>
                    <div class="ml-auto d-flex">
                        
                    </div>
                    <div id="course-header">
                        
                    </div>
                </div>
            </div>
        </div>
    </div>
</header>

        <div id="page-content" class="row">
            <div id="region-main-box" class="col-md-9 col-sm-12 col-12">
                <section id="region-main">
                    <div class="card">
                        <div class="card-body">
                            <span class="notifications" id="user-notifications"></span>
                            <div role="main"><span id="maincontent"></span><form action="https://ufprvirtual.ufpr.br/mod/quiz/processattempt.php?cmid=475948" method="post" enctype="multipart/form-data" accept-charset="utf-8" id="responseform"><div><div id="question-532045-5" class="que calculated deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">1</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:5_:flagged" value="0" /><input type="checkbox" id="q532045:5_:flaggedcheckbox" name="q532045:5_:flagged" value="1" /><input type="hidden" value="qaid=3117122&amp;qubaid=532045&amp;qid=38379614&amp;slot=5&amp;checksum=0104da2d52c11cc4af4a822c80fab27b&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:5_:flaggedlabel" for="q532045:5_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:5_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:5_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr">Calcule o volume do sólido,&nbsp;<span style="font-size: 0.9375rem;">no primeiro&nbsp;</span><span style="font-size: 0.9375rem;">octante, delimitado pelos cilindros <span class="nolink">\( x^{2}+y^{2}=16\)</span> e <span class="nolink">\( z^{2}+y^{2}=16\)</span>.</span></p></span></div><div class="ablock form-inline"><label for="q532045:5_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:5_answer" id="q532045:5_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-532045-6" class="que calculated deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">2</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:6_:flagged" value="0" /><input type="checkbox" id="q532045:6_:flaggedcheckbox" name="q532045:6_:flagged" value="1" /><input type="hidden" value="qaid=3117123&amp;qubaid=532045&amp;qid=38396135&amp;slot=6&amp;checksum=29999a391c75824b751493b8d2f568b5&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:6_:flaggedlabel" for="q532045:6_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:6_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:6_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr" style="text-align: left;">Calcule a integral tripla&nbsp; <span class="nolink">\( \int\int\int_T 4000\,(x^2-y^2)dV \)</span><span>&nbsp; sendo <span class="nolink">\( T \)</span> a região delimitada pelos&nbsp;</span><span>planos coordenados e pelo plano <span class="nolink">\( x+3y+z=1. \)</span></span><br></p></span></div><div class="ablock form-inline"><label for="q532045:6_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:6_answer" id="q532045:6_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-532045-3" class="que calculated deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">3</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:3_:flagged" value="0" /><input type="checkbox" id="q532045:3_:flaggedcheckbox" name="q532045:3_:flagged" value="1" /><input type="hidden" value="qaid=3117120&amp;qubaid=532045&amp;qid=38015573&amp;slot=3&amp;checksum=9c1c885f81f572f4cb5c70d02fb9e9cb&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:3_:flaggedlabel" for="q532045:3_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:3_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:3_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr" style="text-align: left;">Calcule a área delimitada pelas&nbsp; curvas polares <span class="nolink">\( r=4 + 4 \; cos(\theta) \)</span>&nbsp; e&nbsp; &nbsp;<span class="nolink">\( r=4- 4 \; cos(\theta) \)</span> (área interior comum às duas curvas).&nbsp;<br></p></span></div><div class="ablock form-inline"><label for="q532045:3_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:3_answer" id="q532045:3_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-532045-4" class="que calculated deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">4</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:4_:flagged" value="0" /><input type="checkbox" id="q532045:4_:flaggedcheckbox" name="q532045:4_:flagged" value="1" /><input type="hidden" value="qaid=3117121&amp;qubaid=532045&amp;qid=38024024&amp;slot=4&amp;checksum=f1cb393707bedda504b8297043f09acb&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:4_:flaggedlabel" for="q532045:4_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:4_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:4_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr"><span style="font-size: 14.9783px;">Um ponto se desloca&nbsp; ao longo&nbsp; da curva&nbsp;</span></p><p dir="ltr"><span style="font-size: 14.9783px;"><span class="nolink">\( {\bf r}(t)= 5 \; cos(t){\bf i} + 5 \; sen(t) {\bf j} + 2 t {\bf k}. \)</span></span></p><p dir="ltr"><span style="font-size: 14.9783px;">Determine&nbsp; o comprimento da trajetória&nbsp; percorrida pelo ponto entre <span class="nolink">\( t=0 \)</span>&nbsp; e <span class="nolink">\( t= 3 \pi \)</span>.</span></p></span></div><div class="ablock form-inline"><label for="q532045:4_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:4_answer" id="q532045:4_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-532045-9" class="que calculated deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">5</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:9_:flagged" value="0" /><input type="checkbox" id="q532045:9_:flaggedcheckbox" name="q532045:9_:flagged" value="1" /><input type="hidden" value="qaid=3117126&amp;qubaid=532045&amp;qid=38474603&amp;slot=9&amp;checksum=adccfa3bdb65fe483e820385b5863168&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:9_:flaggedlabel" for="q532045:9_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:9_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:9_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr">Encontre o intervalo onde <span class="nolink">\( b\)</span> deve pertencer para que a função&nbsp;<span style="font-size: 0.9375rem;"><span class="nolink">\( f(x,y)=x^2+b xy+y^2\)</span></span><span style="font-size: 0.9375rem;"><span class="nolink">\(-8x\)</span><span class="nolink">\(-8y\)</span><span class="nolink">\(+11 \)</span> tenha um ponto de mínimo tal que <span class="nolink">\(x+y&lt;\)</span><span class="nolink">\(16\)</span>.&nbsp;&nbsp;</span><span style="font-size: 0.9375rem;">Dê como resposta o extremo <strong>inferior</strong> desse intervalo.&nbsp;</span></p></span></div><div class="ablock form-inline"><label for="q532045:9_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:9_answer" id="q532045:9_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-532045-2" class="que multichoice deferredfeedback answersaved"><div class="info"><h3 class="no">Questão <span class="qno">6</span></h3><div class="state">Resposta salva</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:2_:flagged" value="0" /><input type="checkbox" id="q532045:2_:flaggedcheckbox" name="q532045:2_:flagged" value="1" /><input type="hidden" value="qaid=3117119&amp;qubaid=532045&amp;qid=38010106&amp;slot=2&amp;checksum=38618e6ccebcf1944d3a560503e5cc47&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:2_:flaggedlabel" for="q532045:2_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:2_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:2_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr" style="text-align: left;"><br></p><p dir="ltr" style="text-align: left;">Uma montanha tem a altura descrita por uma função <span class="nolink">\(f(x,y)\)</span>, e no ponto em que você está num certo momento, o gradiente da função <span class="nolink">\( f \)</span> é o vetor <span class="nolink">\( (3,-2 ) \)</span>.&nbsp; Nesse instante, tem um aviso de tempestade e é preciso descer a montanha. Dentre as direções cardinais, qual é a&nbsp; direção na qual a descida é mais rápida?&nbsp; (Nota: os pontos cardinais estão orientados da maneira padrão, Norte para cima e Leste para a direita).<br></p></span></div><div class="ablock"><div class="answer"><div class="r0"><input type="radio" name="q532045:2_answer" value="0" id="q532045:2_answer0" aria-labelledby="q532045:2_answer0_label" /><div class="d-flex w-100" id="q532045:2_answer0_label" data-region="answer-label"><div class="flex-fill ml-1"><p dir="ltr" style="text-align: left;">Leste <br></p></div></div> </div>
<div class="r1"><input type="radio" name="q532045:2_answer" value="1" id="q532045:2_answer1" aria-labelledby="q532045:2_answer1_label" checked="checked" /><div class="d-flex w-100" id="q532045:2_answer1_label" data-region="answer-label"><div class="flex-fill ml-1"><p dir="ltr" style="text-align: left;">Sul<br></p></div></div> </div>
<div class="r0"><input type="radio" name="q532045:2_answer" value="2" id="q532045:2_answer2" aria-labelledby="q532045:2_answer2_label" /><div class="d-flex w-100" id="q532045:2_answer2_label" data-region="answer-label"><div class="flex-fill ml-1"><p dir="ltr" style="text-align: left;">Norte<br></p></div></div> </div>
<div class="r1"><input type="radio" name="q532045:2_answer" value="3" id="q532045:2_answer3" aria-labelledby="q532045:2_answer3_label" /><div class="d-flex w-100" id="q532045:2_answer3_label" data-region="answer-label"><div class="flex-fill ml-1"><p dir="ltr" style="text-align: left;">Oeste<br></p></div></div> </div>
</div><div id="q532045:2_clearchoice" class="qtype_multichoice_clearchoice"><input type="radio" name="q532045:2_answer" id="q532045:2_answer-1" value="-1" class="sr-only" aria-hidden="true" /><label for="q532045:2_answer-1"><a tabindex="0" role="button" class="btn btn-link ml-3 mt-n1 mb-n1" href="#">Limpar minha escolha</a></label></div></div></div></div></div><div id="question-532045-1" class="que calculated deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">7</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:1_:flagged" value="0" /><input type="checkbox" id="q532045:1_:flaggedcheckbox" name="q532045:1_:flagged" value="1" /><input type="hidden" value="qaid=3117118&amp;qubaid=532045&amp;qid=38010095&amp;slot=1&amp;checksum=6874ce76315a0a83948ec35a2fbdb365&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:1_:flaggedlabel" for="q532045:1_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:1_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:1_:sequencecheck" value="1" /><div class="qtext"><p dir="ltr" style="text-align: left;"><br></p><p dir="ltr" style="text-align: left;"><br></p><p dir="ltr" style="text-align: left;">Uma caixa com fundo mas sem tampa esta <em><strong>encolhendo</strong></em>, uniformemente em todas as direções, a uma taxa de 4 cm/s. No momento em que a altura da caixa é de 4 cm, a largura é de 4 cm e o comprimento é de 3 cm, qual é a taxa de variação em cm/s da superfície total da caixa?<br></p></div><div class="ablock form-inline"><label for="q532045:1_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:1_answer" id="q532045:1_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-532045-10" class="que calculated deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">8</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:10_:flagged" value="0" /><input type="checkbox" id="q532045:10_:flaggedcheckbox" name="q532045:10_:flagged" value="1" /><input type="hidden" value="qaid=3117127&amp;qubaid=532045&amp;qid=38474602&amp;slot=10&amp;checksum=f63c849979f2f1b278eede1e6a8ae379&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:10_:flaggedlabel" for="q532045:10_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:10_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:10_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr" style="text-align: left;">Uma caixa fechada, de formato de paralelepípedo, com <span class="nolink">\(2304\)</span> metros cúbicos de volume deve ser construída usando três classes de materiais. O custo do material para as partes superiores e inferiores é de <span class="nolink">\(4\)</span> reais por metro quadrado; o custo do material usado&nbsp;na parte de frente e de trás é de <span class="nolink">\(6\)</span> reais por metro quadrado; e o custo para os outros lados é de <span class="nolink">\(96\)</span> reais o metro quadrado. Determine as dimensões da caixa de forma que o custo total em material seja mínimo.&nbsp; Forneça como resposta o valor do custo mínimo.&nbsp;<br></p></span></div><div class="ablock form-inline"><label for="q532045:10_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:10_answer" id="q532045:10_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div id="question-532045-8" class="que match deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">9</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:8_:flagged" value="0" /><input type="checkbox" id="q532045:8_:flaggedcheckbox" name="q532045:8_:flagged" value="1" /><input type="hidden" value="qaid=3117125&amp;qubaid=532045&amp;qid=38011632&amp;slot=8&amp;checksum=71cac4c14ac46627e0ae9069e0b8b3a7&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:8_:flaggedlabel" for="q532045:8_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:8_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:8_:sequencecheck" value="1" /><div class="qtext"><p>Para cada uma das afirmações abaixo marque Verdadeira ou Falsa<br></p></div><div class="ablock"><table class="answer"><tbody><tr class="r0"><td class="text"><span class="filter_mathjaxloader_equation"><ul>
    <li>Se <span class="nolink">\( \vec{F} = P \vec{i} + Q \vec{j} \)</span> é um campo no plano com <span class="nolink">\( 
\frac{\partial Q}{\partial x}(x,y) = \frac{\partial P}{\partial y} (x,y)
 \)</span> para todo <span class="nolink">\( (x,y) \)</span> no domínio de <span class="nolink">\(\vec{F}\)</span>, então <span class="nolink">\( \vec{F} 
\)</span> é conservativo.<br></li>
</ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub0">Resposta 1</label><select class="select custom-select custom-select ml-1" id="menuq532045:8_sub0" name="q532045:8_sub0"><option selected="selected" value="0">Escolher...</option><option value="1">Verdadeira</option><option value="2">Falsa</option></select> </td></tr><tr class="r1"><td class="text"><span class="filter_mathjaxloader_equation"><ul><li>Sendo <span class="nolink">\(E\)</span> um sólido no espaço e <span class="nolink">\(\vec{F} = (y,x,z)\)</span> vale 
<span class="nolink">\(\text{vol}(E) = \iint_S \vec{F} \)</span>, onde <span class="nolink">\(\text{vol}(E)\)</span>
 denota o volume de <span class="nolink">\(E\)</span> e <span class="nolink">\(S = \partial E\)</span> orientada positivamente.<br></li></ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub1">Resposta 2</label><select class="select custom-select custom-select ml-1" id="menuq532045:8_sub1" name="q532045:8_sub1"><option selected="selected" value="0">Escolher...</option><option value="1">Verdadeira</option><option value="2">Falsa</option></select> </td></tr><tr class="r0"><td class="text"><span class="filter_mathjaxloader_equation"><ul>
    <li><ul>
    </ul>Sendo <span class="nolink">\( \vec{F}(x,y) = 
\left( \frac{-y}{x^2 + y^2}, \frac{x}{x^2 + y^2} \right) \)</span> então <span class="nolink">\( \int_C \vec{F} \)</span> apenas depende dos pontos iniciais e finais de <span class="nolink">\(C\)</span>.<br><ul>
</ul><br></li>
</ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub2">Resposta 3</label><select class="select custom-select custom-select ml-1" id="menuq532045:8_sub2" name="q532045:8_sub2"><option selected="selected" value="0">Escolher...</option><option value="1">Verdadeira</option><option value="2">Falsa</option></select> </td></tr><tr class="r1"><td class="text"><span class="filter_mathjaxloader_equation"><ul>
    <li>Seja <span class="nolink">\(E\)</span> um sólido no espaço e <span class="nolink">\(S = \partial E\)</span> sua fronteira. Então <span class="nolink">\(\iint_S \vec{F} = 0\)</span> para todo campo constante <span class="nolink">\(\vec{F}\)</span> no espaço.<br></li>
</ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub3">Resposta 4</label><select class="select custom-select custom-select ml-1" id="menuq532045:8_sub3" name="q532045:8_sub3"><option selected="selected" value="0">Escolher...</option><option value="1">Verdadeira</option><option value="2">Falsa</option></select> </td></tr><tr class="r0"><td class="text"><span class="filter_mathjaxloader_equation"><ul>
    <li>Seja <span class="nolink">\(R\)</span> uma placa fina no plano com função densidade superficial <span class="nolink">\(\rho\)</span> e centro de massa <span class="nolink">\( (\bar{x},\bar{y}) \)</span>. Se <span class="nolink">\(\rho\)</span> é par com relação à <span class="nolink">\(x\)</span> e <span class="nolink">\(R\)</span> é simétrica com relação ao eixo <span class="nolink">\(x\)</span>, vale <span class="nolink">\(\bar{x} = 0\)</span>.<br></li>
</ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub4">Resposta 5</label><select class="select custom-select custom-select ml-1" id="menuq532045:8_sub4" name="q532045:8_sub4"><option selected="selected" value="0">Escolher...</option><option value="1">Verdadeira</option><option value="2">Falsa</option></select> </td></tr></tbody></table></div></div></div></div><div id="question-532045-7" class="que calculated deferredfeedback notyetanswered"><div class="info"><h3 class="no">Questão <span class="qno">10</span></h3><div class="state">Ainda não respondida</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:7_:flagged" value="0" /><input type="checkbox" id="q532045:7_:flaggedcheckbox" name="q532045:7_:flagged" value="1" /><input type="hidden" value="qaid=3117124&amp;qubaid=532045&amp;qid=38010849&amp;slot=7&amp;checksum=6954e97da1b0986f7ad156e814c8f8a7&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:7_:flaggedlabel" for="q532045:7_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:7_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:7_:sequencecheck" value="1" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p><span class="nolink">\(\DeclareMathOperator{\arctg}{arctg} \DeclareMathOperator{\sen}{sen}\)</span>Seja <span class="nolink">\(C\)</span> a curva no plano dada pelos segmentos de retas da figura abaixo, percorrida no sentido indicado. Seja também o 
campo de vetores</p><p><span class="nolink">\[ \vec{F}(x,y) = \left( x e^{\arctg(1 + x^{15})} - 6y; y^2 \cos(3 x^{31})&nbsp; \right) .\]</span></p><p>Se
 o valor da área do triângulo equilátero de vértices <span class="nolink">\(A,B,C\)</span> é igual à <span class="nolink">\(15\)</span> e <span class="nolink">\( \int_C 
\vec{F} = 18\)</span>, qual é o
 valor da área do triângulo equilátero de vértices <span class="nolink">\(P,Q,R\)</span>?</p><p><strong>(OBS.: escreva sua resposta com precisão de 2 casas decimais)</strong></p><p><strong><br></strong></p><p style="text-align: center;"><strong><img src="https://ufprvirtual.ufpr.br/pluginfile.php/644467/question/questiontext/532045/7/38010849/triangulo-encaixado2.png" alt="" role="presentation" class="img-responsive atto_image_button_text-bottom" width="719" height="500"></strong><br></p><p></p></span></div><div class="ablock form-inline"><label for="q532045:7_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:7_answer" id="q532045:7_answer" size="30" class="form-control d-inline" /></span></div></div></div></div><div class="submitbtns"><input type="submit" name="next" value="Finalizar tentativa ..." class="mod_quiz-next-nav btn btn-primary" /></div><input type="hidden" name="attempt" value="443316" /><input type="hidden" name="thispage" value="0" id="followingpage" /><input type="hidden" name="nextpage" value="-1" /><input type="hidden" name="timeup" value="0" id="timeup" /><input type="hidden" name="sesskey" value="XFg5yMJK6p" /><input type="hidden" name="scrollpos" value="" id="scrollpos" /><input type="hidden" name="slots" value="5,6,3,4,9,2,1,10,8,7" /></div></form><div id="connection-error" style="display: none;" role="alert"><p>Conexão de rede perdida. (Salvamento automático falhou). Anote quaisquer respostas registradas nesta página nos últimos minutos e tente conectar-se novamente. Quando a conexão for restabelecida, suas respostas devem ser salvas e esta mensagem irá desaparecer.</p>
</div><div id="connection-ok" style="display: none;" role="alert"><p>Conexão de rede restaurada. Você pode continuar com segurança.</p>
</div></div>
                            
                        </div>
                    </div>
                </section>
            </div>
                <div class="col-md-3 col-sm-12 col-12">
                    <aside id="block-region-side-pre" class="block-region" data-blockregion="side-pre" data-droptarget="1"><a href="#sb-1" class="sr-only sr-only-focusable">Pular &lt;span id=&quot;mod_quiz_navblock_title&quot;&gt;Navegação do questionário&lt;/span&gt;</a>

<section id="mod_quiz_navblock"
     class=" block block_fake  card mb-3"
     role="navigation"
     data-block="_fake"
          aria-labelledby="instance-fakeid-6113064a489dd-header"
     >

    <div class="card-body p-3">

            <h5 id="instance-fakeid-6113064a489dd-header" class="card-title d-inline"><span id="mod_quiz_navblock_title">Navegação do questionário</span></h5>


        <!DOCTYPE html>

<html  dir="ltr" lang="pt-br" xml:lang="pt-br">
<head>
    <title>Exame Final -demais cursos: Revisão da tentativa</title>
    <link rel="shortcut icon" href="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme/1627934520/favicon" />
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<meta name="keywords" content="moodle, Exame Final -demais cursos: Revisão da tentativa" />
<link rel="stylesheet" type="text/css" href="https://ufprvirtual.ufpr.br/theme/yui_combo.php?rollup/3.17.2/yui-moodlesimple-min.css" /><script id="firstthemesheet" type="text/css">/** Required in order to fix style inclusion problems in IE with YUI **/</script><link rel="stylesheet" type="text/css" href="https://ufprvirtual.ufpr.br/theme/styles.php/ufprvirtual/1627934520_1/all" />
<script>
//<![CDATA[
var M = {}; M.yui = {};
M.pageloadstarttime = new Date();
M.cfg = {"wwwroot":"https:\/\/ufprvirtual.ufpr.br","sesskey":"XFg5yMJK6p","sessiontimeout":"7200","themerev":"1627934520","slasharguments":1,"theme":"ufprvirtual","iconsystemmodule":"core\/icon_system_fontawesome","jsrev":"1627934520","admin":"admin","svgicons":true,"usertimezone":"Am\u00e9rica\/S\u00e3o_Paulo","contextid":1029309,"langrev":1628579167,"templaterev":"1627934520"};var yui1ConfigFn = function(me) {if(/-skin|reset|fonts|grids|base/.test(me.name)){me.type='css';me.path=me.path.replace(/\.js/,'.css');me.path=me.path.replace(/\/yui2-skin/,'/assets/skins/sam/yui2-skin')}};
var yui2ConfigFn = function(me) {var parts=me.name.replace(/^moodle-/,'').split('-'),component=parts.shift(),module=parts[0],min='-min';if(/-(skin|core)$/.test(me.name)){parts.pop();me.type='css';min=''}
if(module){var filename=parts.join('-');me.path=component+'/'+module+'/'+filename+min+'.'+me.type}else{me.path=component+'/'+component+'.'+me.type}};
YUI_config = {"debug":false,"base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/3.17.2\/","comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","combine":true,"filter":null,"insertBefore":"firstthemesheet","groups":{"yui2":{"base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/2in3\/2.9.0\/build\/","comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","combine":true,"ext":false,"root":"2in3\/2.9.0\/build\/","patterns":{"yui2-":{"group":"yui2","configFn":yui1ConfigFn}}},"moodle":{"name":"moodle","base":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?m\/1627934520\/","combine":true,"comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","ext":false,"root":"m\/1627934520\/","patterns":{"moodle-":{"group":"moodle","configFn":yui2ConfigFn}},"filter":null,"modules":{"moodle-core-notification":{"requires":["moodle-core-notification-dialogue","moodle-core-notification-alert","moodle-core-notification-confirm","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-core-notification-dialogue":{"requires":["base","node","panel","escape","event-key","dd-plugin","moodle-core-widget-focusafterclose","moodle-core-lockscroll"]},"moodle-core-notification-alert":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-confirm":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-exception":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-notification-ajaxexception":{"requires":["moodle-core-notification-dialogue"]},"moodle-core-blocks":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification"]},"moodle-core-chooserdialogue":{"requires":["base","panel","moodle-core-notification"]},"moodle-core-actionmenu":{"requires":["base","event","node-event-simulate"]},"moodle-core-lockscroll":{"requires":["plugin","base-build"]},"moodle-core-dragdrop":{"requires":["base","node","io","dom","dd","event-key","event-focus","moodle-core-notification"]},"moodle-core-formchangechecker":{"requires":["base","event-focus","moodle-core-event"]},"moodle-core-languninstallconfirm":{"requires":["base","node","moodle-core-notification-confirm","moodle-core-notification-alert"]},"moodle-core-handlebars":{"condition":{"trigger":"handlebars","when":"after"}},"moodle-core-popuphelp":{"requires":["moodle-core-tooltip"]},"moodle-core-tooltip":{"requires":["base","node","io-base","moodle-core-notification-dialogue","json-parse","widget-position","widget-position-align","event-outside","cache-base"]},"moodle-core-event":{"requires":["event-custom"]},"moodle-core-maintenancemodetimer":{"requires":["base","node"]},"moodle-core_availability-form":{"requires":["base","node","event","event-delegate","panel","moodle-core-notification-dialogue","json"]},"moodle-backup-backupselectall":{"requires":["node","event","node-event-simulate","anim"]},"moodle-backup-confirmcancel":{"requires":["node","node-event-simulate","moodle-core-notification-confirm"]},"moodle-course-formatchooser":{"requires":["base","node","node-event-simulate"]},"moodle-course-categoryexpander":{"requires":["node","event-key"]},"moodle-course-util":{"requires":["node"],"use":["moodle-course-util-base"],"submodules":{"moodle-course-util-base":{},"moodle-course-util-section":{"requires":["node","moodle-course-util-base"]},"moodle-course-util-cm":{"requires":["node","moodle-course-util-base"]}}},"moodle-course-management":{"requires":["base","node","io-base","moodle-core-notification-exception","json-parse","dd-constrain","dd-proxy","dd-drop","dd-delegate","node-event-delegate"]},"moodle-course-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-course-coursebase","moodle-course-util"]},"moodle-form-passwordunmask":{"requires":[]},"moodle-form-dateselector":{"requires":["base","node","overlay","calendar"]},"moodle-form-shortforms":{"requires":["node","base","selector-css3","moodle-core-event"]},"moodle-question-chooser":{"requires":["moodle-core-chooserdialogue"]},"moodle-question-preview":{"requires":["base","dom","event-delegate","event-key","core_question_engine"]},"moodle-question-searchform":{"requires":["base","node"]},"moodle-availability_completion-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_date-form":{"requires":["base","node","event","io","moodle-core_availability-form"]},"moodle-availability_grade-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_group-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_grouping-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-availability_profile-form":{"requires":["base","node","event","moodle-core_availability-form"]},"moodle-mod_assign-history":{"requires":["node","transition"]},"moodle-mod_attendance-groupfilter":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-recordings":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_bigbluebuttonbn-modform":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-imports":{"requires":["base","node"]},"moodle-mod_bigbluebuttonbn-broker":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_bigbluebuttonbn-rooms":{"requires":["base","node","datasource-get","datasource-jsonschema","datasource-polling","moodle-core-notification"]},"moodle-mod_chamado-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-mod_chamado-chamadobase","moodle-mod_chamado-util-base","moodle-mod_chamado-util-page","moodle-mod_chamado-util-slot","moodle-course-util"]},"moodle-mod_chamado-chamadobase":{"requires":["base","node"]},"moodle-mod_chamado-autosave":{"requires":["base","node","event","event-valuechange","node-event-delegate","io-form"]},"moodle-mod_chamado-util":{"requires":["node","moodle-core-actionmenu"],"use":["moodle-mod_chamado-util-base"],"submodules":{"moodle-mod_chamado-util-base":{},"moodle-mod_chamado-util-slot":{"requires":["node","moodle-mod_chamado-util-base"]},"moodle-mod_chamado-util-page":{"requires":["node","moodle-mod_chamado-util-base"]}}},"moodle-mod_chamado-toolboxes":{"requires":["base","node","event","event-key","io","moodle-mod_chamado-chamadobase","moodle-mod_chamado-util-slot","moodle-core-notification-ajaxexception"]},"moodle-mod_chamado-modform":{"requires":["base","node","event"]},"moodle-mod_chamado-questionchooser":{"requires":["moodle-core-chooserdialogue","moodle-mod_chamado-util","querystring-parse"]},"moodle-mod_mediagallery-base":{"requires":["base","node","selector-css3","dd-constrain","dd-proxy","dd-drop","dd-plugin","moodle-core-notification","event"]},"moodle-mod_mediagallery-mediabox":{"requires":["base","node","selector-css3"]},"moodle-mod_quiz-questionchooser":{"requires":["moodle-core-chooserdialogue","moodle-mod_quiz-util","querystring-parse"]},"moodle-mod_quiz-modform":{"requires":["base","node","event"]},"moodle-mod_quiz-util":{"requires":["node","moodle-core-actionmenu"],"use":["moodle-mod_quiz-util-base"],"submodules":{"moodle-mod_quiz-util-base":{},"moodle-mod_quiz-util-slot":{"requires":["node","moodle-mod_quiz-util-base"]},"moodle-mod_quiz-util-page":{"requires":["node","moodle-mod_quiz-util-base"]}}},"moodle-mod_quiz-quizbase":{"requires":["base","node"]},"moodle-mod_quiz-dragdrop":{"requires":["base","node","io","dom","dd","dd-scroll","moodle-core-dragdrop","moodle-core-notification","moodle-mod_quiz-quizbase","moodle-mod_quiz-util-base","moodle-mod_quiz-util-page","moodle-mod_quiz-util-slot","moodle-course-util"]},"moodle-mod_quiz-toolboxes":{"requires":["base","node","event","event-key","io","moodle-mod_quiz-quizbase","moodle-mod_quiz-util-slot","moodle-core-notification-ajaxexception"]},"moodle-mod_quiz-autosave":{"requires":["base","node","event","event-valuechange","node-event-delegate","io-form"]},"moodle-message_airnotifier-toolboxes":{"requires":["base","node","io"]},"moodle-filter_glossary-autolinker":{"requires":["base","node","io-base","json-parse","event-delegate","overlay","moodle-core-event","moodle-core-notification-alert","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-filter_mathjaxloader-loader":{"requires":["moodle-core-event"]},"moodle-editor_atto-editor":{"requires":["node","transition","io","overlay","escape","event","event-simulate","event-custom","node-event-html5","node-event-simulate","yui-throttle","moodle-core-notification-dialogue","moodle-core-notification-confirm","moodle-editor_atto-rangy","handlebars","timers","querystring-stringify"]},"moodle-editor_atto-plugin":{"requires":["node","base","escape","event","event-outside","handlebars","event-custom","timers","moodle-editor_atto-menu"]},"moodle-editor_atto-menu":{"requires":["moodle-core-notification-dialogue","node","event","event-custom"]},"moodle-editor_atto-rangy":{"requires":[]},"moodle-report_eventlist-eventfilter":{"requires":["base","event","node","node-event-delegate","datatable","autocomplete","autocomplete-filters"]},"moodle-report_loglive-fetchlogs":{"requires":["base","event","node","io","node-event-delegate"]},"moodle-gradereport_grader-gradereporttable":{"requires":["base","node","event","handlebars","overlay","event-hover"]},"moodle-gradereport_history-userselector":{"requires":["escape","event-delegate","event-key","handlebars","io-base","json-parse","moodle-core-notification-dialogue"]},"moodle-tool_capability-search":{"requires":["base","node"]},"moodle-tool_lp-dragdrop-reorder":{"requires":["moodle-core-dragdrop"]},"moodle-tool_monitor-dropdown":{"requires":["base","event","node"]},"moodle-assignfeedback_editpdf-editor":{"requires":["base","event","node","io","graphics","json","event-move","event-resize","transition","querystring-stringify-simple","moodle-core-notification-dialog","moodle-core-notification-alert","moodle-core-notification-warning","moodle-core-notification-exception","moodle-core-notification-ajaxexception"]},"moodle-atto_accessibilitychecker-button":{"requires":["color-base","moodle-editor_atto-plugin"]},"moodle-atto_accessibilityhelper-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_align-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_bold-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_charmap-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_clear-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_collapse-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_emojipicker-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_emoticon-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_equation-button":{"requires":["moodle-editor_atto-plugin","moodle-core-event","io","event-valuechange","tabview","array-extras"]},"moodle-atto_h5p-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_html-button":{"requires":["promise","moodle-editor_atto-plugin","moodle-atto_html-beautify","moodle-atto_html-codemirror","event-valuechange"]},"moodle-atto_html-beautify":{},"moodle-atto_html-codemirror":{"requires":["moodle-atto_html-codemirror-skin"]},"moodle-atto_image-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_indent-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_italic-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_link-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_managefiles-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_managefiles-usedfiles":{"requires":["node","escape"]},"moodle-atto_media-button":{"requires":["moodle-editor_atto-plugin","moodle-form-shortforms"]},"moodle-atto_noautolink-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_orderedlist-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_recordrtc-button":{"requires":["moodle-editor_atto-plugin","moodle-atto_recordrtc-recording"]},"moodle-atto_recordrtc-recording":{"requires":["moodle-atto_recordrtc-button"]},"moodle-atto_rtl-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_strike-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_styles-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_subscript-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_superscript-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_table-button":{"requires":["moodle-editor_atto-plugin","moodle-editor_atto-menu","event","event-valuechange"]},"moodle-atto_teamsmeeting-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_title-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_underline-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_undo-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_unorderedlist-button":{"requires":["moodle-editor_atto-plugin"]},"moodle-atto_wiris-button":{"requires":["moodle-editor_atto-plugin","get"]}}},"gallery":{"name":"gallery","base":"https:\/\/ufprvirtual.ufpr.br\/lib\/yuilib\/gallery\/","combine":true,"comboBase":"https:\/\/ufprvirtual.ufpr.br\/theme\/yui_combo.php?","ext":false,"root":"gallery\/1627934520\/","patterns":{"gallery-":{"group":"gallery"}}}},"modules":{"core_filepicker":{"name":"core_filepicker","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/repository\/filepicker.js","requires":["base","node","node-event-simulate","json","async-queue","io-base","io-upload-iframe","io-form","yui2-treeview","panel","cookie","datatable","datatable-sort","resize-plugin","dd-plugin","escape","moodle-core_filepicker","moodle-core-notification-dialogue"]},"core_comment":{"name":"core_comment","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/comment\/comment.js","requires":["base","io-base","node","json","yui2-animation","overlay","escape"]},"mathjax":{"name":"mathjax","fullpath":"https:\/\/cdn.jsdelivr.net\/npm\/mathjax@2.7.8\/MathJax.js?delayStartupUntil=configured"},"core_question_flags":{"name":"core_question_flags","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/question\/flags.js","requires":["base","dom","event-delegate","io-base"]},"core_question_engine":{"name":"core_question_engine","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/question\/qengine.js","requires":["node","event"]},"mod_quiz":{"name":"mod_quiz","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/mod\/quiz\/module.js","requires":["base","dom","event-delegate","event-key","core_question_engine","moodle-core-formchangechecker"]}}};
M.yui.loader = {modules: {}};

//]]>
</script>



<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-164264713-2"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'UA-164264713-2');
</script>

    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=0, minimal-ui">

    <link href="https://fonts.googleapis.com/css?family=Poppins" rel="stylesheet">
</head>

<body  id="page-mod-quiz-review" class="format-timelines  path-mod path-mod-quiz chrome dir-ltr lang-pt_br yui-skin-sam yui3-skin-sam ufprvirtual-ufpr-br pagelayout-incourse course-16981 context-1029309 cmid-475948 category-790 ">

<div id="page-wrapper">

    <div>
    <a class="sr-only sr-only-focusable" href="#maincontent">Ir para o conteúdo principal</a>
</div><script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/babel-polyfill/polyfill.min.js"></script>
<script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/polyfills/polyfill.js"></script>
<script src="https://ufprvirtual.ufpr.br/theme/yui_combo.php?rollup/3.17.2/yui-moodlesimple-min.js"></script><script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/javascript-static.js"></script>
<script>
//<![CDATA[
document.body.className += ' jsenabled';
//]]>
</script>



    
    <nav class="fixed-top navbar navbar-light navbar-expand moodle-has-zindex">
        
      
      <nav class="fixed-top"> 
          <div id="barra-brasil" style="background:#7F7F7F; height: 20px; padding:0 0 0 10px;display:block;">
              <ul id="menu-barra-temp" style="list-style:none;">
                  <li style="display:inline; float:left;padding-right:10px; margin-right:10px; border-right:1px solid #EDEDED">
                      <a href="http://brasil.gov.br" style="font-family:sans,sans-serif; text-decoration:none; color:white;">Portal do Governo Brasileiro</a>
                  </li>
              </ul>
          </div>
      </nav>        
            <div  data-region="drawer-toggle" class="navdraweropen-secundary">
                <button aria-expanded="false" aria-controls="nav-drawer" type="button" class="btn nav-link float-sm-left mr-1" data-action="toggle-drawer" data-side="left" data-preference="drawer-open-nav"><i class="slicon-menu"></i><span class="sr-only">Painel lateral</span></button>
            </div>
        
        <a href="https://ufprvirtual.ufpr.br" class="navbar-brand has-logo
                ">
                <span class="logo d-none d-sm-inline">
                    <img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/logo" alt="UFPR Virtual">
                </span>
                   
            
                <span class="site-name">UFPR Virtual</span> 
            
        </a>
    
        <ul class="navbar-nav d-none d-md-flex custom-menus">
            <!-- custom_menu -->
            
            <!-- page_heading_menu -->
            
        </ul>
        <ul class="nav navbar-nav ml-auto">
            <div class="d-none d-lg-block">
                
            </div>
             <li class="nav-item access-menu">
                 <span class="dropdown nav-item">
                     <a class="dropdown nav-link" href="javascript:(function(){d=document;jf=d.createElement('script');jf.src=('https:'==document.location.protocol?'https://ssl.atbar.org/c':'http://c.atbar.org')+'/ATBar2/ATBar.min.user.js';jf.type='text/javascript';jf.id='ToolBar';d.getElementsByTagName('head')[0].appendChild(jf);})();" title="Launch ATbar to adjust this webpage, have it read aloud and other functions.">
                         <img class="icon " alt="Acessibilidade" title="Acessibilidade" src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/acessibilidadeicon" />
                     </a>
                 </span>
             </li>   
            <li class="nav-item lang-menu">
                <div class="dropdown nav-item">
    <a class="dropdown-toggle nav-link" id="drop-down-61133325a71b5611333259bed36" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false" href="#">
        <i class="slicon-globe"></i>
    </a>
    <div class="dropdown-menu" aria-labelledby="drop-down-61133325a71b5611333259bed36">
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/review.php?attempt=443316&amp;cmid=475948&amp;lang=en" title="English ‎(en)‎">English ‎(en)‎</a>
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/review.php?attempt=443316&amp;cmid=475948&amp;lang=fr" title="Français ‎(fr)‎">Français ‎(fr)‎</a>
                <a class="dropdown-item" href="https://ufprvirtual.ufpr.br/mod/quiz/review.php?attempt=443316&amp;cmid=475948&amp;lang=pt_br" title="Português - Brasil ‎(pt_br)‎">Português - Brasil ‎(pt_br)‎</a>
    </div>
</div>
            </li>
    
            <div class="popover-region collapsed popover-region-notifications"
    id="nav-notification-popover-container" data-userid="12740"
    data-region="popover-region">
    <div class="popover-region-toggle nav-link"
        data-region="popover-region-toggle"
        role="button"
        aria-controls="popover-region-container-61133325a7eee611333259bed37"
        aria-haspopup="true"
        aria-label="Mostrar janela de notificações sem as novas notificações"
        tabindex="0">
                <i class="slicon-bell" title="Alternar menu de notificações"></i>
        <div class="count-container hidden" data-region="count-container">0</div>

    </div>
    <div 
        id="popover-region-container-61133325a7eee611333259bed37"
        class="popover-region-container"
        data-region="popover-region-container"
        aria-expanded="false"
        aria-hidden="true"
        aria-label="Janela de notificação"
        role="region">
        <div class="popover-region-header-container">
            <h3 class="popover-region-header-text" data-region="popover-region-header-text">Notificações</h3>
            <div class="popover-region-header-actions" data-region="popover-region-header-actions">        <div class="hover-tooltip-container">
                    <a class="mark-all-read-button"
                    href="#"
                    title="Marcar tudo como lido"
                    data-action="mark-all-read"
                    role="button">
                    <span class="normal-icon"><i class="slicon-check" alt="Marcar tudo como lido"></i></span>
                    <span class="loading-icon icon-no-margin"><i class="icon fa fa-circle-o-notch fa-spin fa-fw "  title="Carregando" aria-label="Carregando"></i></span>
                </a>

    <div class="hover-tooltip">
        Marcar tudo como lido
    </div>
</div>
        <div class="hover-tooltip-container">
                    <a href="https://ufprvirtual.ufpr.br/message/notificationpreferences.php?userid=12740"
                    title="Preferências de notificação">
                    <i class="slicon-settings" alt="Preferências de notificação"></i>
                </a>

    <div class="hover-tooltip">
        Preferências de notificação
    </div>
</div>
</div>
        </div>
        <div class="popover-region-content-container" data-region="popover-region-content-container">
            <div class="popover-region-content" data-region="popover-region-content">
                        <div class="all-notifications"
            data-region="all-notifications"
            role="log"
            aria-busy="false"
            aria-atomic="false"
            aria-relevant="additions"></div>
        <div class="empty-message" tabindex="0" data-region="empty-message">Você não tem nenhuma notificação</div>

            </div>
            <span class="loading-icon icon-no-margin"><i class="icon fa fa-circle-o-notch fa-spin fa-fw "  title="Carregando" aria-label="Carregando"></i></span>
        </div>
                <a class="see-all-link"
                    href="https://ufprvirtual.ufpr.br/message/output/popup/notifications.php">
                    <div class="popover-region-footer-container">
                        <div class="popover-region-seeall-text">Mostrar todos</div>
                    </div>
                </a>
    </div>
</div><div class="popover-region collapsed" data-region="popover-region-messages">
    <a id="message-drawer-toggle-61133325a92c2611333259bed38" class="nav-link d-inline-block popover-region-toggle position-relative" href="#"
            role="button">
        <i class="icon fa slicon-bubble fa-fw "  title="Alternar menu de mensagens" aria-label="Alternar menu de mensagens"></i>
        <div class="count-container hidden" data-region="count-container"
        aria-label="Há 0 conversas não lidas">0</div>
    </a>
    <span class="sr-only sr-only-focusable" data-region="jumpto" tabindex="-1"></span></div>
    
            <li class="usermenu"><div class="action-menu moodle-actionmenu nowrap-items d-inline" id="action-menu-1" data-enhance="moodle-core-actionmenu">

        <div class="menubar d-flex " id="action-menu-1-menubar" role="menubar">

            


                <div class="action-menu-trigger">
                    <div class="dropdown">
                        <a href="#" tabindex="0" class="d-inline-block  dropdown-toggle icon-no-margin" id="action-menu-toggle-1" aria-label="" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false" aria-controls="action-menu-1-menu">
                            
                            <span class="userbutton"><span class="usertext"></span><span class="avatars"><span class="avatar current"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/u/f2" class="userpicture defaultuserpic" width="35" height="35" alt="" /></span></span></span>
                                
                            <b class="caret"></b>
                        </a>
                            <div class="dropdown-menu dropdown-menu-right menu  align-tr-br" id="action-menu-1-menu" data-rel="menu-content" aria-labelledby="action-menu-toggle-1" role="menu" data-align="tr-br">
                                                                <a href="https://ufprvirtual.ufpr.br/user/profile.php?id=12740" class="dropdown-item text-username menu-action" role="menuitem" aria-labelledby="actionmenuaction-1">
                                <i class="icon fa slicon-user fa-fw "  title="Lincoln Souza Linhares" aria-label="Lincoln Souza Linhares"></i>
                                <span class="menu-action-text" id="actionmenuaction-1">Lincoln Souza Linhares</span>
                        </a>
                    <div class="dropdown-divider" role="presentation"><span class="filler">&nbsp;</span></div>
                                                                <a href="https://ufprvirtual.ufpr.br/my/" class="dropdown-item menu-action" role="menuitem" data-title="mymoodle,admin" aria-labelledby="actionmenuaction-2">
                                <i class="icon fa slicon-speedometer fa-fw "  title="Painel" aria-label="Painel"></i>
                                <span class="menu-action-text" id="actionmenuaction-2">Painel</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/user/profile.php?id=12740" class="dropdown-item menu-action" role="menuitem" data-title="profile,moodle" aria-labelledby="actionmenuaction-3">
                                <i class="icon fa slicon-user fa-fw "  title="Perfil" aria-label="Perfil"></i>
                                <span class="menu-action-text" id="actionmenuaction-3">Perfil</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/grade/report/overview/index.php" class="dropdown-item menu-action" role="menuitem" data-title="grades,grades" aria-labelledby="actionmenuaction-4">
                                <i class="icon fa slicon-book-open fa-fw "  title="Notas" aria-label="Notas"></i>
                                <span class="menu-action-text" id="actionmenuaction-4">Notas</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/message/index.php" class="dropdown-item menu-action" role="menuitem" data-title="messages,message" aria-labelledby="actionmenuaction-5">
                                <i class="icon fa slicon-bubble fa-fw "  title="Mensagens" aria-label="Mensagens"></i>
                                <span class="menu-action-text" id="actionmenuaction-5">Mensagens</span>
                        </a>
                                                                <a href="https://ufprvirtual.ufpr.br/user/preferences.php" class="dropdown-item menu-action" role="menuitem" data-title="preferences,moodle" aria-labelledby="actionmenuaction-6">
                                <i class="icon fa slicon-wrench fa-fw "  title="Preferências" aria-label="Preferências"></i>
                                <span class="menu-action-text" id="actionmenuaction-6">Preferências</span>
                        </a>
                    <div class="dropdown-divider" role="presentation"><span class="filler">&nbsp;</span></div>
                                                                <a href="https://ufprvirtual.ufpr.br/login/logout.php?sesskey=XFg5yMJK6p" class="dropdown-item menu-action" role="menuitem" data-title="logout,moodle" aria-labelledby="actionmenuaction-7">
                                <i class="icon fa slicon-logout fa-fw "  title="Sair" aria-label="Sair"></i>
                                <span class="menu-action-text" id="actionmenuaction-7">Sair</span>
                        </a>
                            </div>
                    </div>
                </div>

        </div>

</div></li>
        </ul>
    </nav>

    <div id="page" class="container-fluid">
        <header id="page-header" class="row">
    <div class="col-12 pt-3 pb-3">
        <div class="card ">
            <div class="card-body ">
                <div class="d-flex align-items-center">
                    <div class="mr-auto">
                        <div class="page-context-header"><div class="page-header-headings"><h1>ERE3 - Cálculo2</h1></div></div>
                    </div>

                    <div class="header-actions-container flex-shrink-0" data-region="header-actions-container">
                    </div>
                </div>
                <div class="d-flex flex-wrap">
                    <div id="page-navbar">
                        <nav role="navigation">
    <ol class="breadcrumb">
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/my/" >Painel</a></li>
                <li class="breadcrumb-item"><span class="no-link">Minhas salas</span></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/course/view.php?id=16981" title="ERE3 - Cálculo2">ERE3_Calculo2_2021</a></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=7" >Segundas Chamadas e Exame Final</a></li>
                <li class="breadcrumb-item"><a href="https://ufprvirtual.ufpr.br/mod/quiz/view.php?id=475948" title="Questionário">Exame Final -demais cursos</a></li>
    </ol>
</nav>
                    </div>
                    <div class="ml-auto d-flex">
                        
                    </div>
                    <div id="course-header">
                        
                    </div>
                </div>
            </div>
        </div>
    </div>
</header>

        <div id="page-content" class="row">
            <div id="region-main-box" class="col-md-9 col-sm-12 col-12">
                <section id="region-main">
                    <div class="card">
                        <div class="card-body">
                            <span class="notifications" id="user-notifications"></span>
                            <div role="main"><span id="maincontent"></span><table class="generaltable generalbox quizreviewsummary"><tbody><tr><th class="cell" scope="row">Iniciado em</th><td class="cell">Tuesday, 10 Aug 2021, 20:03</td></tr><tr><th class="cell" scope="row">Estado</th><td class="cell">Finalizada</td></tr><tr><th class="cell" scope="row">Concluída em</th><td class="cell">Tuesday, 10 Aug 2021, 23:14</td></tr><tr><th class="cell" scope="row">Tempo empregado</th><td class="cell">3 horas 11 minutos</td></tr></tbody></table><form action="https://ufprvirtual.ufpr.br/mod/quiz/review.php?attempt=443316&amp;cmid=475948" method="post" class="questionflagsaveform"><div><div id="question-532045-5" class="que calculated deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">1</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:5_:flagged" value="0" /><input type="checkbox" id="q532045:5_:flaggedcheckbox" name="q532045:5_:flagged" value="1" /><input type="hidden" value="qaid=3117122&amp;qubaid=532045&amp;qid=38379614&amp;slot=5&amp;checksum=0104da2d52c11cc4af4a822c80fab27b&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:5_:flaggedlabel" for="q532045:5_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:5_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:5_:sequencecheck" value="3" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr">Calcule o volume do sólido,&nbsp;<span style="font-size: 0.9375rem;">no primeiro&nbsp;</span><span style="font-size: 0.9375rem;">octante, delimitado pelos cilindros <span class="nolink">\( x^{2}+y^{2}=16\)</span> e <span class="nolink">\( z^{2}+y^{2}=16\)</span>.</span></p></span></div><div class="ablock form-inline"><label for="q532045:5_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:5_answer" value="42,667" id="q532045:5_answer" size="30" class="form-control d-inline" readonly="readonly" /></span></div></div></div></div><div id="question-532045-6" class="que calculated deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">2</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:6_:flagged" value="0" /><input type="checkbox" id="q532045:6_:flaggedcheckbox" name="q532045:6_:flagged" value="1" /><input type="hidden" value="qaid=3117123&amp;qubaid=532045&amp;qid=38396135&amp;slot=6&amp;checksum=29999a391c75824b751493b8d2f568b5&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:6_:flaggedlabel" for="q532045:6_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:6_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:6_:sequencecheck" value="3" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr" style="text-align: left;">Calcule a integral tripla&nbsp; <span class="nolink">\( \int\int\int_T 4000\,(x^2-y^2)dV \)</span><span>&nbsp; sendo <span class="nolink">\( T \)</span> a região delimitada pelos&nbsp;</span><span>planos coordenados e pelo plano <span class="nolink">\( x+3y+z=1. \)</span></span><br></p></span></div><div class="ablock form-inline"><label for="q532045:6_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:6_answer" value="19,753" id="q532045:6_answer" size="30" class="form-control d-inline" readonly="readonly" /></span></div></div></div></div><div id="question-532045-3" class="que calculated deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">3</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:3_:flagged" value="0" /><input type="checkbox" id="q532045:3_:flaggedcheckbox" name="q532045:3_:flagged" value="1" /><input type="hidden" value="qaid=3117120&amp;qubaid=532045&amp;qid=38015573&amp;slot=3&amp;checksum=9c1c885f81f572f4cb5c70d02fb9e9cb&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:3_:flaggedlabel" for="q532045:3_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:3_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:3_:sequencecheck" value="3" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr" style="text-align: left;">Calcule a área delimitada pelas&nbsp; curvas polares <span class="nolink">\( r=4 + 4 \; cos(\theta) \)</span>&nbsp; e&nbsp; &nbsp;<span class="nolink">\( r=4- 4 \; cos(\theta) \)</span> (área interior comum às duas curvas).&nbsp;<br></p></span></div><div class="ablock form-inline"><label for="q532045:3_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:3_answer" value="11,398" id="q532045:3_answer" size="30" class="form-control d-inline" readonly="readonly" /></span></div></div></div></div><div id="question-532045-4" class="que calculated deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">4</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:4_:flagged" value="0" /><input type="checkbox" id="q532045:4_:flaggedcheckbox" name="q532045:4_:flagged" value="1" /><input type="hidden" value="qaid=3117121&amp;qubaid=532045&amp;qid=38024024&amp;slot=4&amp;checksum=f1cb393707bedda504b8297043f09acb&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:4_:flaggedlabel" for="q532045:4_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:4_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:4_:sequencecheck" value="3" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr"><span style="font-size: 14.9783px;">Um ponto se desloca&nbsp; ao longo&nbsp; da curva&nbsp;</span></p><p dir="ltr"><span style="font-size: 14.9783px;"><span class="nolink">\( {\bf r}(t)= 5 \; cos(t){\bf i} + 5 \; sen(t) {\bf j} + 2 t {\bf k}. \)</span></span></p><p dir="ltr"><span style="font-size: 14.9783px;">Determine&nbsp; o comprimento da trajetória&nbsp; percorrida pelo ponto entre <span class="nolink">\( t=0 \)</span>&nbsp; e <span class="nolink">\( t= 3 \pi \)</span>.</span></p></span></div><div class="ablock form-inline"><label for="q532045:4_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:4_answer" value="50,754" id="q532045:4_answer" size="30" class="form-control d-inline" readonly="readonly" /></span></div></div></div></div><div id="question-532045-9" class="que calculated deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">5</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:9_:flagged" value="0" /><input type="checkbox" id="q532045:9_:flaggedcheckbox" name="q532045:9_:flagged" value="1" /><input type="hidden" value="qaid=3117126&amp;qubaid=532045&amp;qid=38474603&amp;slot=9&amp;checksum=adccfa3bdb65fe483e820385b5863168&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:9_:flaggedlabel" for="q532045:9_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:9_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:9_:sequencecheck" value="3" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr">Encontre o intervalo onde <span class="nolink">\( b\)</span> deve pertencer para que a função&nbsp;<span style="font-size: 0.9375rem;"><span class="nolink">\( f(x,y)=x^2+b xy+y^2\)</span></span><span style="font-size: 0.9375rem;"><span class="nolink">\(-8x\)</span><span class="nolink">\(-8y\)</span><span class="nolink">\(+11 \)</span> tenha um ponto de mínimo tal que <span class="nolink">\(x+y&lt;\)</span><span class="nolink">\(16\)</span>.&nbsp;&nbsp;</span><span style="font-size: 0.9375rem;">Dê como resposta o extremo <strong>inferior</strong> desse intervalo.&nbsp;</span></p></span></div><div class="ablock form-inline"><label for="q532045:9_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:9_answer" value="-2" id="q532045:9_answer" size="30" class="form-control d-inline" readonly="readonly" /></span></div></div></div></div><div id="question-532045-2" class="que multichoice deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">6</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:2_:flagged" value="0" /><input type="checkbox" id="q532045:2_:flaggedcheckbox" name="q532045:2_:flagged" value="1" /><input type="hidden" value="qaid=3117119&amp;qubaid=532045&amp;qid=38010106&amp;slot=2&amp;checksum=38618e6ccebcf1944d3a560503e5cc47&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:2_:flaggedlabel" for="q532045:2_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:2_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:2_:sequencecheck" value="3" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr" style="text-align: left;"><br></p><p dir="ltr" style="text-align: left;">Uma montanha tem a altura descrita por uma função <span class="nolink">\(f(x,y)\)</span>, e no ponto em que você está num certo momento, o gradiente da função <span class="nolink">\( f \)</span> é o vetor <span class="nolink">\( (3,-2 ) \)</span>.&nbsp; Nesse instante, tem um aviso de tempestade e é preciso descer a montanha. Dentre as direções cardinais, qual é a&nbsp; direção na qual a descida é mais rápida?&nbsp; (Nota: os pontos cardinais estão orientados da maneira padrão, Norte para cima e Leste para a direita).<br></p></span></div><div class="ablock"><div class="answer"><div class="r0"><input type="radio" name="q532045:2_answer" disabled="disabled" value="0" id="q532045:2_answer0" aria-labelledby="q532045:2_answer0_label" checked="checked" /><div class="d-flex w-100" id="q532045:2_answer0_label" data-region="answer-label"><div class="flex-fill ml-1"><p dir="ltr" style="text-align: left;">Leste <br></p></div></div> </div>
<div class="r1"><input type="radio" name="q532045:2_answer" disabled="disabled" value="1" id="q532045:2_answer1" aria-labelledby="q532045:2_answer1_label" /><div class="d-flex w-100" id="q532045:2_answer1_label" data-region="answer-label"><div class="flex-fill ml-1"><p dir="ltr" style="text-align: left;">Sul<br></p></div></div> </div>
<div class="r0"><input type="radio" name="q532045:2_answer" disabled="disabled" value="2" id="q532045:2_answer2" aria-labelledby="q532045:2_answer2_label" /><div class="d-flex w-100" id="q532045:2_answer2_label" data-region="answer-label"><div class="flex-fill ml-1"><p dir="ltr" style="text-align: left;">Norte<br></p></div></div> </div>
<div class="r1"><input type="radio" name="q532045:2_answer" disabled="disabled" value="3" id="q532045:2_answer3" aria-labelledby="q532045:2_answer3_label" /><div class="d-flex w-100" id="q532045:2_answer3_label" data-region="answer-label"><div class="flex-fill ml-1"><p dir="ltr" style="text-align: left;">Oeste<br></p></div></div> </div>
</div></div></div></div></div><div id="question-532045-1" class="que calculated deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">7</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:1_:flagged" value="0" /><input type="checkbox" id="q532045:1_:flaggedcheckbox" name="q532045:1_:flagged" value="1" /><input type="hidden" value="qaid=3117118&amp;qubaid=532045&amp;qid=38010095&amp;slot=1&amp;checksum=6874ce76315a0a83948ec35a2fbdb365&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:1_:flaggedlabel" for="q532045:1_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:1_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:1_:sequencecheck" value="3" /><div class="qtext"><p dir="ltr" style="text-align: left;"><br></p><p dir="ltr" style="text-align: left;"><br></p><p dir="ltr" style="text-align: left;">Uma caixa com fundo mas sem tampa esta <em><strong>encolhendo</strong></em>, uniformemente em todas as direções, a uma taxa de 4 cm/s. No momento em que a altura da caixa é de 4 cm, a largura é de 4 cm e o comprimento é de 3 cm, qual é a taxa de variação em cm/s da superfície total da caixa?<br></p></div><div class="ablock form-inline"><label for="q532045:1_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:1_answer" value="-148" id="q532045:1_answer" size="30" class="form-control d-inline" readonly="readonly" /></span></div></div></div></div><div id="question-532045-10" class="que calculated deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">8</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:10_:flagged" value="0" /><input type="checkbox" id="q532045:10_:flaggedcheckbox" name="q532045:10_:flagged" value="1" /><input type="hidden" value="qaid=3117127&amp;qubaid=532045&amp;qid=38474602&amp;slot=10&amp;checksum=f63c849979f2f1b278eede1e6a8ae379&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:10_:flaggedlabel" for="q532045:10_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:10_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:10_:sequencecheck" value="3" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p dir="ltr" style="text-align: left;">Uma caixa fechada, de formato de paralelepípedo, com <span class="nolink">\(2304\)</span> metros cúbicos de volume deve ser construída usando três classes de materiais. O custo do material para as partes superiores e inferiores é de <span class="nolink">\(4\)</span> reais por metro quadrado; o custo do material usado&nbsp;na parte de frente e de trás é de <span class="nolink">\(6\)</span> reais por metro quadrado; e o custo para os outros lados é de <span class="nolink">\(96\)</span> reais o metro quadrado. Determine as dimensões da caixa de forma que o custo total em material seja mínimo.&nbsp; Forneça como resposta o valor do custo mínimo.&nbsp;<br></p></span></div><div class="ablock form-inline"><label for="q532045:10_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:10_answer" value="13824" id="q532045:10_answer" size="30" class="form-control d-inline" readonly="readonly" /></span></div></div></div></div><div id="question-532045-8" class="que match deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">9</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:8_:flagged" value="0" /><input type="checkbox" id="q532045:8_:flaggedcheckbox" name="q532045:8_:flagged" value="1" /><input type="hidden" value="qaid=3117125&amp;qubaid=532045&amp;qid=38011632&amp;slot=8&amp;checksum=71cac4c14ac46627e0ae9069e0b8b3a7&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:8_:flaggedlabel" for="q532045:8_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:8_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:8_:sequencecheck" value="3" /><div class="qtext"><p>Para cada uma das afirmações abaixo marque Verdadeira ou Falsa<br></p></div><div class="ablock"><table class="answer"><tbody><tr class="r0"><td class="text"><span class="filter_mathjaxloader_equation"><ul>
    <li>Se <span class="nolink">\( \vec{F} = P \vec{i} + Q \vec{j} \)</span> é um campo no plano com <span class="nolink">\( 
\frac{\partial Q}{\partial x}(x,y) = \frac{\partial P}{\partial y} (x,y)
 \)</span> para todo <span class="nolink">\( (x,y) \)</span> no domínio de <span class="nolink">\(\vec{F}\)</span>, então <span class="nolink">\( \vec{F} 
\)</span> é conservativo.<br></li>
</ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub0">Resposta 1</label><select disabled="disabled" class="select custom-select custom-select ml-1" id="menuq532045:8_sub0" name="q532045:8_sub0"><option value="0">Escolher...</option><option selected="selected" value="1">Verdadeira</option><option value="2">Falsa</option></select> </td></tr><tr class="r1"><td class="text"><span class="filter_mathjaxloader_equation"><ul><li>Sendo <span class="nolink">\(E\)</span> um sólido no espaço e <span class="nolink">\(\vec{F} = (y,x,z)\)</span> vale 
<span class="nolink">\(\text{vol}(E) = \iint_S \vec{F} \)</span>, onde <span class="nolink">\(\text{vol}(E)\)</span>
 denota o volume de <span class="nolink">\(E\)</span> e <span class="nolink">\(S = \partial E\)</span> orientada positivamente.<br></li></ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub1">Resposta 2</label><select disabled="disabled" class="select custom-select custom-select ml-1" id="menuq532045:8_sub1" name="q532045:8_sub1"><option value="0">Escolher...</option><option value="1">Verdadeira</option><option selected="selected" value="2">Falsa</option></select> </td></tr><tr class="r0"><td class="text"><span class="filter_mathjaxloader_equation"><ul>
    <li><ul>
    </ul>Sendo <span class="nolink">\( \vec{F}(x,y) = 
\left( \frac{-y}{x^2 + y^2}, \frac{x}{x^2 + y^2} \right) \)</span> então <span class="nolink">\( \int_C \vec{F} \)</span> apenas depende dos pontos iniciais e finais de <span class="nolink">\(C\)</span>.<br><ul>
</ul><br></li>
</ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub2">Resposta 3</label><select disabled="disabled" class="select custom-select custom-select ml-1" id="menuq532045:8_sub2" name="q532045:8_sub2"><option value="0">Escolher...</option><option value="1">Verdadeira</option><option selected="selected" value="2">Falsa</option></select> </td></tr><tr class="r1"><td class="text"><span class="filter_mathjaxloader_equation"><ul>
    <li>Seja <span class="nolink">\(E\)</span> um sólido no espaço e <span class="nolink">\(S = \partial E\)</span> sua fronteira. Então <span class="nolink">\(\iint_S \vec{F} = 0\)</span> para todo campo constante <span class="nolink">\(\vec{F}\)</span> no espaço.<br></li>
</ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub3">Resposta 4</label><select disabled="disabled" class="select custom-select custom-select ml-1" id="menuq532045:8_sub3" name="q532045:8_sub3"><option value="0">Escolher...</option><option value="1">Verdadeira</option><option selected="selected" value="2">Falsa</option></select> </td></tr><tr class="r0"><td class="text"><span class="filter_mathjaxloader_equation"><ul>
    <li>Seja <span class="nolink">\(R\)</span> uma placa fina no plano com função densidade superficial <span class="nolink">\(\rho\)</span> e centro de massa <span class="nolink">\( (\bar{x},\bar{y}) \)</span>. Se <span class="nolink">\(\rho\)</span> é par com relação à <span class="nolink">\(x\)</span> e <span class="nolink">\(R\)</span> é simétrica com relação ao eixo <span class="nolink">\(x\)</span>, vale <span class="nolink">\(\bar{x} = 0\)</span>.<br></li>
</ul></span></td><td class="control"><label class="accesshide" for="menuq532045:8_sub4">Resposta 5</label><select disabled="disabled" class="select custom-select custom-select ml-1" id="menuq532045:8_sub4" name="q532045:8_sub4"><option value="0">Escolher...</option><option selected="selected" value="1">Verdadeira</option><option value="2">Falsa</option></select> </td></tr></tbody></table></div></div></div></div><div id="question-532045-7" class="que calculated deferredfeedback complete"><div class="info"><h3 class="no">Questão <span class="qno">10</span></h3><div class="state">Completo</div><div class="grade">Vale 1,00 ponto(s).</div><div class="questionflag editable" aria-atomic="true" aria-relevant="text" aria-live="assertive"><input type="hidden" name="q532045:7_:flagged" value="0" /><input type="checkbox" id="q532045:7_:flaggedcheckbox" name="q532045:7_:flagged" value="1" /><input type="hidden" value="qaid=3117124&amp;qubaid=532045&amp;qid=38010849&amp;slot=7&amp;checksum=6954e97da1b0986f7ad156e814c8f8a7&amp;sesskey=XFg5yMJK6p&amp;newstate=" class="questionflagpostdata" /><label id="q532045:7_:flaggedlabel" for="q532045:7_:flaggedcheckbox"><img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/core/1627934520/i/unflagged" alt="Não marcada" class="questionflagimage" id="q532045:7_:flaggedimg" /><span>Marcar questão</span></label>
</div></div><div class="content"><div class="formulation clearfix"><h4 class="accesshide">Texto da questão</h4><input type="hidden" name="q532045:7_:sequencecheck" value="3" /><div class="qtext"><span class="filter_mathjaxloader_equation"><p><span class="nolink">\(\DeclareMathOperator{\arctg}{arctg} \DeclareMathOperator{\sen}{sen}\)</span>Seja <span class="nolink">\(C\)</span> a curva no plano dada pelos segmentos de retas da figura abaixo, percorrida no sentido indicado. Seja também o 
campo de vetores</p><p><span class="nolink">\[ \vec{F}(x,y) = \left( x e^{\arctg(1 + x^{15})} - 6y; y^2 \cos(3 x^{31})&nbsp; \right) .\]</span></p><p>Se
 o valor da área do triângulo equilátero de vértices <span class="nolink">\(A,B,C\)</span> é igual à <span class="nolink">\(15\)</span> e <span class="nolink">\( \int_C 
\vec{F} = 18\)</span>, qual é o
 valor da área do triângulo equilátero de vértices <span class="nolink">\(P,Q,R\)</span>?</p><p><strong>(OBS.: escreva sua resposta com precisão de 2 casas decimais)</strong></p><p><strong><br></strong></p><p style="text-align: center;"><strong><img src="https://ufprvirtual.ufpr.br/pluginfile.php/644467/question/questiontext/532045/7/38010849/triangulo-encaixado2.png" alt="" role="presentation" class="img-responsive atto_image_button_text-bottom" width="719" height="500"></strong><br></p><p></p></span></div><div class="ablock form-inline"><label for="q532045:7_answer">Resposta:</label><span class="answer"><input type="text" name="q532045:7_answer" value="90" id="q532045:7_answer" size="30" class="form-control d-inline" readonly="readonly" /></span></div></div></div></div><input type="hidden" name="sesskey" value="XFg5yMJK6p" /><div class="submitbtns"><input type="submit" class="questionflagsavebutton btn btn-secondary" name="savingflags" value="Gravar o estado das marcas" /></div></div></form><div class="submitbtns"><a class="mod_quiz-next-nav" href="https://ufprvirtual.ufpr.br/mod/quiz/view.php?id=475948">Terminar revisão</a></div></div>
                            
                        </div>
                    </div>
                </section>
            </div>
                <div class="col-md-3 col-sm-12 col-12">
                    <aside id="block-region-side-pre" class="block-region" data-blockregion="side-pre" data-droptarget="1"><a href="#sb-1" class="sr-only sr-only-focusable">Pular &lt;span id=&quot;mod_quiz_navblock_title&quot;&gt;Navegação do questionário&lt;/span&gt;</a>

<section id="mod_quiz_navblock"
     class=" block block_fake  card mb-3"
     role="navigation"
     data-block="_fake"
          aria-labelledby="instance-fakeid-611333259cd3e-header"
     >

    <div class="card-body p-3">

            <h5 id="instance-fakeid-611333259cd3e-header" class="card-title d-inline"><span id="mod_quiz_navblock_title">Navegação do questionário</span></h5>


        <div class="card-text content mt-3">
            <div class="qn_buttons clearfix allquestionsononepage"><a class="qnbutton complete free btn thispage" id="quiznavbutton5" title="Completo" data-quiz-page="0" href="#"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>1<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton6" title="Completo" data-quiz-page="0" href="#question-532045-6"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>2<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton3" title="Completo" data-quiz-page="0" href="#question-532045-3"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>3<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton4" title="Completo" data-quiz-page="0" href="#question-532045-4"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>4<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton9" title="Completo" data-quiz-page="0" href="#question-532045-9"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>5<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton2" title="Completo" data-quiz-page="0" href="#question-532045-2"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>6<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton1" title="Completo" data-quiz-page="0" href="#question-532045-1"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>7<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton10" title="Completo" data-quiz-page="0" href="#question-532045-10"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>8<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton8" title="Completo" data-quiz-page="0" href="#question-532045-8"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>9<span class="accesshide"> Esta página <span class="flagstate"></span></span></a><a class="qnbutton complete free btn thispage" id="quiznavbutton7" title="Completo" data-quiz-page="0" href="#question-532045-7"><span class="thispageholder"></span><span class="trafficlight"></span><span class="accesshide">Questão </span>10<span class="accesshide"> Esta página <span class="flagstate"></span></span></a></div><div class="othernav"><a class="mod_quiz-next-nav" href="https://ufprvirtual.ufpr.br/mod/quiz/view.php?id=475948">Terminar revisão</a></div>
            <div class="footer"></div>
            
        </div>

    </div>

</section>

  <span id="sb-1"></span></aside>
                </div>
        </div>
    </div>

    <div id="nav-drawer" data-region="drawer" class="hidden-print moodle-has-zindex closed" aria-hidden="true" tabindex="-1">
            <div id="btn-navdraweropen" data-region="drawer-toggle" class="d-inline-block mr-3 drawer-toggle">
                <button aria-expanded="false" aria-controls="nav-drawer" type="button" class="btn nav-link float-sm-left mr-1" data-action="toggle-drawer" data-side="left" data-preference="drawer-open-nav"><i class="slicon-menu"></i><span class="sr-only">Painel lateral</span></button>
            </div>
        <ul class="list-group metismenu">
                    <li class="list-group-item" data-key="course-sections">
                        <a class="m-l-0 has-arrow" href="javascript:void(0);">
                            <span class="text">Seções da disciplina</span>
                        </a>
                        <ul>
                                    <li class="list-group-item list-group-item-action " data-key="111164">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=1">
                                            <span class="text">Apresentação</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111165">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=2">
                                            <span class="text">Módulo 1 - Funções Vetoriais</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111166">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=3">
                                            <span class="text">Módulo 2 - Cálculo Diferencial</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111167">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=4">
                                            <span class="text">Módulo 3 - Máximos e Mínimos</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111180">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=5">
                                            <span class="text">Módulo 4 - Integração</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="111181">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=6">
                                            <span class="text">Módulo 5 - Cálculo Vetorial</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="114035">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981&amp;section=7">
                                            <span class="text">Segundas Chamadas e Exame Final</span>
                                        </a>
                                    </li>
                        </ul>
                    </li>
                        <li class="list-group-item list-group-item-action " data-key="participants">
                            <a href="https://ufprvirtual.ufpr.br/user/index.php?id=16981">
                                <span class="text m-l-0">Participantes</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="certificates">
                            <a href="https://ufprvirtual.ufpr.br/theme/moove/certificates.php?id=16981">
                                <span class="text m-l-0">Certificados</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="competencies">
                            <a href="https://ufprvirtual.ufpr.br/admin/tool/lp/coursecompetencies.php?courseid=16981">
                                <span class="text m-l-0">Competências</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="grades">
                            <a href="https://ufprvirtual.ufpr.br/grade/report/index.php?id=16981">
                                <span class="text m-l-0">Notas</span>
                            </a>
                        </li>
                </ul>
                <ul class="list-group metismenu">
                        <li class="list-group-item list-group-item-action " data-key="myhome">
                            <a href="https://ufprvirtual.ufpr.br/my/">
                                <span class="text m-l-0">Painel</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="home">
                            <a href="https://ufprvirtual.ufpr.br/?redirect=0">
                                <span class="text m-l-0">Página inicial do site</span>
                            </a>
                        </li>
                        <li class="list-group-item list-group-item-action " data-key="calendar">
                            <a href="https://ufprvirtual.ufpr.br/calendar/view.php?view=month&amp;course=16981">
                                <span class="text m-l-0">Calendário</span>
                            </a>
                        </li>
                    <li class="list-group-item" data-key="mycourses">
                        <a class="m-l-0 has-arrow" href="javascript:void(0);">
                            <span class="text">Minhas salas</span>
                        </a>
                        <ul>
                                    <li class="list-group-item list-group-item-action " data-key="14547">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=14547">
                                            <span class="text">PE2_CMCALCULO2_2020</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="4053">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=4053">
                                            <span class="text">CE084_2020_EST_352965</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="1876">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=1876">
                                            <span class="text">CM042_2020_EST_352963</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="10045">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=10045">
                                            <span class="text">PE_CE095_2020_357893</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="16981">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=16981">
                                            <span class="text">ERE3_Calculo2_2021</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="18951">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=18951">
                                            <span class="text">20202_CE083_394464</span>
                                        </a>
                                    </li>
                                    <li class="list-group-item list-group-item-action " data-key="6123">
                                        <a class="list-group-item list-group-item-action " href="https://ufprvirtual.ufpr.br/course/view.php?id=6123">
                                            <span class="text">Estudantes</span>
                                        </a>
                                    </li>
                        </ul>
                    </li>
                        <li class="list-group-item list-group-item-action " data-key="privatefiles">
                            <a href="https://ufprvirtual.ufpr.br/user/files.php">
                                <span class="text m-l-0">Meus arquivos</span>
                            </a>
                        </li>
        </ul>    </div>

</div>


<div id="top-footer">
  <div class="container-fluid">
    <div class="row">
        <div class="col-md-7 contact">
            
        </div>
        <div class="col-md-5 social">
            <div class="plugins_standard_footer_html"><div class="tool_dataprivacy"><a href="https://ufprvirtual.ufpr.br/admin/tool/dataprivacy/summary.php">Resumo de retenção de dados</a></div><div><p style="margin-bottom:0px">Obter o aplicativo para dispositivos móveis</p><a title="Obter o aplicativo para dispositivos móveis" href="https://play.google.com/store/apps/details?id=br.ufpr.ufprvirtual&amp;hl=pt-pt"><img src='https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/google_play_badge_ptbr' style='height:72px'></img></a><a title="Obter o aplicativo para dispositivos móveis" href="https://apps.apple.com/br/app/ufpr-virtual/id1529174904"><img src='https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/app_store_badge_ptbr' style='height:50px'></img></a></div></div>
        </div>
    </div>
  </div>
</div>


  
  <div class="footer" >
      
      <div class="texto_rodape large-2 medium-2" >
              <span >
                  <img id="image-footer" src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/logo_white">
              </span>
  
      </div>
      <div class="texto_rodape large-8 medium-8">
          <br>CIPEAD - Coordenadoria de Integração de Políticas de Educação a Distância da Universidade Federal do Paraná
          <br>Praça Santos Andrade, 50 - Centro - Telefone:(41)3310-2657 - CEP:80.020-300 - Curitiba/PR
      </div>
      <div class="texto_rodape large-2 medium-2 social_media" >
          <a href="https://www.facebook.com/Cipead-UFPR-505827566179117/" 
              onclick="javascript:window.open('https://www.facebook.com/Cipead-UFPR-505827566179117/'); 
              return false;">
              <img src="https://ufprvirtual.ufpr.br/theme/image.php/ufprvirtual/theme_ufprvirtual/1627934520/facebook_logo_white" title="CIPEAD no Facebook" alt="CIPEAD no Facebook" width="45" height="45">
          </a>
       </div>
    
  </div>
  <div id="sub-footer" class="footer" >
      <div class="copyright texto_rodape large-2 medium-2">
              Direitos autorais - ícones: <a href="https://www.flaticon.com/" style="color:#fff">Flat Icon</a>
              
              
      </div>  
  </div>

<script>
//<![CDATA[
var require = {
    baseUrl : 'https://ufprvirtual.ufpr.br/lib/requirejs.php/1627934520/',
    // We only support AMD modules with an explicit define() statement.
    enforceDefine: true,
    skipDataMain: true,
    waitSeconds : 0,

    paths: {
        jquery: 'https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/jquery/jquery-3.5.1.min',
        jqueryui: 'https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/jquery/ui-1.12.1/jquery-ui.min',
        jqueryprivate: 'https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/requirejs/jquery-private'
    },

    // Custom jquery config map.
    map: {
      // '*' means all modules will get 'jqueryprivate'
      // for their 'jquery' dependency.
      '*': { jquery: 'jqueryprivate' },
      // Stub module for 'process'. This is a workaround for a bug in MathJax (see MDL-60458).
      '*': { process: 'core/first' },

      // 'jquery-private' wants the real jQuery module
      // though. If this line was not here, there would
      // be an unresolvable cyclic dependency.
      jqueryprivate: { jquery: 'jquery' }
    }
};

//]]>
</script>
<script src="https://ufprvirtual.ufpr.br/lib/javascript.php/1627934520/lib/requirejs/require.min.js"></script>
<script>
//<![CDATA[
M.util.js_pending("core/first");require(['core/first'], function() {
require(['core/prefetch']);
;
require(["media_videojs/loader"], function(loader) {
    loader.setUp(function(videojs) {
        videojs.options.flash.swf = "https://ufprvirtual.ufpr.br/media/player/videojs/videojs/video-js.swf";
videojs.addLanguage('pt-BR', {
  "Audio Player": "Reprodutor de áudio",
  "Video Player": "Reprodutor de vídeo",
  "Play": "Tocar",
  "Pause": "Pausar",
  "Replay": "Tocar novamente",
  "Current Time": "Tempo",
  "Duration": "Duração",
  "Remaining Time": "Tempo Restante",
  "Stream Type": "Tipo de Stream",
  "LIVE": "AO VIVO",
  "Loaded": "Carregado",
  "Progress": "Progresso",
  "Progress Bar": "Barra de progresso",
  "progress bar timing: currentTime={1} duration={2}": "{1} de {2}",
  "Fullscreen": "Tela Cheia",
  "Non-Fullscreen": "Tela Normal",
  "Mute": "Mudo",
  "Unmute": "Habilitar Som",
  "Playback Rate": "Velocidade",
  "Subtitles": "Legendas",
  "subtitles off": "Sem Legendas",
  "Captions": "Anotações",
  "captions off": "Sem Anotações",
  "Chapters": "Capítulos",
  "Descriptions": "Descrições",
  "descriptions off": "sem descrições",
  "Audio Track": "Faixa de áudio",
  "Volume Level": "Nível de volume",
  "You aborted the media playback": "Você parou a execução do vídeo.",
  "A network error caused the media download to fail part-way.": "Um erro na rede causou falha durante o download da mídia.",
  "The media could not be loaded, either because the server or network failed or because the format is not supported.": "A mídia não pode ser carregada, por uma falha de rede ou servidor ou o formato não é suportado.",
  "No compatible source was found for this media.": "Nenhuma fonte foi encontrada para esta mídia.",
  "The media playback was aborted due to a corruption problem or because the media used features your browser did not support.": "A reprodução foi interrompida devido à um problema de mídia corrompida ou porque a mídia utiliza funções que seu navegador não suporta.",
  "The media is encrypted and we do not have the keys to decrypt it.": "A mídia está criptografada e não temos as chaves para descriptografar.",
  "Play Video": "Tocar Vídeo",
  "Close": "Fechar",
  "Close Modal Dialog": "Fechar Diálogo Modal",
  "Modal Window": "Janela Modal",
  "This is a modal window": "Isso é uma janela-modal",
  "This modal can be closed by pressing the Escape key or activating the close button.": "Esta janela pode ser fechada pressionando a tecla de Escape.",
  ", opens captions settings dialog": ", abre as configurações de legendas de comentários",
  ", opens subtitles settings dialog": ", abre as configurações de legendas",
  ", opens descriptions settings dialog": ", abre as configurações",
  ", selected": ", selecionada",
  "captions settings": "configurações de legendas de comentários",
  "subtitles settings": "configurações de legendas",
  "descriptions settings": "configurações das descrições",
  "Text": "Texto",
  "White": "Branco",
  "Black": "Preto",
  "Red": "Vermelho",
  "Green": "Verde",
  "Blue": "Azul",
  "Yellow": "Amarelo",
  "Magenta": "Magenta",
  "Cyan": "Ciano",
  "Background": "Plano-de-Fundo",
  "Window": "Janela",
  "Transparent": "Transparente",
  "Semi-Transparent": "Semi-Transparente",
  "Opaque": "Opaco",
  "Font Size": "Tamanho da Fonte",
  "Text Edge Style": "Estilo da Borda",
  "None": "Nenhum",
  "Raised": "Elevado",
  "Depressed": "Acachapado",
  "Uniform": "Uniforme",
  "Dropshadow": "Sombra de projeção",
  "Font Family": "Família da Fonte",
  "Proportional Sans-Serif": "Sans-Serif(Sem serifa) Proporcional",
  "Monospace Sans-Serif": "Sans-Serif(Sem serifa) Monoespaçada",
  "Proportional Serif": "Serifa Proporcional",
  "Monospace Serif": "Serifa Monoespaçada",
  "Casual": "Casual",
  "Script": "Script",
  "Small Caps": "Maiúsculas Pequenas",
  "Reset": "Redefinir",
  "restore all settings to the default values": "restaurar todas as configurações aos valores padrão",
  "Done": "Salvar",
  "Caption Settings Dialog": "Caíxa-de-Diálogo das configurações de Legendas",
  "Beginning of dialog window. Escape will cancel and close the window.": "Iniciando a Janela-de-Diálogo. Pressionar Escape irá cancelar e fechar a janela.",
  "End of dialog window.": "Fim da Janela-de-Diálogo",
  "{1} is loading.": "{1} está carregando."
});

    });
});;

require(['jquery', 'core/custom_interaction_events'], function($, CustomEvents) {
    CustomEvents.define('#single_select611333259bed35', [CustomEvents.events.accessibleChange]);
    $('#single_select611333259bed35').on(CustomEvents.events.accessibleChange, function() {
        var ignore = $(this).find(':selected').attr('data-ignore');
        if (typeof ignore === typeof undefined) {
            $('#single_select_f611333259bed34').submit();
        }
    });
});
;

require(['jquery', 'message_popup/notification_popover_controller'], function($, controller) {
    var container = $('#nav-notification-popover-container');
    var controller = new controller(container);
    controller.registerEventListeners();
    controller.registerListNavigationEventListeners();
});
;

require(
[
    'jquery',
    'core_message/message_popover'
],
function(
    $,
    Popover
) {
    var toggle = $('#message-drawer-toggle-61133325a92c2611333259bed38');
    Popover.init(toggle);
});
;

require(['theme_boost/loader']);
require(['theme_boost/drawer'], function(mod) {
    mod.init();
});
;
M.util.js_pending('qtype_multichoice/answers'); require(['qtype_multichoice/answers'], function(amd) {amd.init("question-532045-2"); M.util.js_complete('qtype_multichoice/answers');});;
M.util.js_pending('core/notification'); require(['core/notification'], function(amd) {amd.init(1029309, [], true); M.util.js_complete('core/notification');});;
M.util.js_pending('core/log'); require(['core/log'], function(amd) {amd.setConfig({"level":"warn"}); M.util.js_complete('core/log');});;
M.util.js_pending('core/page_global'); require(['core/page_global'], function(amd) {amd.init(); M.util.js_complete('core/page_global');});M.util.js_complete("core/first");
});
//]]>
</script>
<script>
//<![CDATA[
M.yui.add_module({"core_question_engine":{"name":"core_question_engine","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/question\/qengine.js","requires":["node","event"]},"mod_quiz":{"name":"mod_quiz","fullpath":"https:\/\/ufprvirtual.ufpr.br\/lib\/javascript.php\/1627934520\/mod\/quiz\/module.js","requires":["base","dom","event-delegate","event-key","core_question_engine","moodle-core-formchangechecker"]}});

//]]>
</script>
<script>
//<![CDATA[
M.str = {"moodle":{"lastmodified":"\u00daltima atualiza\u00e7\u00e3o","name":"Nome","error":"Erro","info":"Informa\u00e7\u00e3o","yes":"Sim","no":"N\u00e3o","cancel":"Cancelar","changesmadereallygoaway":"Voc\u00ea fez altera\u00e7\u00f5es. Est\u00e1 certo de que quer sair e abandonar suas altera\u00e7\u00f5es?","confirm":"Confirmar","areyousure":"Voc\u00ea tem certeza?","closebuttontitle":"Fechar","unknownerror":"Erro desconhecido","file":"Arquivo","url":"URL"},"repository":{"type":"Tipo","size":"Tamanho","invalidjson":"palavra JSON inv\u00e1lida","nofilesattached":"Nenhum arquivo anexado","filepicker":"Seletor de arquivos","logout":"Sair","nofilesavailable":"Nenhum arquivo dispon\u00edvel","norepositoriesavailable":"Desculpe, nenhum dos seus reposit\u00f3rios atuais pode retornar arquivos no formato solicitado.","fileexistsdialogheader":"Arquivo existe","fileexistsdialog_editor":"Um arquivo com este nome j\u00e1 foi anexado ao texto que voc\u00ea est\u00e1 editando.","fileexistsdialog_filemanager":"Um arquivo com este nome j\u00e1 foi anexado","renameto":"Renomear para \"{$a}\"","referencesexist":"Existem {$a} links para esse arquivo","select":"Selecione"},"admin":{"confirmdeletecomments":"Voc\u00ea est\u00e1 prestes a excluir coment\u00e1rios, tem certeza?","confirmation":"Confirma\u00e7\u00e3o"},"question":{"flagged":"Marcada"},"quiz":{"functiondisabledbysecuremode":"Esta funcionalidade est\u00e1 desativada no momento","startattempt":"Iniciar tentativa","timesup":"Acabou o tempo de dura\u00e7\u00e3o!"},"debug":{"debuginfo":"Informa\u00e7\u00f5es de depura\u00e7\u00e3o","line":"Linha","stacktrace":"Rastreamento de pilha"},"langconfig":{"labelsep":":&nbsp;"}};
//]]>
</script>
<script>
//<![CDATA[
(function() {Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.configure({"mathjaxconfig":"MathJax.Hub.Config({\r\n    config: [\"Accessible.js\", \"Safe.js\"],\r\n    errorSettings: { message: [\"!\"] },\r\n    skipStartupTypeset: true,\r\n    messageStyle: \"none\"\r\n});\r\n","lang":"pt-br"});
});
 M.util.js_pending('random611333259bed31'); Y.use('core_question_flags', function(Y) { M.core_question_flags.init(Y, "https:\/\/ufprvirtual.ufpr.br\/question\/toggleflag.php", [{"src":"https:\/\/ufprvirtual.ufpr.br\/theme\/image.php\/ufprvirtual\/core\/1627934520\/i\/unflagged","title":"Marcar quest\u00e3o para refer\u00eancia futura","alt":"N\u00e3o marcada"},{"src":"https:\/\/ufprvirtual.ufpr.br\/theme\/image.php\/ufprvirtual\/core\/1627934520\/i\/flagged","title":"Remover marca\u00e7\u00e3o","alt":"Marcada"}], ["Marcar quest\u00e3o","Remover marca\u00e7\u00e3o"]);  M.util.js_complete('random611333259bed31'); });
 M.util.js_pending('random611333259bed32'); Y.use('mod_quiz', function(Y) { M.mod_quiz.nav.init(Y);  M.util.js_complete('random611333259bed32'); });
M.util.help_popups.setup(Y);
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
Y.use("moodle-filter_mathjaxloader-loader",function() {M.filter_mathjaxloader.typeset();
});
 M.util.js_pending('random611333259bed318'); Y.use('mod_quiz', function(Y) { M.mod_quiz.init_review_form(Y);  M.util.js_complete('random611333259bed318'); });
 M.util.js_pending('random611333259bed319'); Y.on('domready', function() { M.util.js_complete("init");  M.util.js_complete('random611333259bed319'); });
})();
//]]>
</script>


</body>
</html>
