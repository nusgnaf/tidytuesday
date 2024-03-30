# 加拿大曲棍球球员出生月份

如果你是一名加拿大曲棍球球员，祝你生日快乐！现在这个时间比秋天更有可能是正确的出生月份！

本周的数据集来自[加拿大统计局](https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1310041501&pickMembers%5B0%5D=3.1&cubeTimeFrame.startYear=1991&cubeTimeFrame.endYear=2022&referencePeriods=19910101%2C20220101)、[NHL球队列表端点](https://api.nhle.com/stats/rest/en/team)，以及[NHL API](https://api-web.nhle.com/v1/)。
这个数据集受到了JLaw的博客["加拿大NHL球员的出生日期仍然是命运吗？"](https://jlaw.netlify.app/2023/12/04/are-birth-dates-still-destiny-for-canadian-nhl-players/)的启发（通过[Mastodon](https://universeodon.com/@jlaw/111522860812359901)）！

> 在[马尔科姆·格拉德威尔的《异类》](https://www.amazon.com/Outliers-Story-Success-Malcolm-Gladwell/dp/0316017930)的第一章中，他讨论了在加拿大青少年曲棍球中，更有可能出生于一年的第一季度。

> 因为这些孩子在他们的年龄组内年龄较大，他们在年幼时就加入了所有重要的球队，这使得他们能够获得更好的技能发展资源等。

> 虽然似乎更多的球员出生在年初几个月，但尚未探讨的是是否可以预期到这种情况。也许加拿大一般的人更多地出生在年初。

> 我将探讨格拉德威尔的结果是否是预期的，以及这在当今的NHL对于出生在加拿大的球员是否仍然成立。

你能复现JLaw的结果吗？在NHL球员数据中还能找到什么？

## 数据

```{r}
# 选项1：tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-01-09')
## 或者
tuesdata <- tidytuesdayR::tt_load(2024, week = 2)

canada_births_1991_2022 <- tuesdata$canada_births_1991_2022
nhl_player_births <- tuesdata$nhl_player_births
nhl_rosters <- tuesdata$nhl_rosters
nhl_teams <- tuesdata$nhl_teams

# 选项2：直接从 GitHub 读取

canada_births_1991_2022 <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-01-09/canada_births_1991_2022.csv')
nhl_player_births <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-01-09/nhl_player_births.csv')
nhl_rosters <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-01-09/nhl_rosters.csv')
nhl_teams <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-01-09/nhl_teams.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应在数据中得出**因果**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并考虑这些关系下可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用程序](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `canada_births_1991_2022.csv`

|variable |class  |description |
|:--------|:------|:-----------|
|year     |integer|birth year|
|month    |integer|birth month|
|births   |integer|number of live births in Canada in that year and month|

# `nhl_player_births.csv`

|variable             |class     |description          |
|:--------------------|:---------|:--------------------|
|player_id            |double    |unique id of this player (note: 2 players are listed twice with slightly different data) |
|first_name           |character |first name |
|last_name            |character |last name |
|birth_date           |date      |birth date |
|birth_city           |character |birth city |
|birth_country        |character |3-letter code for the birth country |
|birth_state_province |character |birth state or province, if applicable |
|birth_year           |integer   |birth year |
|birth_month          |integer   |birth month |

# `nhl_rosters.csv`

|variable              |class     |description           |
|:---------------------|:---------|:---------------------|
|team_code             |character |unique 3-letter code for this team |
|season                |integer   |season, as YYYYYYYY |
|position_type         |character |"defensemen", "forwards", or "goalies" |
|player_id             |double    |unique id of this player |
|headshot              |character |headshot url for this player-season |
|first_name            |character |first name            |
|last_name             |character |last name             |
|sweater_number        |double    |sweater number        |
|position_code         |character |position code (C = center, D = defense, G = goal, L = left wing, R = right wing) |
|shoots_catches        |character |hand preferred by this player for shooting and catching (L, R, or NA) |
|height_in_inches      |integer   |height in inches at the start of this season |
|weight_in_pounds      |integer   |weight in pounds at the start of this season |
|height_in_centimeters |integer   |height in centimeters at the start of this season |
|weight_in_kilograms   |integer   |weight in kilograms at the start of this season |
|birth_date            |date      |birth date            |
|birth_city            |character |birth city            |
|birth_country         |character |3-letter code for the birth country |
|birth_state_province  |character |birth state or province, if applicable |

# `nhl_teams.csv`

|variable  |class     |description |
|:---------|:---------|:-----------|
|team_code |character |unique 3-letter code for this team |
|full_name |character |full name of this team |


### Cleaning Script

``` r
library(tidyverse)
library(here)
library(janitor)
library(fs)
library(httr2)
library(withr)

working_dir <- here::here("data", "2024", "2024-01-09")

teams <- httr2::request("https://api.nhle.com/stats/rest/en/team") |> 
  httr2::req_perform() |> 
  httr2::resp_body_json() |> 
  _$data |> 
  tibble::tibble(data = _) |> 
  tidyr::unnest_wider(data) |> 
  janitor::clean_names() |> 
  dplyr::select(team_code = tri_code, full_name)
  
rosters <- teams |> 
  dplyr::pull(team_code) |> 
  purrr::map(\(team_code) {
    seasons <- glue::glue("https://api-web.nhle.com/v1/roster-season/{team_code}") |> 
      httr2::request() |> 
      httr2::req_perform() |> 
      httr2::resp_body_json() |> 
      unlist()
    purrr::map(
      seasons,
      \(season) {
        season_roster <- glue::glue("https://api-web.nhle.com/v1/roster/{team_code}/{season}") |>
          httr2::request() |>
          httr2::req_perform() |>
          httr2::resp_body_json() |>
          tibble::enframe(name = "position_type") |> 
          tidyr::unnest_longer(value)
        if (nrow(season_roster)) {
          season_roster |> 
            tidyr::unnest_wider(value) |> 
            janitor::clean_names() |> 
            dplyr::rename(player_id = id) |> 
            tidyr::hoist("first_name", first_name = "default", .remove = FALSE) |> 
            tidyr::hoist("last_name", last_name = "default", .remove = FALSE) |> 
            tidyr::hoist("birth_city", birth_city = 1, .remove = FALSE) |> 
            tidyr::hoist("birth_state_province", birth_state_province = 1, .remove = FALSE) |>
            dplyr::mutate(
              team_code = team_code,
              season = season,
              .before = 1
            )
        } else {
          NULL
        }
      }
    ) |> 
      purrr::list_rbind()
  }) |> 
  purrr::list_rbind()

player_births <- rosters |> 
  dplyr::distinct(
    player_id,
    first_name,
    last_name,
    birth_date,
    birth_city,
    birth_country,
    birth_state_province
  ) |> 
  dplyr::mutate(
    birth_date = lubridate::ymd(birth_date),
    birth_year = lubridate::year(birth_date),
    birth_month = lubridate::month(birth_date)
  )

dl_path <- withr::local_tempfile(fileext = ".zip")

download.file(
  "https://www150.statcan.gc.ca/n1/tbl/csv/13100415-eng.zip",
  dl_path
)

canada_births <- readr::read_csv(dl_path) |> 
  janitor::clean_names() |> 
  dplyr::filter(
    !stringr::str_detect(month_of_birth, "Total"),
    characteristics == "Number of live births", 
    stringr::str_starts(geo, "Canada")
  ) |> 
  dplyr::mutate(
    year = as.integer(ref_date),
    month = stringr::str_extract(month_of_birth, "Month of birth, (\\w+)", 1) |> 
      factor(levels = month.name) |> 
      as.integer(),
    births = as.integer(value),
    .keep = "none"
  )

readr::write_csv(
  canada_births,
  fs::path(working_dir, "canada_births_1991_2022.csv")
)
readr::write_csv(
  player_births,
  fs::path(working_dir, "nhl_player_births.csv")
)
readr::write_csv(
  rosters,
  fs::path(working_dir, "nhl_rosters.csv")
)
readr::write_csv(
  teams,
  fs::path(working_dir, "nhl_teams.csv")
)
```
