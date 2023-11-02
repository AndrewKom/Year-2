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

1.  Подготовка данных

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
    ```

## Оценка результатов

Задача выполнена при помощи приложения RStudio, удалось развить
практические навыки использования языка R для обработки данных

## Вывод

В данной работе я смог исследовать подозрительную сетевую активность
внтуренней сети Доброй Организации. Исследовать файлы, восстановить
данные, подготовить их к анализу и дать ответы на поставленные вопросы
