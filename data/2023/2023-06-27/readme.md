# 美国有人居住的地方

当我们开始夏季度假的公路旅行时，本周的数据来自[国家地图分期产品目录](https://prd-tnm.s3.amazonaws.com/index.html?prefix=StagedProducts/GeographicNames/)，来自[美国地理名称委员会](https://www.usgs.gov/us-board-on-geographic-names/download-gnis-data)。

注意：GNIS还提供了大量其他数据。请查看清理脚本以获取下载其他数据的线索。

### 在这里获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取数据
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载本周感兴趣的readme和所有数据集

# ISO-8601日期或年/周都可以使用！

tuesdata <- tidytuesdayR::tt_load('2023-06-27')
tuesdata <- tidytuesdayR::tt_load(2023, week = 26)

us_place_names <- tuesdata$`us_place_names`
us_place_history <- tuesdata$`us_place_history`

# 或者手动读取数据

us_place_names <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-06-27/us_place_names.csv')
us_place_history <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-06-27/us_place_history.csv')
```

### 数据字典

# `us_place_names.csv`

|variable       |class     |description    |
|:--------------|:---------|:--------------|
|feature_id     |double    |Permanent, unique feature record identifier. |
|feature_name   |character |Official feature name. |
|state_name     |character |The name of the state containing the primary coordinates. |
|county_name    |character |The name of the county containing the primary coordinates. |
|county_numeric |double    |The 3-digit code for the county containing the primary coordinates. |
|date_created   |date |The date the record was initially entered into the
Geographic Names Information System. |
|date_edited    |date |The date any attribute of an existing record was
edited. |
|prim_lat_dec   |double    |The latitude of the official feature location. Note that some values are unknown. |
|prim_long_dec  |double    |The longitude of the official feature location. Note that some values are unknown. |

# `us_place_history.csv`

|variable    |class     |description |
|:-----------|:---------|:-----------|
|feature_id  |double    |Permanent, unique feature record identifier. |
|description |character |Characteristics or information about a feature or the feature data |
|history     |character |Refers to the name origin, and/or cultural history of a feature. |

### Cleaning Script

See Jon Harmon's [cleaning](https://github.com/jonthegeek/apis/blob/main/01_ufo-data.qmd) and [enriching](https://github.com/jonthegeek/apis/blob/main/01_ufo-enrich.qmd) scripts for most of the (extensive) cleaning.

```
library(tidyverse)
library(withr)
library(fs)
library(here)

url <- "https://prd-tnm.s3.amazonaws.com/StagedProducts/GeographicNames/DomesticNames/DomesticNames_National_Text.zip"

path_zip <- local_tempfile(fileext = ".zip")
download.file(url, path_zip)

us_place_names <- read_delim(path_zip, "|") |> 
  mutate(county_numeric = as.integer(county_numeric)) |> 
  # We'll just use populated places, you might want to keep more!
  filter(feature_class == "Populated Place") |> 
  select(-feature_class, -starts_with("source_")) |> 
  # We also don't keep some redundant or less useful features.
  select(
    -state_numeric,
    -map_name, 
    -starts_with("bgn"), 
    -ends_with("_dms")) |> 
  mutate(
    across(
      ends_with("_dec"),
      ~ na_if(.x, 0)
    ),
    across(
      starts_with("date_"),
      lubridate::mdy
    )
  ) |> 
  distinct()

glimpse(us_place_names)

url_history <- "https://prd-tnm.s3.amazonaws.com/StagedProducts/GeographicNames/Topical/FeatureDescriptionHistory_National_Text.zip"

path_history <- local_tempfile(fileext = ".zip")
download.file(url_history, path_history)

us_place_history <- read_delim(path_history, "|") |> 
  semi_join(us_place_names)

# Data dictionary: 
# https://prd-tnm.s3.amazonaws.com/StagedProducts/GeographicNames/GNIS_file_format.pdf

write_csv(
  us_place_names,
  here::here(
    "data",
    "2023",
    "2023-06-27",
    "us_place_names.csv"
  )
)
write_csv(
  us_place_history,
  here::here(
    "data",
    "2023",
    "2023-06-27",
    "us_place_history.csv"
  )
)
```
