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
[1] "interest_over_time"  "interest_by_country" "interest_by_region" 
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


  

 
 
 
 
 
 
 
 
 
 
 
 
 
 

<div id="disqus_thread"></div>
<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
    /*
    var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://statistics-quality.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>









  

               
















