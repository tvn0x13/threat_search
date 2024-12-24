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

1. Установка dplyr
```
install.packages("dplyr")
```
2. Выполнение заданий:
* Сколько строк в датафрейме?
```
> starwars %>% nrow()
```
* Сколько столбцов в датафрейме? 
```
> starwars %>% ncol()
```
* Просмотр примернового вида датафрейма
```
> starwars %>% glimpse()
```
* Количество уникальные расы персонажей
```
> length(unique(starwars$species))
```
* Самый высокий персонаж
```
> starwars %>% arrange(desc(height)) %>% slice_head(n = 1)
```
* Все персонажи ниже 170
```
> starwars %>% filter(!is.na(height) & height < 170) %>% select(name,height)
```
* Индекс массы тела всех персонажей
```
> starwars %>% filter(!is.na(mass) & !is.na(height)) %>% mutate(bmi = mass / (height/100)^2) %>% select(name,bmi)
```
* 10 самых вытянутых персонажей. Вытянутость = масса / рост
```
> starwars %>% filter(!is.na(mass) & !is.na(height)) %>% mutate(stretch = mass / height) %>% arrange(desc(stretch)) %>% slice(1:10) %>% select(name,stretch)
```
* Средний возраст персонажей каждой расы вселенной Звездных войн
```
> starwars <- starwars %>%  mutate(age = 2024 - birth_year)
```
```
> average_age <- starwars %>% group_by(species) %>% summarise(average_age = mean(age, na.rm = TRUE))
```
```
> average_age
```
* Самый распространенный цвет глаз
```
> eye_color <- starwars %>% group_by(eye_color) %>% summarize(count = n()) %>% arrange(desc(count)) %>% slice_head(n = 1)
```
```
> eye_color
```
* Средняя длина имени в каждой расе вселенной Звездных войн
```
> average_name_length <- starwars %>% mutate(name_length = nchar(name)) %>% group_by(species) %>%   summarize(avg_length = mean(name_length, na.rm = TRUE))
```
```
> average_name_length
```
## Оценка результата

В результате работы были выполнены задания по набору данных starwars.

## Вывод

Были изучены функции из библиотеки dplyr и выполнены поставленные задачи.
