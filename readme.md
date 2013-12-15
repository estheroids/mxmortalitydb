Injury Intent Deaths 2004-2012 in Mexico
========================================================

A data only package containing all injury intent deaths (accidents, suicides, homicides, legal interventions, and deaths of unspecified intent) registered by the SSA/INEGI from 2004 to 2012. The data source for the database is the [INEGI](http://www.inegi.org.mx/est/contenidos/proyectos/registros/vitales/mortalidad/default.aspx). In addition the data was coded with the Injury Mortality Matrix provided by the [cdc](http://www.cdc.gov/nchs/data/ice/icd10_transcode.pdf)


```r
## install.packages('devtools')
library(devtools)
## install_github('mxmortalitydb', 'diegovalle')
library(mxmortalitydb)
library(ggplot2)
library(plyr)
```


## Examples

All deaths of unknown intent in Sinaloa (state code 25) where the injury mechanism was a firearm, by year of registration:


```r
## The main data.frame in the package is called injury.intent
ddply(subset(injury.intent, is.na(intent) & mechanism == "Firearm" & state_reg == 
    25), .(year_reg, intent), summarise, count = length(state_reg))
```

```
##   year_reg intent count
## 1     2004   <NA>    11
## 2     2005   <NA>    11
## 3     2006   <NA>     2
## 4     2009   <NA>     8
## 5     2010   <NA>     7
## 6     2011   <NA>    25
## 7     2012   <NA>   197
```


Homicides merged with the agressor.relation.code table:


```r
df <- ddply(subset(injury.intent, intent = "Homicide"), .(year_reg, aggressor_relation_code), 
    summarise, count = length(state_reg))
## A couple of other tables are included in the package to interpret some of
## the values in injury.intent
merge(df, aggressor.relation.code)
```

```
##    aggressor_relation_code year_reg count            relationship
## 1                        1     2012    25                   Padre
## 2                        2     2012    64                   Madre
## 3                        3     2012    14                 Hermano
## 4                        4     2012     7                 Hermana
## 5                        5     2012    10                    Hijo
## 6                        6     2012     1                    Hija
## 7                        7     2012     5                  Abuelo
## 8                        8     2012     3                  Abuela
## 9                        9     2012     5                   Nieto
## 10                      10     2012     1                   Nieta
## 11                      11     2012    27         Esposo, Cónyuge
## 12                      12     2012     3         Esposa, Cónyuge
## 13                      13     2012     7                     Tío
## 14                      14     2012     1                     Tía
## 15                      15     2012    19                 Sobrino
## 16                      17     2012    14                   Primo
## 17                      21     2012     1                Bisnieto
## 18                      27     2012     2                  Suegro
## 19                      31     2012     4                   Yerno
## 20                      33     2012     9                  Cuñado
## 21                      34     2012     1                  Cuñada
## 22                      35     2012     1                 Concuño
## 23                      37     2012     6               Padrastro
## 24                      39     2012     2                Hijastro
## 25                      41     2012     1             Hermanastro
## 26                      45     2012     9    Concubino, compañero
## 27                      46     2012     4    Concubina, compañera
## 28                      47     2012     3 Amante, Amasio, Querido
## 29                      49     2012     2                   Novio
## 30                      51     2012     3               Ex esposo
## 31                      57     2012     1                Compadre
## 32                      66     2012     5                Conocido
## 33                      67     2012     5                  Vecino
## 34                      68     2012     6                   Amigo
## 35                      69     2012     1                   Amiga
## 36                      70     2012     3           Otro familiar
## 37                      71     2012     1          Sin parentesco
## 38                      72     2012   438                 Ninguno
## 39                      88     2012 47763               No aplica
## 40                      99     2012 25250         No especificado
```


A plot of female homicide counts:


```r
df <- ddply(subset(injury.intent, sex == "Female" & intent == "Homicide"), .(year_reg, 
    intent), summarise, count = length(state_reg))
ggplot(df, aes(year_reg, count)) + geom_line() + labs(title = "Female homicides in Mexico, by year of registration")
```

![plot of chunk unnamed-chunk-4](http://i.imgur.com/369LANr.png) 


## Warning

I encourage you to get acquainted with the database since it may contain some errors (from the source) and some fields may be difficult to interpret because of the large number of missing values (see above example). The field _intent.imputed_ is the result of running a statistical model to impute the intent of deaths of unknown intent and is mainly useful to the author of this package. Feel free to ignore the column.


```r
ddply(subset(injury.intent, intent.imputed == "Homicide"), .(year_reg), summarise, 
    count = length(state_reg))
```

```
##   year_reg count
## 1     2004 10554
## 2     2005 11129
## 3     2006 11615
## 4     2007 10456
## 5     2008 15391
## 6     2009 21220
## 7     2010 27660
## 8     2011 30312
## 9     2012 28086
```


## License

This package is free and open source software, licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).