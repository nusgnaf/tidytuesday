# 闰年日

闰年日快乐！本周的数据来自维基百科上的[2月29日](https://en.wikipedia.org/wiki/February_29)条目。

> 2月29日是一个闰日（或"闰年日"），周期性添加到儒略历和格里高利历中以创建闰年的日期。

维基百科列表中缺少的一个事件是：[R语言的1.0版本于2000年2月29日发布](https://en.wikipedia.org/wiki/R_(programming_language)#History)。

哪个年龄段的闰年日出生在维基百科的数据中最常见？与附近年份相比，有没有年份的数据明显较少？您能在数据中发现什么其他模式？

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-02-27')
## OR
tuesdata <- tidytuesdayR::tt_load(2024, week = 9)

events <- tuesdata$events
births <- tuesdata$births
deaths <- tuesdata$deaths

# 选项 2: 直接从 GitHub 读取

events <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-02-27/events.csv')
births <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-02-27/births.csv')
deaths <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-02-27/deaths.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该在数据中得出**因果关系**的结论。影响所有数据的各种调节变量，其中许多可能没有在这些数据集中捕获。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `events.csv`

|variable |class     |description |
|:--------|:---------|:-----------|
|year     |integer   |Year of the event. |
|event    |character |A short, free-text description of the event. |

# `births.csv`

|variable    |class     |description |
|:-----------|:---------|:-----------|
|year_birth  |integer   |Year in which this person was born. |
|person      |character |The name of the person. |
|description |character |A short description of the person. |
|year_death  |integer   |Year in which this person died. |

# `deaths.csv`

|variable    |class     |description |
|:-----------|:---------|:-----------|
|year_death  |integer   |Year in which this person died. |
|person      |character |The name of the person. |
|description |character |A short description of the person. |
|year_birth  |integer   |Year in which this person was born. |

### 清洗脚本

``` r
library(tidyverse)
library(rlang)
library(rvest)
library(here)

working_dir <- here::here("data", "2024", "2024-02-27")

feb29 <- "https://en.wikipedia.org/wiki/February_29"

# Read the HTML once so we don't have to keep hitting it.
feb29_html <- rvest::read_html(feb29)

# Find the headers. We'll use these to figure out which bullets are "inside"
# each header, since nothing "contains" them to make it easy.
h2s <- feb29_html |> 
  rvest::html_elements("h2") |> 
  rvest::html_text2() |> 
  stringr::str_remove("\\[edit\\]")

# We'll get all bullets that are after each header. We can then subtract out
# later lists to figure out what's under a particular header.
bullets_after_headers <- purrr::map(
  h2s,
  \(this_header) {
    this_selector <- glue::glue("h2:contains('{this_header}') ~ ul > li")
    feb29_html |> 
      rvest::html_elements(this_selector) |> 
      rvest::html_text2() |> 
      # Remove footnotes.
      stringr::str_remove_all("\\[\\d+\\]")
  }
) |> 
  rlang::set_names(h2s)

# Subtract subsequent bullets from each set.
bullets_in_headers <- purrr::map2(
  bullets_after_headers[-length(h2s)],
  bullets_after_headers[-1],
  setdiff
)

# The three sets we care about (Events, Births, Deaths) each have their own
# format.
events <- tibble::tibble(events = bullets_in_headers[["Events"]]) |> 
  tidyr::separate_wider_regex(
    "events",
    patterns = c(
      year = "^\\d+",
      " – ",
      event = ".*"
    )
  )
births <- tibble::tibble(births = bullets_in_headers[["Births"]]) |> 
  tidyr::separate_wider_regex(
    "births",
    patterns = c(
      year_birth = "^\\d+",
      " – ",
      person = ".*"
    )
  ) |> 
  tidyr::separate_wider_regex(
    "person",
    patterns = c(
      person = "[^(]*",
      "\\(d\\. ",
      "(?:February 29, )*",
      year_death = "\\d+",
      "\\)\\.?"
    ),
    too_few = "align_start"
  ) |> 
  tidyr::separate_wider_regex(
    "person",
    patterns = c(
      person = "[^,]*",
      ", ",
      description = ".*"
    ),
    too_few = "align_start"
  )

deaths <- tibble::tibble(deaths = bullets_in_headers[["Deaths"]]) |> 
  tidyr::separate_wider_regex(
    "deaths",
    patterns = c(
      year_death = "^\\d+",
      " – ",
      person = ".*"
    )
  ) |> 
  tidyr::separate_wider_regex(
    "person",
    patterns = c(
      person = "[^(]*",
      "\\(b\\. ",
      "(?:February 29, )*",
      year_birth = "\\d+",
      "\\)\\.?"
    ),
    too_few = "align_start"
  ) |> 
  tidyr::separate_wider_regex(
    "person",
    patterns = c(
      person = "[^,]*",
      ", ",
      description = ".*"
    ),
    too_few = "align_start"
  )

readr::write_csv(
  events,
  fs::path(working_dir, "events.csv")
)
readr::write_csv(
  births,
  fs::path(working_dir, "births.csv")
)
readr::write_csv(
  deaths,
  fs::path(working_dir, "deaths.csv")
)
```
