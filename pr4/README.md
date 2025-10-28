# Исследование метаданных DNS трафика
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

1\. Загрузка и распаковка архива
[dns.zip](https://dns.zip/ "https://dns.zip/")  
2. Импорт данных в R (например, с помощью readdelim() или readcsv())  
3. Назначение названий столбцов и описание структуры данных  
4. Преобразование типов данных (даты, IP, домены и пр.)  
5. Просмотр структуры с glimpse()  
6. Подсчёт количества уникальных участников информационного обмена  
7. Определение соотношения внутренних и внешних участников  
8. Выявление топ-10 самых активных участников сети  
9. Выявление топ-10 доменов по количеству обращений  
10. Расчёт интервалов между запросами к топ-10 доменам и их статистика
(summary())  
11. Поиск IP-адресов с признаками скрытого DNS-канала (периодичность
запросов)  
12. Получение геолокации и информации о провайдере для топ-10 доменов
через API [ip-api.com](https://ip-api.com/ "https://ip-api.com/").

## **Шаги:**

``` r
options(repos = c(CRAN = "https://mirror.truenetwork.ru/CRAN/"))
install.packages("readr")
```

    пакет 'readr' успешно распакован, MD5-суммы проверены

    Warning: не могу удалить прежнюю установку пакета 'readr'

    Warning in file.copy(savedcopy, lib, recursive = TRUE): проблема с копированием
    C:\Rstudio\R-4.5.1\library\00LOCK\readr\libs\x64\readr.dll в
    C:\Rstudio\R-4.5.1\library\readr\libs\x64\readr.dll: Permission denied

    Warning: восстановлен 'readr'


    Скачанные бинарные пакеты находятся в
        C:\Users\kifor\AppData\Local\Temp\Rtmp6NxzQF\downloaded_packages

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
        C:\Users\kifor\AppData\Local\Temp\Rtmp6NxzQF\downloaded_packages

``` r
install.packages("stringr")
```

    пакет 'stringr' успешно распакован, MD5-суммы проверены

    Скачанные бинарные пакеты находятся в
        C:\Users\kifor\AppData\Local\Temp\Rtmp6NxzQF\downloaded_packages

``` r
install.packages("httr")
```

    пакет 'httr' успешно распакован, MD5-суммы проверены

    Скачанные бинарные пакеты находятся в
        C:\Users\kifor\AppData\Local\Temp\Rtmp6NxzQF\downloaded_packages

``` r
install.packages("jsonlite")
```

    пакет 'jsonlite' успешно распакован, MD5-суммы проверены

    Warning: не могу удалить прежнюю установку пакета 'jsonlite'

    Warning in file.copy(savedcopy, lib, recursive = TRUE): проблема с копированием
    C:\Rstudio\R-4.5.1\library\00LOCK\jsonlite\libs\x64\jsonlite.dll в
    C:\Rstudio\R-4.5.1\library\jsonlite\libs\x64\jsonlite.dll: Permission denied

    Warning: восстановлен 'jsonlite'


    Скачанные бинарные пакеты находятся в
        C:\Users\kifor\AppData\Local\Temp\Rtmp6NxzQF\downloaded_packages

``` r
install.packages("tidyverse")
```

    пакет 'tidyverse' успешно распакован, MD5-суммы проверены

    Скачанные бинарные пакеты находятся в
        C:\Users\kifor\AppData\Local\Temp\Rtmp6NxzQF\downloaded_packages

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.1     ✔ stringr   1.5.2
    ✔ ggplot2   4.0.0     ✔ tibble    3.3.0
    ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ✔ purrr     1.1.0     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

\## Загрузка и распаковка архива
[dns.zip](https://dns.zip/ "https://dns.zip/")

``` r
download.file("https://storage.yandexcloud.net/dataset.ctfsec/dns.zip", "dns.zip")
unzip("dns.zip", exdir = "dns_data")
```

\## Импорт данных в R (например, с помощью readdelim() или readcsv())

``` r
dns_raw <- read_delim("dns_data/dns.log", delim = "\t", comment = "#", col_names = FALSE)
```

    Rows: 427935 Columns: 23
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: "\t"
    chr (13): X2, X3, X5, X7, X9, X10, X11, X12, X13, X14, X15, X21, X22
    dbl  (5): X1, X4, X6, X8, X20
    lgl  (5): X16, X17, X18, X19, X23

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

\## Назначение названий столбцов и описание структуры данных

``` r
column_names <- c(
  "timestamp", "uid", "source_ip", "source_port", "destination_ip", 
  "destination_port", "protocol", "transaction_id", "query", "qclass", 
  "qclass_name", "qtype", "qtype_name", "rcode", "rcode_name", 
  "AA", "TC", "RD", "RA", "Z", "answers", "TTLS", "rejected"
)

dns_data <- read_delim(
  "dns_data/dns.log",  # ← здесь путь к файлу напрямую
  delim = "\t",
  col_names = column_names,
  comment = "#",
  na = c("", "NA", "-"),
  trim_ws = TRUE,
  show_col_types = FALSE
) %>% as_tibble()

head(dns_data, 10)
```

    # A tibble: 10 × 23
         timestamp uid         source_ip source_port destination_ip destination_port
             <dbl> <chr>       <chr>           <dbl> <chr>                     <dbl>
     1 1331901006. CWGtK431H9… 192.168.…       45658 192.168.27.203              137
     2 1331901015. C36a282Jlj… 192.168.…         137 192.168.202.2…              137
     3 1331901016. C36a282Jlj… 192.168.…         137 192.168.202.2…              137
     4 1331901017. C36a282Jlj… 192.168.…         137 192.168.202.2…              137
     5 1331901006. C36a282Jlj… 192.168.…         137 192.168.202.2…              137
     6 1331901007. C36a282Jlj… 192.168.…         137 192.168.202.2…              137
     7 1331901007. C36a282Jlj… 192.168.…         137 192.168.202.2…              137
     8 1331901006. ClEZCt3GLk… 192.168.…         137 192.168.202.2…              137
     9 1331901007. ClEZCt3GLk… 192.168.…         137 192.168.202.2…              137
    10 1331901008. ClEZCt3GLk… 192.168.…         137 192.168.202.2…              137
    # ℹ 17 more variables: protocol <chr>, transaction_id <dbl>, query <chr>,
    #   qclass <dbl>, qclass_name <chr>, qtype <dbl>, qtype_name <chr>,
    #   rcode <dbl>, rcode_name <chr>, AA <lgl>, TC <lgl>, RD <lgl>, RA <lgl>,
    #   Z <dbl>, answers <chr>, TTLS <chr>, rejected <lgl>

\## Преобразование типов данных (даты, IP, домены и пр.)

``` r
library(dplyr)

dns_data <- dns_data %>%
  mutate(
    timestamp = as.POSIXct(timestamp, origin = "1970-01-01"),
    source_ip = as.character(source_ip),
    destination_ip = as.character(destination_ip),
    source_port = as.integer(source_port),
    destination_port = as.integer(destination_port),
    transaction_id = as.integer(transaction_id),
    query = tolower(as.character(query)),
    qclass = as.integer(qclass),
    qtype = as.integer(qtype),
    rcode = as.integer(rcode),
    AA = as.logical(AA),
    TC = as.logical(TC),
    RD = as.logical(RD),
    RA = as.logical(RA),
    Z = as.integer(Z),
    rejected = as.logical(rejected)
  )

glimpse(dns_data)
```

    Rows: 427,935
    Columns: 23
    $ timestamp        <dttm> 2012-03-16 16:30:05, 2012-03-16 16:30:15, 2012-03-16…
    $ uid              <chr> "CWGtK431H9XuaTN4fi", "C36a282Jljz7BsbGH", "C36a282Jl…
    $ source_ip        <chr> "192.168.202.100", "192.168.202.76", "192.168.202.76"…
    $ source_port      <int> 45658, 137, 137, 137, 137, 137, 137, 137, 137, 137, 1…
    $ destination_ip   <chr> "192.168.27.203", "192.168.202.255", "192.168.202.255…
    $ destination_port <int> 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 137…
    $ protocol         <chr> "udp", "udp", "udp", "udp", "udp", "udp", "udp", "udp…
    $ transaction_id   <int> 33008, 57402, 57402, 57402, 57398, 57398, 57398, 6218…
    $ query            <chr> "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\…
    $ qclass           <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    $ qclass_name      <chr> "C_INTERNET", "C_INTERNET", "C_INTERNET", "C_INTERNET…
    $ qtype            <int> 33, 32, 32, 32, 32, 32, 32, 32, 32, 32, 33, 33, 33, 1…
    $ qtype_name       <chr> "SRV", "NB", "NB", "NB", "NB", "NB", "NB", "NB", "NB"…
    $ rcode            <int> 0, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
    $ rcode_name       <chr> "NOERROR", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
    $ AA               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALS…
    $ TC               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALS…
    $ RD               <lgl> FALSE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE…
    $ RA               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALS…
    $ Z                <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 1, 1, 1,…
    $ answers          <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
    $ TTLS             <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
    $ rejected         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALS…

\## Подсчёт количества уникальных участников информационного обмена

``` r
participants <- dns_data %>%
  select(source_ip, destination_ip) %>%
  pivot_longer(cols = everything(), values_to = "ip") %>%
  distinct(ip)

n_participants <- nrow(participants)
print(n_participants)
```

    [1] 1359

\## Определение соотношения внутренних и внешних участников

``` r
participants <- participants %>%
  mutate(type = if_else(str_detect(ip, "^192\\.168\\."), "internal", "external"))

table(participants$type)
```


    external internal 
         121     1238 

\## Выявление топ-10 самых активных участников сети

``` r
top_active <- dns_data %>%
  count(source_ip, sort = TRUE) %>%
  slice_head(n = 10)

print(top_active)
```

    # A tibble: 10 × 2
       source_ip           n
       <chr>           <int>
     1 10.10.117.210   75943
     2 192.168.202.93  26522
     3 192.168.202.103 18121
     4 192.168.202.76  16978
     5 192.168.202.97  16176
     6 192.168.202.141 14967
     7 10.10.117.209   14222
     8 192.168.202.110 13372
     9 192.168.203.63  12148
    10 192.168.202.106 10784

\## Выявление топ-10 доменов по количеству обращений

``` r
top_domains <- dns_data %>%
  filter(!is.na(query)) %>%
  count(query, sort = TRUE) %>%
  slice_head(n = 10)

print(top_domains)
```

    # A tibble: 10 × 2
       query                                                                       n
       <chr>                                                                   <int>
     1 "teredo.ipv6.microsoft.com"                                             39273
     2 "tools.google.com"                                                      14057
     3 "www.apple.com"                                                         13390
     4 "time.apple.com"                                                        13109
     5 "safebrowsing.clients.google.com"                                       11658
     6 "wpad"                                                                  11429
     7 "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x… 10401
     8 "isatap"                                                                 9712
     9 "44.206.168.192.in-addr.arpa"                                            7248
    10 "hpe8aa67"                                                               6929

\## Расчёт интервалов между запросами к топ-10 доменам и их статистика
(summary())

``` r
dns_data <- dns_data %>% arrange(query, timestamp)

interval_stats <- top_domains$query %>%
  map_df(function(domain) {
    times <- dns_data %>%
      filter(query == domain) %>%
      arrange(timestamp) %>%
      pull(timestamp)
    
    intervals <- diff(times)
    
    tibble(
      domain = domain,
      summary = list(summary(as.numeric(intervals)))
    )
  })

print(interval_stats)
```

    # A tibble: 10 × 2
       domain                                                             summary   
       <chr>                                                              <list>    
     1 "teredo.ipv6.microsoft.com"                                        <smmryDfl>
     2 "tools.google.com"                                                 <smmryDfl>
     3 "www.apple.com"                                                    <smmryDfl>
     4 "time.apple.com"                                                   <smmryDfl>
     5 "safebrowsing.clients.google.com"                                  <smmryDfl>
     6 "wpad"                                                             <smmryDfl>
     7 "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x… <smmryDfl>
     8 "isatap"                                                           <smmryDfl>
     9 "44.206.168.192.in-addr.arpa"                                      <smmryDfl>
    10 "hpe8aa67"                                                         <smmryDfl>

\## Поиск IP-адресов с признаками скрытого DNS-канала (периодичность
запросов)

``` r
suspicious_ips <- dns_data %>%
  group_by(source_ip, query) %>%
  filter(n() > 10) %>%
  arrange(timestamp) %>%
  mutate(interval = as.numeric(difftime(timestamp, lag(timestamp), units = "secs"))) %>%
  summarise(sd_interval = sd(interval, na.rm = TRUE), .groups = "drop") %>%
  filter(sd_interval < 1)  # почти одинаковые интервалы

print(suspicious_ips)
```

    # A tibble: 118 × 3
       source_ip       query                         sd_interval
       <chr>           <chr>                               <dbl>
     1 10.10.117.210   "hq.h"                            0      
     2 10.10.117.210   "httphq.hec.net"                  0      
     3 10.10.117.210   "www.h"                           0      
     4 169.254.228.26  "tirani"                          0.350  
     5 192.168.0.3     "\\x01\\x02__msbrowse__\\x02"     0.269  
     6 192.168.0.3     "isatap"                          0.313  
     7 192.168.0.3     "tirani"                          0.350  
     8 192.168.202.100 "addons.mozilla.org"              0.00414
     9 192.168.202.100 "sb-ssl.google.com"               0.00507
    10 192.168.202.100 "tomcat.apache.org"               0.00488
    # ℹ 108 more rows

\## Получение геолокации и информации о провайдере для топ-10 доменов
через API [ip-api.com](https://ip-api.com/ "https://ip-api.com/").

``` r
library(httr)
library(jsonlite)
```


    Присоединяю пакет: 'jsonlite'

    Следующий объект скрыт от 'package:purrr':

        flatten

``` r
lookup_ip <- function(domain) {
  res <- GET(paste0("http://ip-api.com/json/", domain))
  fromJSON(content(res, "text", encoding = "UTF-8"))
}

geo_info <- top_domains$query %>%
  map_df(function(domain) {
    info <- tryCatch(lookup_ip(domain), error = function(e) NULL)
    if (!is.null(info) && info$status == "success") {
      tibble(
        domain = domain,
        country = info$country,
        city = info$city,
        isp = info$isp
      )
    } else {
      tibble(domain = domain, country = NA, city = NA, isp = NA)
    }
  })

print(geo_info)
```

    # A tibble: 10 × 4
       domain                                                    country city  isp  
       <chr>                                                     <chr>   <chr> <chr>
     1 "teredo.ipv6.microsoft.com"                               <NA>    <NA>  <NA> 
     2 "tools.google.com"                                        The Ne… Amst… Goog…
     3 "www.apple.com"                                           The Ne… Haar… Akam…
     4 "time.apple.com"                                          Nether… Amst… Appl…
     5 "safebrowsing.clients.google.com"                         The Ne… Amst… Goog…
     6 "wpad"                                                    <NA>    <NA>  <NA> 
     7 "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x0… <NA>    <NA>  <NA> 
     8 "isatap"                                                  <NA>    <NA>  <NA> 
     9 "44.206.168.192.in-addr.arpa"                             <NA>    <NA>  <NA> 
    10 "hpe8aa67"                                                <NA>    <NA>  <NA> 

## **Оценка результата**

В данной практической работе мы применили ранее полученные знания для
анализа сетевого трафика на основе DNS-логов. Были выполнены операции по
импорту, структурированию, преобразованию и исследованию данных с
использованием пакета dplyr.

## **Вывод**

Таким образом, мы закрепили навыки работы с функциями dplyr: select(),
filter(), mutate(), arrange(), group_by(), summarise(), count() и
join(), применяя их к реальным данным сетевой активности для выявления
участников, популярных доменов и потенциальных угроз.
