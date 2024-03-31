# 历史标志

本周的数据来自[美国历史标志数据库索引](https://www.hmdb.org/geolists.asp?c=United%20States%20of%20America)。在[HMDb.org网站](https://www.hmdb.org/)上了解更多关于这些标志的信息，其中包括一些文章，包括[数据库计数和统计信息](https://www.hmdb.org/stats.asp)。

我们还包含了一些在历史标志数据库中没有条目的地点的数据集。
您可以尝试将其与[geonames.org](http://www.geonames.org/)（代码：HSTS）的信息结合起来，以找到需要提交的标志。感谢[Jesus M. Castagnetto](https://github.com/rfordatascience/tidytuesday/issues/574#issuecomment-1601050053) 提供的 geonames 提示！

### 在这里获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取数据
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载本周感兴趣的readme和所有数据集

# ISO-8601日期或年/周都可以使用！

tuesdata <- tidytuesdayR::tt_load('2023-07-04')
tuesdata <- tidytuesdayR::tt_load(2023, week = 27)

historical_markers <- tuesdata$`historical_markers`
no_markers <- tuesdata$`no_markers`

# 或者手动读取数据

historical_markers <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-07-04/historical_markers.csv')
no_markers <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-07-04/no_markers.csv')
```

### 数据字典

# `historical_markers.csv`

|variable           |class     |description        |
|:------------------|:---------|:------------------|
|marker_id          |double    |Unique ID for this marker in the HMdb. |
|marker_no          |character |Number of this marker in the state numbering scheme. |
|title              |character |Main title of the marker. |
|subtitle           |character |Subtitle of the marker, if any. |
|addl_subtitle      |character |Additional subtitle text. |
|year_erected       |integer   |The year in which the marker was erected. |
|erected_by         |character |The organization which erected the marker. |
|latitude_minus_s   |double    |The latitude of the marker. |
|longitude_minus_w  |double    |The longitude of the marker. |
|street_address     |character |The street address of the marker, if available. |
|city_or_town       |character |The city, town, etc in which the marker is located. |
|section_or_quarter |character |The section of the city, town, etc, when available. |
|county_or_parish   |character |The county, parish, or similar designation in which the marker appears. |
|state_or_prov      |character |The state, province, territory, etc in which the marker appears. |
|location           |character |A description of the marker's location. |
|missing            |character |Whether the marker is "Reported missing" or "Confirmed missing". NA values indicate that the marker has neither been reported missing nor confirmed as missing. |
|link               |character |The HMDb link to the marker. Links include additional details, such as photos and topic lists to which this marker belongs. |

# `no_markers.csv`

|variable |class     |description |
|:--------|:---------|:-----------|
|county   |character |County or equivalent. |
|state    |character |State or territory. |

### Cleaning Script

```
library(tidyverse)
library(here)
library(fs)
library(janitor)
library(remotes)

# I used the dev version of rvest from
# https://github.com/tidyverse/rvest/pull/362 as of 2023-06-21 to scrape some
# data this week.
install_github("tidyverse/rvest#362")
library(rvest)

# Begin by visiting
# https://www.hmdb.org/geolists.asp?c=United%20States%20of%20America, opening
# each state in a new tab, and then clicking the "Download" button at the
# top-right. I saved them all locally in a state_exports folder in this week's
# data folder, but I won't upload those unprocessed CSVs to github.

export_path <- here::here("data", "2023", "2023-07-04", "state_exports")

historical_markers_raw <- fs::dir_map(
  export_path,
  read_csv
)

# I know there's one problem with each read, which we'll deal with. Check for
# additional problems.
probs <- historical_markers_raw |> 
  purrr::map(
    \(hm) {
      problems(hm) |> 
        filter(!(expected == "17 columns" & actual == "4 columns")) |> 
        mutate(file = str_remove(file, fixed(export_path)))
    }
  )

has_probs <- vctrs::list_sizes(probs) > 0

probs[has_probs]
historical_markers_raw[[which(has_probs)]] |> 
  slice(1168:1170) |> 
  glimpse()

# It looks like the "Section or Quarter" column is auto-guessed as logical (NA),
# but should actually be character. We'll use this information to re-parse.

hmdb_cols <- spec(historical_markers_raw[[1]]) |> 
  cols_condense()
hmdb_cols

# That spec will take care of the problem.

historical_markers_raw <- fs::dir_map(
  export_path,
  \(path) {
    read_csv(path, col_types = hmdb_cols)
  }
)

# The remaining problems are all the expected one.

historical_markers <- historical_markers_raw |> 
  purrr::list_rbind() |> 
  filter(`Marker No.` != "Source: Hmdb.org") |> 
  clean_names()

# HMDb.org also has an interesting dataset about missing data. We'll scrape that
# as well. 

stats_page <- rvest::read_html_live("https://www.hmdb.org/stats.asp")

stats_page$click("#NoCArrow")

no_markers <- stats_page |> 
  rvest::html_element("#NoCDiv") |> 
  rvest::html_table() |> 
  select(X2) |> 
  filter(!X2 == "") |> 
  tidyr::separate_wider_delim(X2, ",  ", names = c("county", "state"))

write_csv(
  historical_markers,
  here::here(
    "data",
    "2023",
    "2023-07-04",
    "historical_markers.csv"
  )
)
write_csv(
  no_markers,
  here::here(
    "data",
    "2023",
    "2023-07-04",
    "no_markers.csv"
  )
)
```
