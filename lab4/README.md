Практическая работа 4
================

## Цель работы

1.  Развить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R
3.  Закрепить навыки исследования метаданных DNS трафика

## Исходные данные

1.  ОС Windows
2.  tidyverse
3.  RStudio

## План

1.  Используем RStudio
2.  Исследуете подозрительную сетевую активность во внутренней сети
    Доброй Организации
3.  Создать отчет

## Описание шагов:

1.  *Воспользуемся RStudio*

2.  Скачиваем пакет tidyverse

3.  Подготовить данные

4.  Проанализировать подозрительную сетевую активность

5.  Обогащение данных

Работает с пакетом tidyverse

``` r
library(tidyverse)
```

    Warning: пакет 'tidyverse' был собран под R версии 4.2.3

    Warning: пакет 'ggplot2' был собран под R версии 4.2.3

    Warning: пакет 'tibble' был собран под R версии 4.2.3

    Warning: пакет 'tidyr' был собран под R версии 4.2.3

    Warning: пакет 'readr' был собран под R версии 4.2.3

    Warning: пакет 'purrr' был собран под R версии 4.2.3

    Warning: пакет 'dplyr' был собран под R версии 4.2.3

    Warning: пакет 'stringr' был собран под R версии 4.2.3

    Warning: пакет 'forcats' был собран под R версии 4.2.3

    Warning: пакет 'lubridate' был собран под R версии 4.2.3

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(readr)
```

### **Подготовка данных**

Импортируйте данные DNS

Добавьте пропущенные данные о структуре данных (назначении столбцов)
Преобразуйте данные в столбцах в нужный формат

``` r
DNS_name <- read.csv(file='header.csv')
DNS_name[3, "Field"] <- "id_orig_h"
DNS_name[4, "Field"] <- "id_orig_p"
DNS_name
```

              Field       Type
    1           ts       time 
    2          uid      string
    3     id_orig_h     recor 
    4     id_orig_p         d 
    5        proto       proto
    6     trans_id       count
    7        query      string
    8       qclass       count
    9  qclass_name      string
    10       qtype       count
    11  qtype_name      string
    12       rcode       count
    13  rcode_name      string
    14          QR       bool 
    15          AA       bool 
    16       TC RD  bool bool 
    17          RA       bool 
    18           Z       count
    19     answers      vector
    20        TTLs      vector
    21    rejected       bool 
                                                                                           Description
    1                                                                    Timestamp of the DNS request 
    2                                                                     Unique id of the connection 
    3                                                ID record with orig/resp host/port. See conn.log 
    4                                                                                                 
    5                                                        Protocol of DNS transaction – TCP or UDP 
    6                                       16 bit identifier assigned by DNS client; responses match 
    7                                                                Domain name subject of the query 
    8                                                                Value specifying the query class 
    9                                           Descriptive name of the query class (e.g. C_INTERNET) 
    10                                                                Value specifying the query type 
    11                                                     Name of the query type (e.g. A, AAAA, PTR) 
    12                                                        Response code value in the DNS response 
    13                                 Descriptive name of the response code (e.g. NOERROR, NXDOMAIN) 
    14                                        Was this a query or a response? T = response, F = query 
    15                                    Authoritative Answer. T = server is authoritative for query 
    16 Truncation. T = message was truncated Recursion Desired. T = request recursive lookup of query 
    17                                     Recursion Available. T = server supports recursive queries 
    18                                      Reserved field, should be zero in all queries & responses 
    19                                           List of resource descriptions in answer to the query 
    20                                                               Caching intervals of the answers 
    21                                               Whether the DNS query was rejected by the server 

2 Преобразуйте данные в столбцах в нужный формат

``` r
Field <- DNS_name %>% select(Field)
Field <- pull(Field)
Field <- append(Field,"id_resp_h",4)
Field <- append(Field,"id_resp_p",5)
Field
```

     [1] "ts "          "uid "         "id_orig_h"    "id_orig_p"    "id_resp_h"   
     [6] "id_resp_p"    "proto "       "trans_id "    "query "       "qclass "     
    [11] "qclass_name " "qtype "       "qtype_name "  "rcode "       "rcode_name " 
    [16] "QR "          "AA "          "TC RD "       "RA "          "Z "          
    [21] "answers "     "TTLs "        "rejected "   

``` r
logs <- read.csv(file = "dns.log", sep="\t",col.names = Field)

logs$ts. <- as.POSIXct(logs$ts., origin="1970-01-01")

logs %>% glimpse()
```

    Rows: 427,934
    Columns: 23
    $ ts.          <dttm> 2012-03-16 16:30:15, 2012-03-16 16:30:15, 2012-03-16 16:…
    $ uid.         <chr> "C36a282Jljz7BsbGH", "C36a282Jljz7BsbGH", "C36a282Jljz7Bs…
    $ id_orig_h    <chr> "192.168.202.76", "192.168.202.76", "192.168.202.76", "19…
    $ id_orig_p    <int> 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 13…
    $ id_resp_h    <chr> "192.168.202.255", "192.168.202.255", "192.168.202.255", …
    $ id_resp_p    <int> 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 13…
    $ proto.       <chr> "udp", "udp", "udp", "udp", "udp", "udp", "udp", "udp", "…
    $ trans_id.    <int> 57402, 57402, 57402, 57398, 57398, 57398, 62187, 62187, 6…
    $ query.       <chr> "HPE8AA67", "HPE8AA67", "HPE8AA67", "WPAD", "WPAD", "WPAD…
    $ qclass.      <chr> "1", "1", "1", "1", "1", "1", "1", "1", "1", "1", "1", "1…
    $ qclass_name. <chr> "C_INTERNET", "C_INTERNET", "C_INTERNET", "C_INTERNET", "…
    $ qtype.       <chr> "32", "32", "32", "32", "32", "32", "32", "32", "32", "33…
    $ qtype_name.  <chr> "NB", "NB", "NB", "NB", "NB", "NB", "NB", "NB", "NB", "SR…
    $ rcode.       <chr> "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-…
    $ rcode_name.  <chr> "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-…
    $ QR.          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, F…
    $ AA.          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, F…
    $ TC.RD.       <lgl> TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, FAL…
    $ RA.          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, F…
    $ Z.           <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 1, 1, 1, 1, 0, …
    $ answers.     <chr> "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-…
    $ TTLs.        <chr> "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-…
    $ rejected.    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, F…

### **Анализ**

1 Сколько участников информационного обмена в сети Доброй Организации?

``` r
df <- data.frame(a = c(logs[,"id_orig_h"], logs[,"id_resp_h"]))
un <- unique(df$a)
length(un)
```

    [1] 1359

2 Какое соотношение участников обмена внутри сети и участников обращений
к внешним ресурсам?

``` r
ip<- c("192.168.", "10.", "100.([6-9]|1[0-1][0-9]|12[0-7]).", "172.((1[6-9])|(2[0-9])|(3[0-1])).")
ips <- un[grep(paste(ip, collapse = "|"), un)]
internal <- sum(un %in% ips)
external <- length(un) - internal
ratio <- internal / external
ratio
```

    [1] 15.57317

3 Найдите топ-10 участников сети, проявляющих наибольшую сетевую
активность

``` r
df %>%  group_by(a=a)  %>%  summarise(active=n()) %>% arrange(desc(active)) %>% slice(1:10)
```

    # A tibble: 10 × 2
       a               active
       <chr>            <int>
     1 192.168.207.4   266627
     2 10.10.117.210    75943
     3 192.168.202.255  68720
     4 192.168.202.93   26522
     5 172.19.1.100     25481
     6 192.168.202.103  18121
     7 192.168.202.76   16978
     8 192.168.202.97   16176
     9 192.168.202.141  14976
    10 192.168.202.110  14493

4 Найдите топ-10 доменов, к которым обращаются пользователи сети и
соответственное количество обращений

``` r
top_10 <- logs %>%  group_by(domain = tolower(query.))  %>%  summarise(active=n()) %>% arrange(desc(active)) %>% slice(1:10)
top_10
```

    # A tibble: 10 × 2
       domain                                                                 active
       <chr>                                                                   <int>
     1 "teredo.ipv6.microsoft.com"                                             39273
     2 "tools.google.com"                                                      14057
     3 "www.apple.com"                                                         13390
     4 "time.apple.com"                                                        13109
     5 "safebrowsing.clients.google.com"                                       11658
     6 "wpad"                                                                  11429
     7 "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\…  10400
     8 "isatap"                                                                 9712
     9 "44.206.168.192.in-addr.arpa"                                            7248
    10 "hpe8aa67"                                                               6929

5 Определите базовые статистические характеристики (функция summary())
интервала времени между последовательным обращениями к топ-10 доменам.

``` r
sum <- logs %>% filter(tolower(query.) %in% top_10$domain) %>% arrange(ts.)
time <- diff(sum$ts.)
summary(time)
```

      Length    Class     Mode 
      137204 difftime  numeric 

6 Часто вредоносное программное обеспечение использует DNS канал в
качестве канала управления, периодически отправляя запросы на
подконтрольный злоумышленникам DNS сервер. По периодическим запросам на
один и тот же домен можно выявить скрытый DNS канал. Есть ли такие IP
адреса в исследуемом датасете?

``` r
DNS <- logs %>% group_by(ip = tolower(id_orig_h), domain = tolower(query.)) %>% summarise(request = n(), .groups = 'drop') %>% filter(request > 1700)
DNS_top <- unique(DNS$ip) 
DNS_top 
```

     [1] "10.10.117.209"   "10.10.117.210"   "192.168.202.102" "192.168.202.103"
     [5] "192.168.202.106" "192.168.202.113" "192.168.202.141" "192.168.202.75" 
     [9] "192.168.202.76"  "192.168.202.83"  "192.168.202.84"  "192.168.202.85" 
    [13] "192.168.202.87"  "192.168.202.93"  "192.168.202.97"  "192.168.203.63" 
    [17] "192.168.204.60"  "192.168.21.103" 

### **Обогащение данных**

Определите местоположение (страну, город) и организацию-провайдера для
топ-10 доменов. Для этого можно использовать сторонние сервисы
(https://2ip.ru/whois/#result-anchor)

*tools.google.com*

ip: 142.250.185.206

Хост: fra16s52-in-f14.1e100.net

Город: Моунтайн-Вью

Страна: США

ip диапазон: 142.250.0.0 - 142.251.255.255

Название провайдера: Google LLC

*www.apple.com*

ip: 23.40.25.24

Город: Santa Clara

Страна: США

Название провайдера: Akamai Technologies, Inc.

*time.apple.com*

ip: 17.253.52.253

Хост: ntp.euro.apple.com

Город: Нью-Йорк

Страна: США

ip диапазон: 17.0.0.0 - 17.255.255.255

Название провайдера: Apple Inc.

*safebrowsing.clients.google.com*

ip: 142.250.185.78

Хост: fra16s48-in-f14.1e100.net

Город: Моунтайн-Вью

Страна: США

ip диапазон: 142.250.0.0 - 142.251.255.255

Название провайдера: Google LLC

## Оценка результатов

Задача выполнена при помощи приложения RStudio, удалось развить
практические навыки использования языка R для обработки данных

## Вывод

В данной работе я смог исследовать подозрительную сетевую активность
внтуренней сети Доброй Организации. Исследовать файлы, восстановить
данные, подготовить их к анализу и дать ответы на поставленные вопросы
