# Исследование информации о состоянии беспроводных сетей
## Цель работы

1. Получить знания о методах исследования радиоэлектронной обстановки. 
2. Составить представление о механизмах работы Wi-Fi сетей на канальном и сетевом уровне модели OSI. 
3. Зекрепить практические навыки использования языка программирования R для обработки данных.
4. Закрепить знания основных функций обработки данных экосистемы tidyverse языка R.
## Исходные данные
1. Программное обеспечение Windows 10
2. RStudio Desktop
3. .csv данные P2_wifi_data
## План
1. Импортировать csv файл
2. Подготовить данные
3. Решить поставленные задачи
## Шаги
### 1. Подготовка данных
#### Импорт данных из .csv файла
```
> library(tidyverse)
> library(dplyr)
> library(readr)
> library(knitr)
> dataset1 <- read.csv("P2_wifi_data.csv", nrows = 167)
> dataset2 <- read.csv("P2_wifi_data.csv", skip = 169)
```
#### Структурирование
```
df1<df1%>%mutate_at(vars(BSSID,Privacy,Cipher,Authentication,LAN.IP,ESSID),trimws)%>%mutate_at(vars(BSSID,Privacy,Cipher,Authentication,LAN.IP,ESSID),na_if,"")

df1$First.time.seen<-as.POSIXct(df1$First.time.seen,format="%Y-%m-%d %H:%M:%S")

df1$Last.time.seen<-as.POSIXct(df1$Last.time.seen,format="%Y-%m-%d %H:%M:%S")

df2<df2%>%mutate_at(vars(Station.MAC,BSSID,Probed.ESSIDs),trimws)%>%mutate_at(vars(Station.MAC,BSSID,Probed.ESSIDs),na_if,"")

df2$First.time.seen<-as.POSIXct(df2$First.time.seen,format = "%Y-%m-%d %H:%M:%S")

df2$Last.time.seen<-as.POSIXct(df2$Last.time.seen,format = "%Y-%m-%d %H:%M:%S")
```
#### Просмотр структуры данных
```
> glimpse(df1)
Rows: 167
Columns: 15
$ BSSID           <chr> "BE:F1:71:D5:17:8B", "6E:C7:EC:16:DA:1…
$ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13…
$ Last.time.seen  <dttm> 2023-07-28 11:50:50, 2023-07-28 11:55…
$ channel         <int> 1, 1, 1, 7, 6, 6, 11, 11, 11, 1, 6, 14…
$ Speed           <int> 195, 130, 360, 360, 130, 130, 195, 130…
$ Privacy         <chr> "WPA2", "WPA2", "WPA2", "WPA2", "WPA2"…
$ Cipher          <chr> "CCMP", "CCMP", "CCMP", "CCMP", "CCMP"…
$ Authentication  <chr> "PSK", "PSK", "PSK", "PSK", "PSK", NA,…
$ Power           <int> -30, -30, -68, -37, -57, -63, -27, -38…
$ X..beacons      <int> 846, 750, 694, 510, 647, 251, 1647, 12…
$ X..IV           <int> 504, 116, 26, 21, 6, 3430, 80, 11, 0, …
$ LAN.IP          <chr> "0.  0.  0.  0", "0.  0.  0.  0", "0. …
$ ID.length       <int> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, …
$ ESSID           <chr> "C322U13 3965", "Cnet", "KC", "POCO X5…
$ Key             <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
```
```
> glimpse(df2)
Rows: 12,269
Columns: 7
$ Station.MAC     <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E…
$ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13…
$ Last.time.seen  <dttm> 2023-07-28 10:59:44, 2023-07-28 09:13…
$ Power           <chr> " -33", " -65", " -39", " -61", " -53"…
$ X..packets      <chr> "      858", "        4", "      432",…
$ BSSID           <chr> "BE:F1:71:D5:17:8B", "(not associated)…
$ Probed.ESSIDs   <chr> "C322U13 3965", "IT2 Wireless", "C322U…
```
### 2. Анализ точки доступа
#### Определить небезопасные точки доступа (без шифрования – OPN)
```
> unsafe_points<-df1 %>%filter(Privacy=='OPN')%>%select(BSSID)%>%unique()
> unsafe_points%>%select(BSSID)
               BSSID
1  E8:28:C1:DC:B2:52
2  E8:28:C1:DC:B2:50
3  E8:28:C1:DC:B2:51
4  E8:28:C1:DC:FF:F2
5  00:25:00:FF:94:73
6  E8:28:C1:DD:04:52
7  E8:28:C1:DE:74:31
8  E8:28:C1:DE:74:32
9  E8:28:C1:DC:C8:32
10 E8:28:C1:DD:04:50
11 E8:28:C1:DD:04:51
12 E8:28:C1:DC:C8:30
13 E8:28:C1:DE:74:30
14 E0:D9:E3:48:FF:D2
15 E8:28:C1:DC:B2:41
16 E8:28:C1:DC:B2:40
17 00:26:99:F2:7A:E0
18 E8:28:C1:DC:B2:42
19 E8:28:C1:DD:04:40
20 E8:28:C1:DD:04:41
21 E8:28:C1:DE:47:D2
22 02:BC:15:7E:D5:DC
23 E8:28:C1:DC:C6:B1
24 E8:28:C1:DD:04:42
25 E8:28:C1:DC:C8:31
26 E8:28:C1:DE:47:D1
27 00:AB:0A:00:10:10
28 E8:28:C1:DC:C6:B0
29 E8:28:C1:DC:C6:B2
30 E8:28:C1:DC:BD:50
31 E8:28:C1:DC:0B:B2
32 E8:28:C1:DC:33:12
33 00:03:7A:1A:03:56
34 00:03:7F:12:34:56
35 00:3E:1A:5D:14:45
36 E0:D9:E3:49:00:B1
37 E8:28:C1:DC:BD:52
38 00:26:99:F2:7A:EF
39 02:67:F1:B0:6C:98
40 02:CF:8B:87:B4:F9
41 00:53:7A:99:98:56
42 E8:28:C1:DE:47:D0
```
#### Определить производителя для каждого обнаруженного устройства
```
> names_facture <- sapply(unsafe_points,function(i) substr(i, 1, 8))%>%unique()
```
```
> cat(names_facture, "\n")
E8:28:C1 00:25:00 E0:D9:E3 00:26:99 02:BC:15 00:AB:0A 00:03:7A 00:03:7F 00:3E:1A 02:67:F1 02:CF:8B 00:53:7A
```
E8:28:C1 - Eltex Enterprise Ltd 
00:25:00 - Apple Inc; E0:D9:E3 - Eltex Enterprise Ltd
00:26:99 - Cisco Systems 
00:03:7A - Taiyo Yuden Co., Ltd 
00:03:7F - Atheros Communications, Inc.
#### Выявить устройства, использующие последнюю версию протокола шифрования WPA3, и названия точек доступа, реализованных на этих устройствах
```
> df1%>%filter(grepl("WPA3", Privacy))%>%select(BSSID,ESSID,Privacy)
```
```
              BSSID              ESSID   Privacy
1 26:20:53:0C:98:E8               <NA> WPA3 WPA2
2 A2:FE:FF:B8:9B:C9         Christie’s WPA3 WPA2
3 96:FF:FC:91:EF:64               <NA> WPA3 WPA2
4 CE:48:E7:86:4E:33 iPhone (Анастасия) WPA3 WPA2
5 8E:1F:94:96:DA:FD iPhone (Анастасия) WPA3 WPA2
6 BE:FD:EF:18:92:44            Димасик WPA3 WPA2
7 3A:DA:00:F9:0C:02  iPhone XS Max 🦊🐱🦊 WPA3 WPA2
8 76:C5:A0:70:08:96               <NA> WPA3 WPA2
```
#### Отсортировать точки доступа по интервалу времени, в течение которого они находились на связи, по убыванию
```
> net_data <- df1 %>% mutate(Time = difftime(Last.time.seen, First.time.seen, units = "mins")) %>% arrange(desc(Time)) %>% select(BSSID, Time)
```
```
> net_data %>% head(20)
               BSSID          Time
1  00:25:00:FF:94:73 163.2500 mins
2  E8:28:C1:DD:04:52 162.9333 mins
3  E8:28:C1:DC:B2:52 162.5833 mins
4  08:3A:2F:56:35:FE 162.4333 mins
5  6E:C7:EC:16:DA:1A 162.1500 mins
6  E8:28:C1:DC:B2:50 162.1000 mins
7  E8:28:C1:DC:B2:51 162.0833 mins
8  48:5B:39:F9:7A:48 162.0833 mins
9  E8:28:C1:DC:FF:F2 162.0667 mins
10 8E:55:4A:85:5B:01 162.0500 mins
11 00:26:99:BA:75:80 161.8333 mins
12 00:26:99:F2:7A:E2 161.7833 mins
13 1E:93:E3:1B:3C:F4 160.5500 mins
14 9A:75:A8:B9:04:1E 160.4667 mins
15 0C:80:63:A9:6E:EE 160.4667 mins
16 00:23:EB:E3:81:F2 159.9167 mins
17 9E:A3:A9:DB:7E:01 159.2500 mins
18 E8:28:C1:DC:C8:32 159.2500 mins
19 1C:7E:E5:8E:B7:DE 158.7333 mins
20 00:26:99:F2:7A:E1 158.2000 mins
```
#### Обнаружить топ-10 самых быстрых точек доступа
```
> top_speed <- df1 %>% arrange(desc(Speed))
```
```
> top_speed %>% select(BSSID, ESSID, Speed) %>% knitr::kable() %>% head(10)
 [1] "|BSSID             |ESSID                       | Speed|"
 [2] "|:-----------------|:---------------------------|-----:|"
 [3] "|26:20:53:0C:98:E8 |NA                          |   866|"
 [4] "|96:FF:FC:91:EF:64 |NA                          |   866|"
 [5] "|CE:48:E7:86:4E:33 |iPhone (Анастасия)          |   866|"
 [6] "|8E:1F:94:96:DA:FD |iPhone (Анастасия)          |   866|"
 [7] "|9A:75:A8:B9:04:1E |KC                          |   360|"
 [8] "|4A:EC:1E:DB:BF:95 |POCO X5 Pro 5G              |   360|"
 [9] "|56:C5:2B:9F:84:90 |OnePlus 6T                  |   360|"
[10] "|E8:28:C1:DC:B2:41 |MIREA_GUESTS                |   360|"
```
#### Отсортировать точки доступа по частоте отправки запросов (beacons) в единицу времени по их убыванию
```
> beacon_rate <- df1 %>% mutate(beacon_rate = X..beacons /as.numeric(difftime(Last.time.seen,First.time.seen,units="mins"))) %>%filter(!is.infinite(beacon_rate)) %>%arrange(desc(beacon_rate))
```
```
> beacon_rate %>% select(BSSID, ESSID, beacon_rate) %>% knitr::kable() %>% head(10)
 [1] "|BSSID             |ESSID                    | beacon_rate|"
 [2] "|:-----------------|:------------------------|-----------:|"
 [3] "|F2:30:AB:E9:03:ED |iPhone (Uliana)          |  51.4285714|"
 [4] "|B2:CF:C0:00:4A:60 |Михаил's Galaxy M32      |  48.0000000|"
 [5] "|3A:DA:00:F9:0C:02 |iPhone XS Max 🦊🐱🦊     |  33.3333333|"
 [6] "|02:BC:15:7E:D5:DC |MT_FREE                  |  30.0000000|"
 [7] "|00:3E:1A:5D:14:45 |MT_FREE                  |  30.0000000|"
 [8] "|76:C5:A0:70:08:96 |NA                       |  30.0000000|"
 [9] "|D2:25:91:F6:6C:D8 |Саня                     |  23.0769231|"
[10] "|BE:F1:71:D6:10:D7 |C322U21 0566             |  10.4449847|"
```

### 3. Данные клиентов
#### Определить производителя для каждого обнаруженного устройства
```
> facture <- df2 %>% filter(BSSID != '(not associated)') %>% mutate(Manufacturer = substr(BSSID, 1, 8)) %>% select(Manufacturer)
```
```
> unique(facture) %>% knitr::kable() %>% head(10)
 [1] "|    |Manufacturer |" "|:---|:------------|"
 [3] "|1   |BE:F1:71     |" "|4   |1E:93:E3     |"
 [5] "|5   |E8:28:C1     |" "|6   |00:25:00     |"
 [7] "|7   |00:26:99     |" "|8   |0C:80:63     |"
 [9] "|10  |0A:C5:E1     |" "|12  |9A:75:A8     |"
```
E8:28:C1 - Eltex Enterprise Ltd.
00:25:00 - Apple, Inc.
00:26:99 - Cisco Systems, Inc
0C:80:63 - Tp-Link Technologies Co.,Ltd.
#### Обнаружить устройства, которые НЕ рандомизируют свой MAC адрес
```
> df2 %>% filter(!grepl("^02|^06|^0A|^0E", BSSID)) %>% filter(BSSID != '(not associated)') %>% select(BSSID) %>% knitr::kable() %>% head(20)
```
```
 [1] "|BSSID             |" "|:-----------------|"
 [3] "|BE:F1:71:D5:17:8B |" "|BE:F1:71:D6:10:D7 |"
 [5] "|BE:F1:71:D5:17:8B |" "|1E:93:E3:1B:3C:F4 |"
 [7] "|E8:28:C1:DC:FF:F2 |" "|00:25:00:FF:94:73 |"
 [9] "|00:26:99:F2:7A:E2 |" "|0C:80:63:A9:6E:EE |"
[11] "|E8:28:C1:DD:04:52 |" "|1E:93:E3:1B:3C:F4 |"
[13] "|9A:75:A8:B9:04:1E |" "|8A:A3:03:73:52:08 |"
[15] "|4A:EC:1E:DB:BF:95 |" "|BE:F1:71:D5:0E:53 |"
[17] "|08:3A:2F:56:35:FE |" "|6E:C7:EC:16:DA:1A |"
[19] "|6E:C7:EC:16:DA:1A |" "|08:3A:2F:56:35:FE |"
```
#### Кластеризовать запросы от устройств к точкам доступа по их именам. Определить время появления устройства в зоне радиовидимости и время выхода его из нее.
```
> df2 %>% group_by(Probed.ESSIDs) %>% summarise(first_seen = min(First.time.seen, na.rm = TRUE),last_seen = max(Last.time.seen, na.rm = TRUE)) %>% knitr::kable() %>% head(10)
```
```
 [1] "|Probed.ESSIDs                    |first_seen          |last_seen           |"
 [2] "|:--------------------------------|:-------------------|:-------------------|"
 [3] "|-D-13-                           |2023-07-28 09:14:42 |2023-07-28 10:26:42 |"
 [4] "|1                                |2023-07-28 10:36:12 |2023-07-28 11:56:13 |"
 [5] "|107                              |2023-07-28 10:29:43 |2023-07-28 10:29:43 |"
 [6] "|531                              |2023-07-28 10:57:04 |2023-07-28 10:57:04 |"
 [7] "|AAAAAOB/CC0ADwGkRedmi 3S         |2023-07-28 09:34:20 |2023-07-28 11:44:40 |"
 [8] "|AKADO-D967                       |2023-07-28 10:31:55 |2023-07-28 10:31:55 |"
 [9] "|AQAAAB6zaIoATwEURedmi Note 5     |2023-07-28 10:25:19 |2023-07-28 11:51:48 |"
[10] "|ASUS                             |2023-07-28 10:31:13 |2023-07-28 10:31:13 |"
```
#### Оценить стабильность уровня сигнала внури кластера во времени. Выявить наиболее стабильный кластер.
```
> df2%>%mutate(t=difftime(Last.time.seen,First.time.seen))%>%filter(t!=0)%>%arrange(desc(t))%>%filter(!is.na(Probed.ESSIDs))%>%group_by(Probed.ESSIDs)%>%summarise(m=mean(t),Sd=sd(t))%>%filter(Sd!=0)%>%arrange(Sd)%>%select(Probed.ESSIDs,m,Sd)%>%head(1)
```
```
# A tibble: 1 × 3
  Probed.ESSIDs m            Sd
  <chr>         <drtn>    <dbl>
1 nvripcsuite   9780 secs  3.46
```
## Оценка результата

В результате работы были импортированы, структурированы и проанализированы данные из .csv файла
## Вывод
Были проанализированы датасеты и выполнены все задачи