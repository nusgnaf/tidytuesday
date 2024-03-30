# 美国投票地点 2012-2020

本周，我们通过探索美国投票地点来纪念马丁·路德·金的遗产。

[数据集](https://github.com/PublicI/us-polling-places)来自[公共诚信中心](https://publicintegrity.org/)。
您可以在他们2020年9月的文章["国家数据发布揭示了过去投票地点变化"](https://publicintegrity.org/politics/elections/ballotboxbarriers/data-release-sheds-light-on-past-polling-place-changes/)中了解更多关于数据及其收集方式的信息。
感谢[Kelsey E Gonzalez](https://github.com/kelseygonzalez)在2020年提供数据集建议！

注意：一些州在此数据集中没有数据。几个州（科罗拉多州、夏威夷州、俄勒冈州、华盛顿州和犹他州）主要通过邮寄投票，并且在这个集合中几乎没有数据，其他一些州由于其他原因不可用。

对于有多次选举数据的州，县级投票地点数量随时间如何变化？

## 数据

```{r}
# 选项1：tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-01-16')
## 或者
tuesdata <- tidytuesdayR::tt_load(2024, week = 3)

polling_places <- tuesdata$polling_places

# 选项2：直接从 GitHub 读取

polling_places <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-01-16/polling_places.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应在数据中得出**因果**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并考虑这些关系下可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用程序](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `polling_places.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|election_date     |date      |date of the election as YYYY-MM-DD |
|state             |character |2-letter abbreviation of the state |
|county_name       |character |county name, if available |
|jurisdiction      |character |jurisdiction, if available |
|jurisdiction_type |character |type of jurisdiction, if available; one of "county", "borough", "town", "municipality", "city", "parish", or "county_municipality" |
|precinct_id       |character |unique ID of the precinct, if available |
|precinct_name     |character |name of the precinct, if available |
|polling_place_id  |character |unique ID of the polling place, if available |
|location_type     |character |type of polling location, if available; one of "early_vote", "early_vote_site", "election_day", "polling_location", "polling_place", or "vote_center" |
|name              |character |name of the polling place, if available |
|address           |character |address of the polling place, if available |
|notes             |character |optional notes about the polling place |
|source            |character |source of the polling place data; one of "ORR", "VIP", "website", or "scraper" |
|source_date       |date      |date that the source was compiled |
|source_notes      |character |optional notes about the source |

### Cleaning Script

``` r
library(tidyverse)
library(here)
library(fs)
library(gh)

working_dir <- here::here("data", "2024", "2024-01-16")

polling_places <- 
  # Get the list of states that have data.
  gh::gh("/repos/PublicI/us-polling-places/contents/data") |> 
  purrr::map_chr("name") |> 
  # For each state, get the list of CSV URLs.
  purrr::map(
    \(state) {
      gh::gh(
        glue::glue("/repos/PublicI/us-polling-places/contents/data/{state}/output")
      ) |> 
        purrr::map_chr("download_url")
    }
  ) |> 
  unlist() |> 
  readr::read_csv(
    col_types = readr::cols(.default = readr::col_character())
  ) |> 
  # Some rows are duplicated.
  dplyr::distinct() |> 
  dplyr::mutate(
    election_date = lubridate::ymd(election_date),
    source_date = lubridate::ymd(source_date)
  )

readr::write_csv(
  polling_places,
  fs::path(working_dir, "polling_places.csv")
)
```
