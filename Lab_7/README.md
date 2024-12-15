# Анализ данных сетевого трафика при помощи библиотеки Arrow
## Цель работы
1. Изучить возможности технологии Apache Arrow для обработки и анализ больших данных
2. Получить навыки применения Arrow совместно с языком программирования R
3. Получить навыки анализа метаинфомации о сетевом трафике
4. Получить навыки применения облачных технологий хранения, подготовки и анализа данных: Yandex Object Storage, Rstudio Server.

## Исходные данные
1. Программное обеспечение macOs
2. Rstudio Desktop
3. Интерпретатор языка R 4.2
4. Сервис Yandex DataLens
5. Apache Arrow
## Задание
Используя язык программирования R, библиотеку arrow и облачную IDE Rstudio Server, развернутую в Yandex Cloud, выполнить задания и составить отчет.
## План
1. Выполним практическое задание
2. Составим отчет
## Шаги
### Импорт данных
1. Скачиваем файл tm_data.pqt по [ссылке](https://storage.yandexcloud.net/arrow-datasets/tm_data.pqt)
2. Импортируем данные
```
> df <- arrow::open_dataset(sources = "tm_data.pqt", format = "parquet")
```
```
> glimpse(df)
```
```
FileSystemDataset with 1 Parquet file
105,747,730 rows x 5 columns
$ timestamp <double> 1.578326e+12, 1.578326e+12, 1.578326e+12…
$ src       <string> "13.43.52.51", "16.79.101.100", "18.43.1…
$ dst       <string> "18.70.112.62", "12.48.65.39", "14.51.30…
$ port       <int32> 40, 92, 27, 57, 115, 92, 65, 123, 79, 72…
$ bytes      <int32> 57354, 11895, 898, 7496, 20979, 8620, 46…
Call `print()` for full schema details
```
### Найдите утечку данных из Вашей сети
```
> task1 <- df %>% filter(str_detect(src, "^12.") | str_detect(src, "^13.") | str_detect(src, "^14."))  %>% filter(!str_detect(dst, "^12.") & !str_detect(dst, "^13.") & !str_detect(dst, "^14."))  %>% group_by(src) %>% summarise("sum" = sum(bytes)) %>% arrange(desc(sum)) %>% head(1) %>% select(src) 
> task1 %>% collect()
```
```
# A tibble: 1 × 1
  src         
  <chr>       
1 13.37.84.125
```
### Найдите утечку данных 2
```
> task21 <- df %>% select(timestamp, src, dst, bytes) %>% mutate(trafic = (str_detect(src, "^((12|13|14)\\.)") & !str_detect(dst, "^((12|13|14)\\.)")),time = hour(as_datetime(timestamp/1000))) %>% filter(trafic == TRUE, time >= 0 & time <= 24) %>% group_by(time) %>% summarise(trafictime = n()) %>% arrange(desc(trafictime))
> task21 %>% collect()
```
```
# A tibble: 24 × 2
    time trafictime
   <int>      <int>
 1    16    4490576
 2    22    4489703
 3    18    4489386
 4    23    4488093
 5    19    4487345
 6    21    4487109
 7    17    4483578
 8    20    4482712
 9    13     169617
10     7     169241
```
```
> task22 <- df %>% mutate(time = hour(as_datetime(timestamp/1000))) %>% filter(!str_detect(src, "^13.37.84.125")) %>% filter(str_detect(src, "^12.") | str_detect(src, "^13.") | str_detect(src, "^14."))  %>% filter(!str_detect(dst, "^12.") | !str_detect(dst, "^13.") | !str_detect(dst, "^14."))  %>% filter(time >= 1 & time <= 15) %>%  group_by(src) %>% summarise("sum" = sum(bytes)) %>% arrange(desc(sum)) %>% head(1)%>% select(src) 
> task22 %>%collect()
```
```
# A tibble: 1 × 1
  src        
  <chr>      
1 12.55.77.96
```
### Найдите утечку данных 3
```
> task31 <- df %>% filter(!str_detect(src, "^13.37.84.125")) %>% filter(!str_detect(src, "^12.55.77.96")) %>%filter(str_detect(src, "^12.") | str_detect(src, "^13.") | str_detect(src, "^14."))  %>% filter(!str_detect(dst, "^12.") & !str_detect(dst, "^13.") & !str_detect(dst,"^14."))%>% select(src, bytes, port)
> task31 %>% group_by(port) %>% summarise("mean"=mean(bytes), "max"=max(bytes), "sum" = sum(bytes)) %>% mutate("diff"= max-mean)  %>% filter(diff!=0) %>% arrange(desc(diff)) %>% head(1) %>%collect()
```
```
# A tibble: 1 × 5
   port   mean    max         sum    diff
  <int>  <dbl>  <int>     <int64>   <dbl>
1    37 35090. 209402 32136394510 174312.
```
```
> task32 <- task31  %>% filter(port==37) %>% group_by(src) %>% summarise("mean"=mean(bytes)) %>% arrange(desc(mean)) %>% head(1) %>% select(src)
> task32 %>% collect()
```
```
# A tibble: 1 × 1
  src         
  <chr>       
1 14.31.107.42
```
### Оценка результата
Был проанализирован пакет tm_data и выполнены все задания
### Вывод
Мы научились использовать библиотеку Apache Arrow для анализа данных
