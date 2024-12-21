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
```
* ##### Сколько строк в каждом датафрейме?
```
> list(flights = nrow(flights),airlines = nrow(airlines),airports = nrow(airports),planes = nrow(planes),weather = nrow(weather))
```
* ##### Сколько столбцов в каждом датафрейме?
```
> list(flights = ncol(flights),airlines = ncol(airlines),airports = ncol(airports),planes = ncol(planes),weather = ncol(weather))
```
* ##### Как просмотреть примерный вид датафрейма?
```
> flights %>% glimpse()
```
* ##### Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных (представлено в наборах данных)?
```
> flights %>% filter(!is.na(carrier)) %>% distinct(carrier) %>% nrow()
```
* ##### Сколько рейсов принял аэропорт John F Kennedy Intl в мае?
```
> flights %>% filter(origin == "JFK", month == 5) %>% nrow()
```
* ##### Какой самый северный аэропорт?
```
> airports %>% arrange(desc(lat)) %>% slice(1)
```
* ##### Какой аэропорт самый высокогорный (находится выше всех над уровнем моря)?
```
> airports %>% arrange(desc(alt)) %>% slice(1)
```
* ##### Какие бортовые номера у самых старых самолетов?
```
> planes %>% arrange(desc(year)) %>% select(tailnum)
```
* ##### Какая средняя температура воздуха была в сентябре в аэропорту John F Kennedy Intl (в градусах Цельсия)
```
> weather %>% filter(origin == "JFK", month == 9) %>% summarise(avg_temp = mean((temp - 32) * 5 / 9, na.rm = TRUE))
```
* ##### Самолеты какой авиакомпании совершили больше всего вылетов в июне?
```
> flights %>% filter(month == 6) %>% count(carrier, sort = TRUE)
```
* ##### Самолеты какой авиакомпании задерживались чаще других в 2013 году?
```
> flights %>% filter(dep_delay > 0 & year == 2013) %>% count(carrier, sort = TRUE)
```
## Оценка результата

В результате были выполненны задачи с использованием набора данных nycflights13.
## Вывод

Были выполнены поставленные задачи и изучен набор данных nycflights13.
