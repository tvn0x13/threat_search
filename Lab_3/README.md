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

#### 1. Установка пакета nycflights13
```
install.packages('nycflights13')
```
#### 2. Подключение библиотеки nycflights13 и dplyr
```
library(nycflights13)
library(dplyr)
```
#### 3. Выполнение заданий

* ##### Сколько встроенных в пакет nycflights13 датафреймов?
```
> length(data(package="nycflights13")$results[, "Item"])
[1] 5
```
* ##### Сколько строк в каждом датафрейме?
```
> list(flights = nrow(flights),airlines = nrow(airlines),airports = nrow(airports),planes = nrow(planes),weather = nrow(weather))
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
```
* ##### Сколько столбцов в каждом датафрейме?
```
> list(flights = ncol(flights),airlines = ncol(airlines),airports = ncol(airports),planes = ncol(planes),weather = ncol(weather))
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
```
* ##### Как просмотреть примерный вид датафрейма?
```
> flights %>% glimpse()
Rows: 336,776
Columns: 19
$ year           <int> 2013, 2013, 2013, 2013, 2013, 2013, 201…
$ month          <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
$ day            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
$ dep_time       <int> 517, 533, 542, 544, 554, 554, 555, 557,…
$ sched_dep_time <int> 515, 529, 540, 545, 600, 558, 600, 600,…
$ dep_delay      <dbl> 2, 4, 2, -1, -6, -4, -5, -3, -3, -2, -2…
$ arr_time       <int> 830, 850, 923, 1004, 812, 740, 913, 709…
$ sched_arr_time <int> 819, 830, 850, 1022, 837, 728, 854, 723…
$ arr_delay      <dbl> 11, 20, 33, -18, -25, 12, 19, -14, -8, …
$ carrier        <chr> "UA", "UA", "AA", "B6", "DL", "UA", "B6…
$ flight         <int> 1545, 1714, 1141, 725, 461, 1696, 507, …
$ tailnum        <chr> "N14228", "N24211", "N619AA", "N804JB",…
$ origin         <chr> "EWR", "LGA", "JFK", "JFK", "LGA", "EWR…
$ dest           <chr> "IAH", "IAH", "MIA", "BQN", "ATL", "ORD…
$ air_time       <dbl> 227, 227, 160, 183, 116, 150, 158, 53, …
$ distance       <dbl> 1400, 1416, 1089, 1576, 762, 719, 1065,…
$ hour           <dbl> 5, 5, 5, 5, 6, 5, 6, 6, 6, 6, 6, 6, 6, …
$ minute         <dbl> 15, 29, 40, 45, 0, 58, 0, 0, 0, 0, 0, 0…
$ time_hour      <dttm> 2013-01-01 05:00:00, 2013-01-01 05:00:…
```
* ##### Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных (представлено в наборах данных)?
```
> flights %>% filter(!is.na(carrier)) %>% distinct(carrier) %>% nrow()
[1] 16
```
* ##### Сколько рейсов принял аэропорт John F Kennedy Intl в мае?
```
> flights %>% filter(origin == "JFK", month == 5) %>% nrow()
[1] 9397
```
* ##### Какой самый северный аэропорт?
```
> airports %>% arrange(desc(lat)) %>% slice(1)
# A tibble: 1 × 8
  faa   name                   lat   lon   alt    tz dst   tzone
  <chr> <chr>                <dbl> <dbl> <dbl> <dbl> <chr> <chr>
1 EEN   Dillant Hopkins Air…  72.3  42.9   149    -5 A     NA
```
* ##### Какой аэропорт самый высокогорный (находится выше всех над уровнем моря)?
```
> airports %>% arrange(desc(alt)) %>% slice(1)
# A tibble: 1 × 8
  faa   name        lat   lon   alt    tz dst   tzone         
  <chr> <chr>     <dbl> <dbl> <dbl> <dbl> <chr> <chr>         
1 TEX   Telluride  38.0 -108.  9078    -7 A     America/Denver
```
* ##### Какие бортовые номера у самых старых самолетов?
```
> planes %>% arrange(desc(year)) %>% select(tailnum)
# A tibble: 3,322 × 1
   tailnum
   <chr>  
 1 N150UW 
 2 N151UW 
 3 N152UW 
 4 N153UW 
 5 N154UW 
 6 N155UW 
 7 N156UW 
 8 N157UW 
 9 N198UW 
10 N199UW
```
* ##### Какая средняя температура воздуха была в сентябре в аэропорту John F Kennedy Intl (в градусах Цельсия)
```
> weather %>% filter(origin == "JFK", month == 9) %>% summarise(avg_temp = mean((temp - 32) * 5 / 9, na.rm = TRUE))
# A tibble: 1 × 1
  avg_temp
     <dbl>
1     19.4
```
* ##### Самолеты какой авиакомпании совершили больше всего вылетов в июне?
```
> flights %>% filter(month == 6) %>% count(carrier, sort = TRUE)
# A tibble: 16 × 2
   carrier     n
   <chr>   <int>
 1 UA       4975
 2 B6       4622
 3 EV       4456
 4 DL       4126
 5 AA       2757
 6 MQ       2178
 7 US       1736
 8 9E       1437
 9 WN       1028
10 VX        480
11 FL        252
12 AS         60
13 F9         55
14 YV         49
15 HA         30
16 OO          2
```
* ##### Самолеты какой авиакомпании задерживались чаще других в 2013 году?
```
> flights %>% filter(dep_delay > 0 & year == 2013) %>% count(carrier, sort = TRUE)
# A tibble: 16 × 2
   carrier     n
   <chr>   <int>
 1 UA      27261
 2 EV      23139
 3 B6      21445
 4 DL      15241
 5 AA      10162
 6 MQ       8031
 7 9E       7063
 8 WN       6558
 9 US       4775
10 VX       2225
11 FL       1654
12 F9        341
13 YV        233
14 AS        226
15 HA         69
16 OO          9
```
## Оценка результата

В результате были выполненны задачи с использованием набора данных nycflights13.
## Вывод

Были выполнены поставленные задачи и изучен набор данных nycflights13.
