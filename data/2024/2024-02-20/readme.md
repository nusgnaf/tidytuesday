# R联盟基础设施指导委员会 (ISC) 拨款

R联盟基础设施指导委员会(ISC)拨款计划将于2024年3月1日至4月1日再次接受提案（然后在秋季再次接受）。

> 这项举措是我们致力于加强和改进R生态系统的基石。我们资助为R社区的技术和社会基础设施做出贡献的项目。

在[他们宣布这一轮拨款的博文](https://www.r-consortium.org/blog/2024/02/08/r-consortium-infrastructure-steering-committee-isc-grant-program-accepting-proposals-starting-march-1st)中了解更多。

自2016年以来，R联盟ISC一直在授予拨款。

本周的数据是对过去拨款受益者的探索。

有哪些关键词在获奖拨款的标题或摘要中显著？
拨款金额随时间变化了吗？

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-02-20')
## OR
tuesdata <- tidytuesdayR::tt_load(2024, week = 8)

isc_grants <- tuesdata$isc_grants

# 选项 2: 直接从 GitHub 读取

isc_grants <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-02-20/isc_grants.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该在数据中得出**因果关系**的结论。影响所有数据的各种调节变量，其中许多可能没有在这些数据集中捕获。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `isc_grants.csv`

|variable    |class     |description         |
|:-----------|:---------|:-------------------|
|year        |integer   |The year in which the grant was awarded. |
|group       |integer   |Whether the grant was awarded in the spring cycle (1) or the fall cycle (2). |
|title       |character |The title of the project. |
|funded      |integer   |The dollar amount funded for the project. |
|proposed_by |character |The name of the person who requested the grant. |
|summary     |character |A description of the project. |
|website     |character |The website associated with the project, if available. |

### 数据清洗脚本

``` r
library(tidyverse)
library(janitor)
library(rlang)
library(rvest)
library(here)

working_dir <- here::here("data", "2024", "2024-02-20")

all_grants_url <- "https://www.r-consortium.org/all-projects/awarded-projects"
urls <-
  all_grants_url |> 
  rvest::read_html() |> 
  rvest::html_node(".main-content") |> 
  rvest::html_nodes("a") |> 
  rvest::html_attr("href") |> 
  stringr::str_subset(stringr::fixed("/projects/awarded-projects/")) |> 
  stringr::str_remove("#.+$") |> 
  unique() |> 
  rvest::url_absolute(all_grants_url)

get_grant_data <- function(url) {
  year <- as.integer(stringr::str_extract(url, "\\d{4}"))
  group <- as.integer(stringr::str_extract(url, "\\d$"))
  project_description_html <- 
    rvest::read_html(url) |> 
    rvest::html_nodes(".project-description") 
  titles <- 
    project_description_html |> 
    rvest::html_nodes("h3") |> 
    rvest::html_text2()
  other_data <- 
    project_description_html |> 
    purrr::map(parse_project_description) |> 
    purrr::list_rbind()
  
  tibble::tibble(
    year = year,
    group = group,
    title = titles,
    other_data
  )
}

parse_project_description <- function(project_description_html) {
  pieces <- project_description_html |> 
    rvest::html_nodes("p") |> 
    rvest::html_text2() |> 
    stringr::str_split(":\n")
  
  pieces <- purrr::reduce(
    pieces,
    \(x, y) {
      if (!rlang::is_named(x) && length(x) == 2) {
        x <- rlang::set_names(x[[2]], x[[1]])
      }
      if (length(y) == 2) {
        y <- rlang::set_names(y[[2]], y[[1]])
      } else {
        x[[length(x)]] <- paste(x[[length(x)]], y, sep = "\n")
        y <- NULL
      }
      return(c(x, y))
    }
  )
  
  pieces |> 
    tibble::enframe() |> 
    tidyr::pivot_wider(names_from = name, values_from = value)
}

isc_grants <- 
  urls |> 
  purrr::map(get_grant_data) |> 
  purrr::list_rbind() |> 
  janitor::clean_names() |> 
  dplyr::mutate(
    funded = stringr::str_remove(funded, "\\$") |> 
      stringr::str_remove(",") |> 
      as.integer(),
    website = dplyr::na_if(website, "")
  )

readr::write_csv(
  isc_grants,
  fs::path(working_dir, "isc_grants.csv")
)
```
