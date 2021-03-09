Readme
================
Herbert Santos e Marilia Santiago
03/03/2021

## Tutorial DPLYR

![Dplyr](README_files/figure-gfm/dplyr.png)

# Sumário

<!--ts-->

-   [Passo a passo]()
-   [O Pacote dplyr](#pacote)
-   [Instalando o dplyr](#instalando)
-   [Funções principais](#funcoes)
    -   [Filters](#filter)
    -   [Select](#select)
    -   [Mutate](#mutate)
    -   [Arrange](#arrange)
-   [Conectando tudo](#conectando)
-   [Filtrando, Selecionando e criando colunas](#filtro)
-   [Agrupando e sumarizando](#agrupando)
-   [Exemplo com planilha Excel](#exemplo) <!--te-->

<h1 id="pacote">
O pacote dplyr
</h1>
<p>

O dplyr é um pacote da linguagem R desenvolvido especificamente para
manipulação de dados, muito utilizado em tarefas de pré-processamento
para machine learning. Ele faz parte da coleção de pacotes Tidyverse,
que tem como principal desenvolvedor Hadley Wickam, cientista chefe do
RStudio e professor de estatística na Universidade de Auckland, Stanford
University, e Rice University.

Se você ainda não tem o dplyr instalado na sua máquina, rode o seguinte
comando.
</p>

``` r
install.packages("dplyr", repos = "http://cran.r-project.org")
```

<p>
Depois carregue o pacote:
</p>

``` r
 library(dplyr)
```

<h1 id="funcoes">
Funções principais
</h1>
<p>
As funções do dplyr são todas em formas de verbo e, se você sabe um
pouco de inglês, são razoavelmente auto-explicativas. Vamos ver aqui as
seis principais funções que, em conjunto, já te permitem fazer bastante
coisa. Todas as funções possuem a mesma estrutura: o primeiro argumento
é o data.frame que você quer manipular e os argumentos subsequentes
dizem o que fazer com os dados. Além disso, as funções do dplyr sempre
retornam um data.frame, isto é, ela não vai tentar simplificar o
resultado para um vetor ou outro tipo de objeto.
</p>
<h3 id="filter">
§ filter:
</h3>
<p>

filtra um data.frame com vetores lógicos. Em outras palavras, deixa
apenas as linhas que satisfazem a certo critério. Por exemplo, nos
nossos dados de imóveis, poderíamos filtrar somente aqueles anúncios de
aluguel com valores de preço por metro quadrado menores ou maiores do
que determinado nível.

    1 # com dplyr
    2 filter(dados, pm2 > 96, tipo == "aluguel")
    3
    4 # equivalente com R base
    5 dados[dados$pm2 > 96 & dados$tipo == "aluguel", ]

<h3 id="select">
§ select:
</h3>
<p>
seleciona uma ou mais colunas de um data.frame. Por exemplo, poderíamos
selecionar a coluna de preços e quartos do anúncio.
</p>

    1 # com dplyr
    2 select(dados, preco, quartos)
    3 
    4 # equivalente com R base
    5 dados[, c("preco", "quartos")]

<h3 id="mutate">
§ mutate:
</h3>
<p>
cria uma nova coluna. Por exemplo, vamos criar a coluna pm2 como
preco/m2.
</p>

    1 # com dplyr
    2 dados <- mutate(dados, pm2 = preco/m2)
    3
    4 # equivalente com R base
    5 dados$pm2 <- dados$preco/dados$m2

<h3 id="arrange">
§ arrange:
</h3>
<p>
orderna o data.frame com base em uma coluna. Por exemplo, ordernar
observações dos apartamentos segundo os preços por metro quadrado.
</p>

    1 # com dplyr
    2 arrange(dados, pm2)
    3
    4 # equivalente com R base
    5 dados[order(dados$pm2), ]

<p>
Os próximos dois verbos são <strong>group\_by</strong> e
<strong>summarise</strong> que, em geral, são utilizados em conjunto com
outros verbos. Deixaremos, assim, para ver exemplos com essas estruturas
logo a seguir, quando conectarmos todos os verbos do dplyr com o
operador %&gt;%.
</p>
<h5>
§ group\_by:
</h5>
<p>
agrupa um data.frame segundo um vetor de categorias. “Agrupar” aqui quer
dizer que todas as operações subsequentes serão feitas separadas por
grupos. É equivalente ao split, que vimos antes.
</p>
<h5>
§ summarise:
</h5>
<p>
o summarise transforma um vetor com vários números (por exemplo, um
vetor de preços) em um único número de acordo com uma função (por
exemplo, preço médio ou preço mediano).
</p>
<h3 id="conectando">
Conectando tudo com %&gt;%
</h3>
<p>
Até agora talvez você não tenha visto muita vantagem em utilizar o
dplyr. Escrever filter(dados, pm2 &gt; 96, tipo == “aluguel”) pode ser
mais intuitivo do que dados\[dados$pm2 &gt; 96 & dados$tipo ==
“aluguel”, \] mas talvez não tanto assim. A grande vantagem do pacote e
das funções em formas de verbo aparece quando concatenamos várias
operações em sequência com o auxílio do operador %&gt;%.
</p>
<p>
O dplyr foi desenhado para trabalhar com o pipe operator %&gt;% do
pacote magritrr. Basicamente, o operador faz com que você possa escrever
x %&gt;% f() ao invés de f(x). Pode não parecer muito, mas na prática
esse pequeno detalhe tem uma grande utilidade: você vai poder escrever o
código de manipulação dos dados da mesma forma que você pensa nas
atividades.
</p>
<p>
Pense numa receita que tenha as seguintes instruções: <strong>junte os
igredientes, misture e leve ao forno</strong>. Na forma usual do R, tais
instruções provavelmente teriam a seguinte forma:
</p>

    1- forno(misture(junte(ingredientes)))

<p>
Note que temos que pensar “de dentro para fora”. O primeiro comando que
lemos é forno que, na verdade, é a última operação que será realizada!
Com o pipe operator, por outro lado, você escreveria algo mais ou menos
assim:
</p>

    1- ingredientes %>% junte %>% misture %>% forno

<p>
É quase igual a ler instruções verbais da receita. Parece mais
intuitivo, não?
</p>
<p>
Vejamos exemplos de manipulações de dados com o dplyr usando uma base de
dados de imóveis.
</p>
<h3 id="filtro">
Filtrando, selecionando e criando colunas
</h3>
<p>
<strong>Instrução:</strong> Filtre apenas os dados coletados de
apartamento, selecione as colunas bairro e preco, crie uma coluna pm2 =
preco/m2, ordene os dados de forma decrescente em pm2 e mostre apenas as
6 primeiras linhas (head).
</p>

<strong>Código:</strong>

``` r
1- dados %>% filter(imovel=="apartamento") %>% 
2- select(bairro, preco, m2) %>% mutate(pm2 = preco/m2) %>%
3- arrange(desc(pm2)) %>% head
```

    1-  ## Source: local data frame [6 x 4]
    2-  ##
    3-  ##    bairro   preco     m2     pm2
    4-  ##     (chr)   (dbl)  (dbl) (dbl)
    5-  ## 1 Asa Sul 4259579 215.72 19745.87
    6-  ## 2 Asa Sul 4259579 215.72 19745.87
    7-  ## 3 Asa Sul 4259579 215.72 19745.87
    8-  ## 4 Asa Sul 4259579 215.72 19745.87
    9-  ## 5 Asa Sul 4259579 215.72 19745.87
    10- ## 6 Asa Sul 4259579 215.72 19745.87

<h3 id="agrupando">
Agrupando e sumarizando
</h3>
<p>
<strong>Instrução:</strong> Filtre apenas os dados de venda de
apartamento. Agrupe os dados por bairro. Calcule as medianas do preco,
m2 e pm2, bem como o número de observações. Filtre apenas os grupos com
mais de 30 observações. Ordene de forma decrescente com base na mediana
de pm2.
</p>

<strong>Código:</strong>

``` r
1-  dados %>%
2-  filter(imovel=="apartamento", tipo=="venda") %>%
3-  group_by(bairro) %>%
4-  summarise(Mediana_Preco = median(preco),
5-          Mediana_M2 = median(m2),
6-          Mediana_pm2 = median(pm2),
7-          Obs = length(pm2)) %>%
8-  filter(Obs > 30) %>%
9-  arrange(desc(Mediana_pm2))
```


    1-  ## Source: local data frame [6 x 5]
    2-  ##
    3-  ##       bairro Mediana_Preco Mediana_M2 Mediana_pm2  Obs
    4-  ##        (chr)         (dbl)    (dbl)      (dbl)   (int)
    5-  ## 1   Sudoeste        850000    86.00  9689.922   20356
    6-  ## 2   Noroeste        950000   100.90  9654.000   36610
    7-  ## 3    Asa Sul        950000   107.00  9066.667   35241
    8-  ## 4  Asa Norte        780000    94.00  9000.000   40023
    9-  ## 5 Lago Norte        470000    57.55  8329.250    5938
    10- ## 6   Lago Sul        488307    88.55   5199.120    477

<h3 id="exemplo">
Exemplo utilizando planilha do Excel
</h3>

``` r
 library(xlsx) #UTILIZADA PARA LER ARQUIVOS XLSX DIRETAMENTE
```

    ## Warning: package 'xlsx' was built under R version 4.0.4

``` r
 library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
## Indicando o arquivo xlsx que será carregado na variável dados

 dados = read.xlsx(file='Turma.xlsx', sheetIndex = 1)
 dados %>%
    # AGRUPANDO DADOS ESPECIFICOS
   group_by(Media,Nota1,Nota2, Aluno) %>% 
   # ORGANIZANDO AS MEDIAS MAIORES OU IGUAIS A 7 EM ORDEM CRESCENTE
    filter(Media>="7") %>% 
  summarise()
```

    ## `summarise()` has grouped output by 'Media', 'Nota1', 'Nota2'. You can override using the `.groups` argument.

    ## # A tibble: 3 x 4
    ## # Groups:   Media, Nota1, Nota2 [3]
    ##   Media Nota1 Nota2 Aluno      
    ##   <dbl> <dbl> <dbl> <chr>      
    ## 1   7      10     4 Maria Rita 
    ## 2   7.5     7     8 Joao Pedro 
    ## 3   9       8    10 Maros Paulo

Você pode utilizar o pacote Dplyr todas as vezes que desejar trabalhar
com a organização dos dados de uma tabela. O Dplyr possibilita um
trabalho de forma mais intuitiva para que haja uma melhor compreensão
dos dados contidos em uma tabela.
