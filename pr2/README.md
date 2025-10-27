# Основы обработки данных с помощью R и Dplyr
sniper12.04@yandex.ru

## Цель работы

1.  Развить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания базовых типов данных языка R
3.  Развить практические навыки использования функций обработки данных
    пакета dplyr – функции select(), filter(), mutate(), arrange(),
    group_by()

## Исходные данные

1.  Программное обеспечение ОСWindows 10 Pro
2.  RStudio
3.  Интерпретатор языка R 4.5.1

## План

1.  Подключение пакета dplyr  
2.  Получение количества строк в датафрейме  
3.  Получение количества столбцов в датафрейме  
4.  Просмотр структуры датафрейма  
5.  Подсчёт количества уникальных рас  
6.  Поиск самого высокого персонажа  
7.  Поиск персонажей ниже 170 см  
8.  Расчёт индекса массы тела (ИМТ)  
9.  Поиск 10 самых “вытянутых” персонажей  
10. Расчёт среднего возраста по расам  
11. Определение самого распространённого цвета глаз  
12. Расчёт средней длины имени по расам

## Шаги:

``` r
sessionInfo()
```

    R version 4.5.1 (2025-06-13 ucrt)
    Platform: x86_64-w64-mingw32/x64
    Running under: Windows 10 x64 (build 19045)

    Matrix products: default
      LAPACK version 3.12.1

    locale:
    [1] LC_COLLATE=Russian_Russia.utf8  LC_CTYPE=Russian_Russia.utf8   
    [3] LC_MONETARY=Russian_Russia.utf8 LC_NUMERIC=C                   
    [5] LC_TIME=Russian_Russia.utf8    

    time zone: Europe/Moscow
    tzcode source: internal

    attached base packages:
    [1] stats     graphics  grDevices utils     datasets  methods   base     

    loaded via a namespace (and not attached):
     [1] compiler_4.5.1    fastmap_1.2.0     cli_3.6.5         tools_4.5.1      
     [5] htmltools_0.5.8.1 yaml_2.3.10       rmarkdown_2.30    knitr_1.50       
     [9] jsonlite_2.0.0    xfun_0.53         digest_0.6.37     rlang_1.1.6      
    [13] evaluate_1.0.5   

### Подключение пакета dplyr

``` r
options(repos = c(CRAN = "https://mirror.truenetwork.ru/CRAN/"))
install.packages("dplyr")  
```

    пакет 'dplyr' успешно распакован, MD5-суммы проверены

    Warning: не могу удалить прежнюю установку пакета 'dplyr'

    Warning in file.copy(savedcopy, lib, recursive = TRUE): проблема с копированием
    C:\Rstudio\R-4.5.1\library\00LOCK\dplyr\libs\x64\dplyr.dll в
    C:\Rstudio\R-4.5.1\library\dplyr\libs\x64\dplyr.dll: Permission denied

    Warning: восстановлен 'dplyr'


    Скачанные бинарные пакеты находятся в
        C:\Users\kifor\AppData\Local\Temp\Rtmpo7l9lh\downloaded_packages

``` r
library(dplyr)
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

### Получение количества строк в датафрейме

``` r
starwars %>% nrow()
```

    [1] 87

### Получение количества столбцов в датафрейме

``` r
starwars %>% ncol()
```

    [1] 14

### Просмотр структуры датафрейма

``` r
starwars %>% glimpse()
```

    Rows: 87
    Columns: 14
    $ name       <chr> "Luke Skywalker", "C-3PO", "R2-D2", "Darth Vader", "Leia Or…
    $ height     <int> 172, 167, 96, 202, 150, 178, 165, 97, 183, 182, 188, 180, 2…
    $ mass       <dbl> 77.0, 75.0, 32.0, 136.0, 49.0, 120.0, 75.0, 32.0, 84.0, 77.…
    $ hair_color <chr> "blond", NA, NA, "none", "brown", "brown, grey", "brown", N…
    $ skin_color <chr> "fair", "gold", "white, blue", "white", "light", "light", "…
    $ eye_color  <chr> "blue", "yellow", "red", "yellow", "brown", "blue", "blue",…
    $ birth_year <dbl> 19.0, 112.0, 33.0, 41.9, 19.0, 52.0, 47.0, NA, 24.0, 57.0, …
    $ sex        <chr> "male", "none", "none", "male", "female", "male", "female",…
    $ gender     <chr> "masculine", "masculine", "masculine", "masculine", "femini…
    $ homeworld  <chr> "Tatooine", "Tatooine", "Naboo", "Tatooine", "Alderaan", "T…
    $ species    <chr> "Human", "Droid", "Droid", "Human", "Human", "Human", "Huma…
    $ films      <list> <"A New Hope", "The Empire Strikes Back", "Return of the J…
    $ vehicles   <list> <"Snowspeeder", "Imperial Speeder Bike">, <>, <>, <>, "Imp…
    $ starships  <list> <"X-wing", "Imperial shuttle">, <>, <>, "TIE Advanced x1",…

### Подсчёт количества уникальных рас

``` r
starwars %>% distinct(species) %>% nrow()
```

    [1] 38

### Поиск самого высокого персонажа

``` r
starwars %>% filter(height == max(height, na.rm = TRUE))
```

    # A tibble: 1 × 14
      name      height  mass hair_color skin_color eye_color birth_year sex   gender
      <chr>      <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
    1 Yarael P…    264    NA none       white      yellow            NA male  mascu…
    # ℹ 5 more variables: homeworld <chr>, species <chr>, films <list>,
    #   vehicles <list>, starships <list>

### Поиск персонажей ниже 170 см

``` r
starwars %>% filter(height < 170)
```

    # A tibble: 22 × 14
       name     height  mass hair_color skin_color eye_color birth_year sex   gender
       <chr>     <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
     1 C-3PO       167    75 <NA>       gold       yellow           112 none  mascu…
     2 R2-D2        96    32 <NA>       white, bl… red               33 none  mascu…
     3 Leia Or…    150    49 brown      light      brown             19 fema… femin…
     4 Beru Wh…    165    75 brown      light      blue              47 fema… femin…
     5 R5-D4        97    32 <NA>       white, red red               NA none  mascu…
     6 Yoda         66    17 white      green      brown            896 male  mascu…
     7 Mon Mot…    150    NA auburn     fair       blue              48 fema… femin…
     8 Wicket …     88    20 brown      brown      brown              8 male  mascu…
     9 Nien Nu…    160    68 none       grey       black             NA male  mascu…
    10 Watto       137    NA black      blue, grey yellow            NA male  mascu…
    # ℹ 12 more rows
    # ℹ 5 more variables: homeworld <chr>, species <chr>, films <list>,
    #   vehicles <list>, starships <list>

### Расчёт индекса массы тела (ИМТ)

``` r
starwars %>% mutate(BMI = mass / (height/100)^2)
```

    # A tibble: 87 × 15
       name     height  mass hair_color skin_color eye_color birth_year sex   gender
       <chr>     <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
     1 Luke Sk…    172    77 blond      fair       blue            19   male  mascu…
     2 C-3PO       167    75 <NA>       gold       yellow         112   none  mascu…
     3 R2-D2        96    32 <NA>       white, bl… red             33   none  mascu…
     4 Darth V…    202   136 none       white      yellow          41.9 male  mascu…
     5 Leia Or…    150    49 brown      light      brown           19   fema… femin…
     6 Owen La…    178   120 brown, gr… light      blue            52   male  mascu…
     7 Beru Wh…    165    75 brown      light      blue            47   fema… femin…
     8 R5-D4        97    32 <NA>       white, red red             NA   none  mascu…
     9 Biggs D…    183    84 black      light      brown           24   male  mascu…
    10 Obi-Wan…    182    77 auburn, w… fair       blue-gray       57   male  mascu…
    # ℹ 77 more rows
    # ℹ 6 more variables: homeworld <chr>, species <chr>, films <list>,
    #   vehicles <list>, starships <list>, BMI <dbl>

### Поиск 10 самых “вытянутых” персонажей

``` r
starwars %>%
  mutate(stretch_ratio = mass / height) %>%
  arrange(desc(stretch_ratio)) %>%
  slice(1:10)
```

    # A tibble: 10 × 15
       name     height  mass hair_color skin_color eye_color birth_year sex   gender
       <chr>     <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> <chr> 
     1 Jabba D…    175  1358 <NA>       green-tan… orange         600   herm… mascu…
     2 Grievous    216   159 none       brown, wh… green, y…       NA   male  mascu…
     3 IG-88       200   140 none       metal      red             15   none  mascu…
     4 Owen La…    178   120 brown, gr… light      blue            52   male  mascu…
     5 Darth V…    202   136 none       white      yellow          41.9 male  mascu…
     6 Jek Ton…    180   110 brown      fair       blue            NA   <NA>  <NA>  
     7 Bossk       190   113 none       green      red             53   male  mascu…
     8 Tarfful     234   136 brown      brown      blue            NA   male  mascu…
     9 Dexter …    198   102 none       brown      yellow          NA   male  mascu…
    10 Chewbac…    228   112 brown      unknown    blue           200   male  mascu…
    # ℹ 6 more variables: homeworld <chr>, species <chr>, films <list>,
    #   vehicles <list>, starships <list>, stretch_ratio <dbl>

### Расчёт среднего возраста по расам

``` r
starwars %>%
  group_by(species) %>%
  summarise(mean_age = mean(birth_year, na.rm = TRUE))
```

    # A tibble: 38 × 2
       species   mean_age
       <chr>        <dbl>
     1 Aleena       NaN  
     2 Besalisk     NaN  
     3 Cerean        92  
     4 Chagrian     NaN  
     5 Clawdite     NaN  
     6 Droid         53.3
     7 Dug          NaN  
     8 Ewok           8  
     9 Geonosian    NaN  
    10 Gungan        52  
    # ℹ 28 more rows

### Определение самого распространённого цвета глаз

``` r
starwars %>%
  count(eye_color) %>%
  arrange(desc(n)) %>%
  slice(1)
```

    # A tibble: 1 × 2
      eye_color     n
      <chr>     <int>
    1 brown        21

### Расчёт средней длины имени по расам

``` r
starwars %>%
  mutate(name_length = nchar(name)) %>%
  group_by(species) %>%
  summarise(mean_name_length = mean(name_length, na.rm = TRUE))
```

    # A tibble: 38 × 2
       species   mean_name_length
       <chr>                <dbl>
     1 Aleena               12   
     2 Besalisk             15   
     3 Cerean               12   
     4 Chagrian             10   
     5 Clawdite             10   
     6 Droid                 4.83
     7 Dug                   7   
     8 Ewok                 21   
     9 Geonosian            17   
    10 Gungan               11.7 
    # ℹ 28 more rows

## Оценка результатов

В ходе практической работы были применены базовые навыки анализа данных,
полученные ранее, для обработки набора starwars с использованием пакета
dplyr

## Вывод

В результате работы были закреплены практические навыки применения
функций dplyr, таких как select(), filter(), mutate(), arrange(),
group_by() для анализа и трансформации данных
