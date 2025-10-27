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

1.  Программное обеспечение ОС Windows 10 Pro
2.  RStudio
3.  Интерпретатор языка R 4.5.1

## План

1.  Установка и подключение пакета nycflights13  

2.  Определение количества встроенных датафреймов  

3.  Подсчёт количества строк в каждом датафрейме  

4.  Подсчёт количества столбцов в каждом датафрейме  

5.  Просмотр структуры датафрейма  

6.  Подсчёт количества уникальных компаний-перевозчиков  

7.  Подсчёт рейсов, принятых аэропортом John F Kennedy Intl в мае  

8.  Определение самого северного аэропорта

9.  Определение самого высокогорного аэропорта  

10. Поиск бортовых номеров самых старых самолётов  

11. Расчёт средней температуры в сентябре в аэропорту JFK (в °C)  

12. Самолеты какой авиакомпании совершили больше всего вылетов в июне?

13. ## Самолеты какой авиакомпании задерживались чаще других в 2013 году?

\## Установка и подключение пакета nycflights13

``` r
options(repos = c(CRAN = "https://mirror.truenetwork.ru/CRAN/"))
install.packages("nycflights13")
```

    пакет 'nycflights13' успешно распакован, MD5-суммы проверены

    Скачанные бинарные пакеты находятся в
        C:\Users\kifor\AppData\Local\Temp\Rtmp29pmDC\downloaded_packages

``` r
install.packages("dplyr")
```

    пакет 'dplyr' успешно распакован, MD5-суммы проверены

    Warning: не могу удалить прежнюю установку пакета 'dplyr'

    Warning in file.copy(savedcopy, lib, recursive = TRUE): проблема с копированием
    C:\Rstudio\R-4.5.1\library\00LOCK\dplyr\libs\x64\dplyr.dll в
    C:\Rstudio\R-4.5.1\library\dplyr\libs\x64\dplyr.dll: Permission denied

    Warning: восстановлен 'dplyr'


    Скачанные бинарные пакеты находятся в
        C:\Users\kifor\AppData\Local\Temp\Rtmp29pmDC\downloaded_packages

``` r
library(nycflights13)
library(dplyr)
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

\## Определение количества встроенных датафреймов

``` r
data_list <- data(package = "nycflights13")$results[, "Item"]
length(data_list)
```

    [1] 5

\## Подсчёт количества строк в каждом датафрейме

``` r
sapply(data_list, function(x) nrow(get(x, envir = asNamespace("nycflights13"))))
```

    airlines airports  flights   planes  weather 
          16     1458   336776     3322    26115 

\## Подсчёт количества столбцов в каждом датафрейме

``` r
sapply(data_list, function(x) ncol(get(x, envir = asNamespace("nycflights13"))))
```

    airlines airports  flights   planes  weather 
           2        8       19        9       15 

\## Просмотр структуры датафрейма

``` r
head(airlines)
```

    # A tibble: 6 × 2
      carrier name                    
      <chr>   <chr>                   
    1 9E      Endeavor Air Inc.       
    2 AA      American Airlines Inc.  
    3 AS      Alaska Airlines Inc.    
    4 B6      JetBlue Airways         
    5 DL      Delta Air Lines Inc.    
    6 EV      ExpressJet Airlines Inc.

\## Подсчёт количества уникальных компаний-перевозчиков

``` r
length(unique(airlines$carrier))
```

    [1] 16

\## Подсчёт рейсов, принятых аэропортом John F Kennedy Intl в мае

``` r
flights %>%
  filter(dest == "JFK", month == 5) %>%
  nrow()
```

    [1] 0

\## Определение самого северного аэропорта

``` r
airports %>%
  filter(alt == max(alt, na.rm = TRUE))
```

    # A tibble: 1 × 8
      faa   name        lat   lon   alt    tz dst   tzone         
      <chr> <chr>     <dbl> <dbl> <dbl> <dbl> <chr> <chr>         
    1 TEX   Telluride  38.0 -108.  9078    -7 A     America/Denver

\## Определение самого высокогорного аэропорта

``` r
airports %>%
  filter(lat == max(lat, na.rm = TRUE))
```

    # A tibble: 1 × 8
      faa   name                      lat   lon   alt    tz dst   tzone
      <chr> <chr>                   <dbl> <dbl> <dbl> <dbl> <chr> <chr>
    1 EEN   Dillant Hopkins Airport  72.3  42.9   149    -5 A     <NA> 

\## Поиск бортовых номеров самых старых самолётов

``` r
planes %>%
filter(!is.na(year)) %>%
arrange(year) %>%
head(10) %>%
select(year, tailnum)
```

    # A tibble: 10 × 2
        year tailnum
       <int> <chr>  
     1  1956 N381AA 
     2  1959 N201AA 
     3  1959 N567AA 
     4  1963 N378AA 
     5  1963 N575AA 
     6  1965 N14629 
     7  1967 N615AA 
     8  1968 N425AA 
     9  1972 N383AA 
    10  1973 N364AA 

\## Расчёт средней температуры в сентябре в аэропорту JFK (в °C)

``` r
weather %>%
  filter(origin == "JFK", month == 9) %>%
  summarise(mean_temp_C = mean((temp - 32) * 5/9, na.rm = TRUE))
```

    # A tibble: 1 × 1
      mean_temp_C
            <dbl>
    1        19.4

\## Самолеты какой авиакомпании совершили больше всего вылетов в июне?

``` r
flights %>%
  filter(month == 6) %>%
  count(carrier, sort = TRUE) %>%
  slice_max(n, n = 1) %>%
  left_join(airlines, by = "carrier") %>%
  pull(name)
```

    [1] "United Air Lines Inc."

\## Самолеты какой авиакомпании задерживались чаще других в 2013 году?

``` r
flights %>%
  filter(dep_delay > 0) %>%
  count(carrier, sort = TRUE) %>%
  left_join(airlines, by = "carrier")
```

    # A tibble: 16 × 3
       carrier     n name                       
       <chr>   <int> <chr>                      
     1 UA      27261 United Air Lines Inc.      
     2 EV      23139 ExpressJet Airlines Inc.   
     3 B6      21445 JetBlue Airways            
     4 DL      15241 Delta Air Lines Inc.       
     5 AA      10162 American Airlines Inc.     
     6 MQ       8031 Envoy Air                  
     7 9E       7063 Endeavor Air Inc.          
     8 WN       6558 Southwest Airlines Co.     
     9 US       4775 US Airways Inc.            
    10 VX       2225 Virgin America             
    11 FL       1654 AirTran Airways Corporation
    12 F9        341 Frontier Airlines Inc.     
    13 YV        233 Mesa Airlines Inc.         
    14 AS        226 Alaska Airlines Inc.       
    15 HA         69 Hawaiian Airlines Inc.     
    16 OO          9 SkyWest Airlines Inc.      

## Оценка результатов

В данной практической работе мы применили знания, полученные ранее, для
анализа набора данных nycflights13. Были выполнены запросы по структуре,
фильтрации, агрегации и объединению таблиц.

## Вывод

Таким образом, мы закрепили навыки работы с функциями пакета dplyr:
select(), filter(), mutate(), arrange(), group_by(), summarise() и
join(), применяя их к реальным данным авиаперевозок Нью-Йорка за 2013
год.

### 
