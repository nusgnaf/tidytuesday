# 澳大利亚的袋獾

本周的数据来自 [澳大利亚生物多样性图谱](https://www.ala.org.au)。感谢 Di Cook 准备了本周的数据集！

[澳大利亚生物多样性图谱的袋獾页面](https://bie.ala.org.au/species/https://biodiversity.org.au/afd/taxa/6c72d199-f0f1-44d3-8197-224a2f7cff5f)对这些濒危物种有更详细的介绍。

提供了袋獾观测数据的 [csv](data/numbats.csv) 文件。刷新数据的代码如下。

一些有趣的问题需要回答：

- 澳大利亚的哪些地方可以找到袋獾？
- 历史上分布更广泛吗？（您可能需要排除动物园报告的观察结果。）
- 袋獾的观察通常在一天的什么时间发生？
- 夏季或冬季袋獾的观察频率更高？
- 袋獾在晴天和温暖的天气比在阴天、潮湿、寒冷的天气中更容易观察到吗？
- 观察是否更多发生在工作日而不是周末？

### 在此处获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# 可以使用 ISO-8601 日期或年/周

tuesdata <- tidytuesdayR::tt_load('2023-03-07')
tuesdata <- tidytuesdayR::tt_load(2023, week = 10)

numbats <- tuesdata$numbats

# 或者手动读取数据

numbats <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-03-07/numbats.csv')
```

### 数据字典

# `numbats.csv`

|variable         |class     |description      |
|:----------------|:---------|:----------------|
|decimalLatitude  |double    |decimalLatitude  |
|decimalLongitude |double    |decimalLongitude |
|eventDate        |datetime  |eventDate        |
|scientificName   |factor    |Either "Myrmecobius fasciatus" or "Myrmecobius fasciatus rufus" |
|taxonConceptID   |factor    |The URL for this (sub)species |
|recordID         |character |recordID         |
|dataResourceName |factor    |dataResourceName |
|year             |integer   |The 4-digit year of the event (when available) |
|month            |factor    |The 3-letter month abbreviation of the event (when available) |
|wday             |factor    |The 3-letter weekday abbreviation of the event (when available) |
|hour             |integer   |The hour of the event (when available) |
|day              |date      |The date of the event (when available) |
|dryandra         |logical   |whether the observation was in Dryandra Woodland |
|prcp             |double    |Precipitation on that day in Dryandra Woodland (when relevant), in millimeters |
|tmax             |double    |Maximum temperature on that day in Dryandra Woodland (when relevant), in degrees Celsius |
|tmin             |double    |Minimum temperature on that day in Dryandra Woodland (when relevant), in degrees Celsius |


### Cleaning Script

```r
# Cleaning script provided at
# https://github.com/numbats/numbats-tidytuesday/blob/main/code/data.R Slightly
# updated here.

library(galah) # API to ALA
library(lubridate)
library(tidyverse)
library(rnoaa)
library(here)

# Downloading data is free but you need to 
# create an account https://www.ala.org.au then
# use this email address to pull data.
# galah_config(email = YOUR_EMAIL_ADDRESS)
id <- galah_identify("numbat")

numbats <- atlas_occurrences(identify = id)
numbats <- numbats %>%
  mutate(
    year = year(eventDate),
    month = month(eventDate, label=TRUE, abbr=TRUE),
    wday = wday(eventDate, label=TRUE, abbr=TRUE, week_start = 1),
    hour = hour(eventDate),
    day = ymd(as.Date(eventDate))
  )

narrogin <- meteo_pull_monitors(
  monitors = "ASN00010614",
  var = c("PRCP", "TMAX", "TMIN"),
  date_min = "2005-01-01",
  date_max = "2023-02-23")

narrogin %>%
  pivot_longer(cols = prcp:tmin, names_to = "var", values_to = "value") %>%
  mutate(day = lubridate::yday(date), year = lubridate::year(date)) %>%
  ggplot(aes(x = day, y= year, fill = is.na(value))) +
  geom_tile() +
  theme_minimal() +
  theme(panel.grid = element_blank()) +
  facet_wrap(vars(var), ncol = 1) +
  scale_fill_brewer(palette = "Dark2", name = "missing") +
  xlab("Day of the year")

narrogin_latlon <- tibble(lon = 117.1782, lat = -32.9310)

within_rad <- function(x, y, lon, lat, km) {
  deg <- km/111
  inside <- sqrt((lon-x)^2 + (lat-y)^2) < deg
  return(inside)
}

# Only sites within 50km radius of Narrogin weather station
# which is Dryandra Woodlands
numbats <- numbats %>%
  mutate(
    dryandra = within_rad(
      decimalLongitude, decimalLatitude, 
      narrogin_latlon$lon, narrogin_latlon$lat,
      50
    )
  )
  
numbats <- numbats %>% 
  left_join(narrogin, by = join_by(day == date)) %>%
  mutate(
    prcp = if_else(dryandra, prcp, NA, missing = NA),
    tmax = if_else(dryandra, tmax, NA, missing = NA),
    tmin = if_else(dryandra, tmin, NA, missing = NA)
  ) %>%
  select(-id)

# Things are only in this dataset if they were PRESENT.
numbats <- numbats |> 
  select(-occurrenceStatus)

# Those last three values are in values to coerce them to integers, and might be
# confusing. Translate them to doubles.
numbats <- numbats |> 
  mutate(
    prcp = prcp/10,
    tmax = tmax/10,
    tmin = tmin/10
  )

write_csv(
  numbats, 
  file = here::here(
    "data",
    "2023",
    "2023-03-07",
    "numbats.csv"
  )
)
```
