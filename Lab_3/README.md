# Основы обработки данных с помощью R

## Цель работы

1. Развить практические навыки использования языка программирования R для обработки данных 
2. Закрепить знания базовых типов данных языка R 
3. Развить пркатические навыки использования функций обработки данных пакета dplyr – функции select(), filter(), mutate(), arrange(), group_by()
## Исходные данные

1. Программное обеспечение Windows 10
2. Rstudio Desktop
3. Интерпретатор языка R 4.4.1
## План

1. Установить программный пакет dplyr
2. Проанализировать набор данных
3. Ответить на вопросы
## Шаги

### Шаг 1: Получение данных
Подключим пакеты:

``` r
library(dplyr)
```

    Warning: пакет 'dplyr' был собран под R версии 4.4.2


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

``` r
library(nycflights13)
```

    Warning: пакет 'nycflights13' был собран под R версии 4.4.2

### Шаг 2: Ответы на вопросы

-   Сколько встроенных в пакет nycflights13 датафреймов?

``` r
ls("package:nycflights13") %>% length()
```

    [1] 5

-   Сколько строк в каждом датафрейме?

``` r
list(
  flights = nrow(flights),
  airlines = nrow(airlines),
  airports = nrow(airports),
  planes = nrow(planes),
  weather = nrow(weather))
```

    $flights
    [1] 336776

    $airlines
    [1] 16

    $airports
    [1] 1458

    $planes
    [1] 3322

    $weather
    [1] 26115

-   Сколько столбцов в каждом датафрейме?

``` r
list(
  flights = ncol(flights),
  airlines = ncol(airlines),
  airports = ncol(airports),
  planes = ncol(planes),
  weather = ncol(weather))
```

    $flights
    [1] 19

    $airlines
    [1] 2

    $airports
    [1] 8

    $planes
    [1] 9

    $weather
    [1] 15

-   Как просмотреть примерный вид датафрейма?

``` r
planes %>% glimpse()
```

    Rows: 3,322
    Columns: 9
    $ tailnum      <chr> "N10156", "N102UW", "N103US", "N104UW", "N10575", "N105UW…
    $ year         <int> 2004, 1998, 1999, 1999, 2002, 1999, 1999, 1999, 1999, 199…
    $ type         <chr> "Fixed wing multi engine", "Fixed wing multi engine", "Fi…
    $ manufacturer <chr> "EMBRAER", "AIRBUS INDUSTRIE", "AIRBUS INDUSTRIE", "AIRBU…
    $ model        <chr> "EMB-145XR", "A320-214", "A320-214", "A320-214", "EMB-145…
    $ engines      <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, …
    $ seats        <int> 55, 182, 182, 182, 55, 182, 182, 182, 182, 182, 55, 55, 5…
    $ speed        <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
    $ engine       <chr> "Turbo-fan", "Turbo-fan", "Turbo-fan", "Turbo-fan", "Turb…

-   Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных
    (представлено в наборах данных)?

``` r
airlines %>%
  select(carrier) %>%
  unique() %>%
  filter(!is.na(carrier)) %>%
  nrow()
```

    [1] 16

-   Сколько рейсов принял аэропорт John F Kennedy Intl в мае?

``` r
flights %>% filter(origin == "JFK", month == 5) %>% nrow()
```

    [1] 9397

-   Какой самый северный аэропорт?

``` r
airports %>% arrange(desc(lat)) %>% slice(1)
```

    # A tibble: 1 × 8
      faa   name                      lat   lon   alt    tz dst   tzone
      <chr> <chr>                   <dbl> <dbl> <dbl> <dbl> <chr> <chr>
    1 EEN   Dillant Hopkins Airport  72.3  42.9   149    -5 A     <NA> 

-   Какой аэропорт самый высокогорный (находится выше всех над уровнем
    моря)?

``` r
airports %>% arrange(desc(alt)) %>% slice(1)
```

    # A tibble: 1 × 8
      faa   name        lat   lon   alt    tz dst   tzone         
      <chr> <chr>     <dbl> <dbl> <dbl> <dbl> <chr> <chr>         
    1 TEX   Telluride  38.0 -108.  9078    -7 A     America/Denver

-   Какие бортовые номера у самых старых самолетов?

``` r
airports %>% arrange(desc(lat)) %>% slice(1)
```

    # A tibble: 1 × 8
      faa   name                      lat   lon   alt    tz dst   tzone
      <chr> <chr>                   <dbl> <dbl> <dbl> <dbl> <chr> <chr>
    1 EEN   Dillant Hopkins Airport  72.3  42.9   149    -5 A     <NA> 

-   Какая средняя температура воздуха была в сентябре в аэропорту John F
    Kennedy Intl (в градусах Цельсия).

``` r
weather %>% filter(origin == "JFK", month == 9) %>% summarise(avg_temp = mean((temp - 32) * 5 / 9, na.rm = TRUE))
```

    # A tibble: 1 × 1
      avg_temp
         <dbl>
    1     19.4

-   Самолеты какой авиакомпании совершили больше всего вылетов в июне?

``` r
b<- flights %>% filter(month == 6) %>% group_by(carrier) %>% summarise("coun"=n()) %>% arrange(desc(coun)) %>% slice(1) %>% select(carrier)
airlines %>% filter(carrier == b$carrier) %>% select(name)
```

    # A tibble: 1 × 1
      name                 
      <chr>                
    1 United Air Lines Inc.

-   Самолеты какой авиакомпании задерживались чаще других в 2013 году?

``` r
c<- flights %>% filter(arr_delay > 0 & year == 2013) %>% group_by(carrier) %>% summarise("coun"=n()) %>% arrange(desc(coun)) %>% slice(1) %>% select(carrier)
airlines %>% filter(carrier == c$carrier) %>% select(name)
```

    # A tibble: 1 × 1
      name                    
      <chr>                   
    1 ExpressJet Airlines Inc.
## Оценка результата

В результате были выполненны задачи с использованием набора данных nycflights13.
## Вывод

Были выполнены поставленные задачи и изучен набор данных nycflights13.
