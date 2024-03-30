# 财务赞助商

TidyTuesday是由[R4DS在线学习社区](https://r4ds.io)组织的。
我们目前（当这个数据集在2024年3月首次发布时）正在寻找一个新的财务赞助商。
请参阅[我们的捐赠页面](https://r4ds.io/donate.html)了解有关我们当前努力的详细信息。

为了帮助我们的搜索，本周的数据集来自[财务赞助商目录](https://fiscalsponsordirectory.org/?page_id=1330)。
数据相当混乱，但您可能可以从中提取更多信息。

财务赞助商目录在[2023年3月分析了他们的目录](https://fiscalsponsordirectory.org/?page_id=95)。

- 数据是否有所改变？
- 您能否识别出其他趋势，或者更清晰地表示它们？
- 您能否找到对R4DS在线学习社区特别有吸引力的赞助商？R4DS在线学习社区是一个“多元化、友好和包容性的数据科学学习者和从业者社区”（总部位于美国德克萨斯州奥斯汀的全球社区）？

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-03-12')
## OR
tuesdata <- tidytuesdayR::tt_load(2024, week = 11)

fiscal_sponsor_directory <- tuesdata$fiscal_sponsor_directory


# 选项 2: 直接从 GitHub 读取

fiscal_sponsor_directory <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-03-12/fiscal_sponsor_directory.csv')

```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该在数据中得出**因果关系**的结论。影响所有数据的各种调节变量，其中许多可能没有在这些数据集中捕获。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `fiscal_sponsor_directory.csv`

|variable                           |class     |description                        |
|:----------------------------------|:---------|:----------------------------------|
|details_url                        |character |The source of the information about this sponsor. |
|name                               |character |The name of this fiscal sponsor. |
|website                            |character |The URL for this sponsor's website. |
|year_501c3                         |integer   |The year in which this sponsor became a 501(c)(3) charitable organization. |
|year_fiscal_sponsor                |integer   |The year in which this organization first acted as a fiscal sponsor. |
|n_sponsored                        |integer   |The number of projects sponsored by this sponsor. |
|fiscal_sponsorship_fee_description |character |Free text describing the fees associated with fiscal sponsorship by this organization. |
|eligibility_criteria               |character |Free text describing requirements for an organization to be sponsored by this sponsor. This field contains multiple values, concatenated with "|" (see below).|
|project_types                      |character |Free text describing the types of projects that this organization sponsors. This field contains multiple values, concatenated with "|" (see below). |
|services                           |character |The services offered by this sponsor. This field contains multiple values, concatenated with "|" (see below). |
|fiscal_sponsorship_model           |character |The model(s) to which this sponsor belongs. The models are described in "Fiscal Sponsorship: 6 Ways to Do It Right", sold by fiscalsponsordirectory.org. This field contains multiple values, concatenated with "|" (see below). |
|description                        |character |A free text description of this organization. |


### 清理脚本

注意：要将 `eligibility_criteria`、`project_types`、`services` 和 `fiscal_sponsorship_model` 字段分隔成列表列，您可以使用以下代码：

``` r
fiscal_sponsor_directory |> 
  dplyr::mutate(
    dplyr::across(
      c(eligibility_criteria, project_types, services, fiscal_sponsorship_model),
      \(col) {
        stringr::str_split(col, "\\|")
      }
    )
  )
```

``` r
library(tidyverse)
library(rlang)
library(rvest)
library(here)
library(fs)

working_dir <- here::here("data", "2024", "2024-03-12")

fiscal_sponsors_list_url <- "https://fiscalsponsordirectory.org/?page_id=1330"

# Read the HTML once so we don't have to keep hitting it.
fiscal_sponsors_list_html <- rvest::read_html(fiscal_sponsors_list_url)

fiscal_sponsor_names <- fiscal_sponsors_list_html |> 
  rvest::html_elements("#main-content a") |>   
  rvest::html_text2() |> 
  stringr::str_trim()

fiscal_sponsor_urls <- fiscal_sponsors_list_html |> 
  rvest::html_elements("#main-content a") |> 
  rvest::html_attr("href") |> 
  # Some of them randomly have "&preview=true" appended, but it doesn't do
  # anything useful.
  stringr::str_remove("&preview=true$")

fiscal_sponsors <- tibble::tibble(
  name = fiscal_sponsor_names,
  details_url = fiscal_sponsor_urls
) |> 
  dplyr::distinct() |> 
  dplyr::summarize(
    name = paste(name, collapse = ""),
    .by = details_url
  )

text_after_heading <- function(these_texts,
                               heading_regex,
                               heading_regex_remove = heading_regex) {
  this_text <- these_texts |>
    stringr::str_subset(paste0(heading_regex, "\\S+")) |>
    # One had a weird repeat.
    stringr::str_remove(heading_regex_remove)
  
  if (!length(this_text)) {
    this_text_n <- these_texts |> 
      stringr::str_which(heading_regex)
    if (length(this_text_n)) {
      this_text <- these_texts[[this_text_n + 1]]
    } else {
      this_text <- NA_character_
    }
  }
  this_text <- unique(this_text)
  
  if (!all(is.na(this_text))) {
    this_text <- paste(this_text, collapse = "\n")
  }
  
  return(this_text)
}

fiscal_sponsor_details <- fiscal_sponsors$details_url |> 
  purrr::map(
    \(details) {
      this_page <- rlang::try_fetch(
        rvest::read_html(details),
        error = function(e) {
          NULL
        }
      )
      if (is.null(this_page)) {
        return(tibble::tibble(details_url = details))
      }
      
      # The pages all have a logical structure, but the data doesn't know about
      # the structure. We need to extract things back out.
      these_texts <- this_page |> 
        rvest::html_elements(".entry-content p, .entry-content div") |> 
        rvest::html_text2() |> 
        stringr::str_subset(".+") |> 
        unique()
      these_uls <- this_page |> 
        rvest::html_elements(".entry-content ul") |> 
        rvest::html_text2()
      
      website <- these_texts |> 
        stringr::str_subset(
          "Website:\\s*"
        ) |>
        stringr::str_extract(
          "Website:\\s*(\\S+)",
          1
        ) |> 
        stringr::str_remove("^https?://") |>
        unique()
      year_501c3 <- these_texts |> 
        stringr::str_subset(
          "Year organization became a 501\\(c\\)\\(3\\):\\s*"
        ) |>
        stringr::str_extract(
          "Year organization became a 501\\(c\\)\\(3\\):\\s*(\\d+)",
          1
        ) |> 
        as.integer() |> 
        unique()
      year_fiscal_sponsor <- these_texts |> 
        stringr::str_subset(
          "Year of first fiscal sponsorship:\\s*"
        ) |>
        stringr::str_extract(
          "Year of first fiscal sponsorship:\\s*(\\d+)",
          1
        ) |> 
        as.integer() |> 
        unique()
      n_sponsored <- these_texts |> 
        stringr::str_subset(
          "Number of sponsored projects:\\s*"
        ) |>
        stringr::str_extract(
          "Number of sponsored projects:\\s*(\\d+)",
          1
        ) |> 
        as.integer() |> 
        unique()
      fiscal_sponsorship_fee_description <- text_after_heading(
        these_texts,
        "^(Fiscal sponsorship fee)|(Fees)",
        "^(((Fiscal sponsorship fee)|(Fees))(:?)(\\s*))+"
      )
      organization_description <- text_after_heading(
        these_texts, 
        "^(Project )?Organization ([dD]escription:)?\\s*"
      )
      
      if (length(these_uls)) {
        eligibility_criteria <- NA_character_
        project_types <- NA_character_
        services <- NA_character_
        fiscal_sponsorship_model <- NA_character_
        n <- 1L
        if (
          any(stringr::str_detect(these_texts, "Eligibility criteria:")) &&
          length(these_uls) >= n
        ) {
          eligibility_criteria <- these_uls[[n]]
          n <- n + 1L
        }
        if (
          any(stringr::str_detect(these_texts, "Types of projects or services we sponsor:")) &&
          length(these_uls) >= n
        ) {
          project_types <- these_uls[[n]]
          n <- n + 1L
        }
        if (
          any(stringr::str_detect(these_texts, "Services we offer projects:")) &&
          length(these_uls) >= n
        ) {
          services <- these_uls[[n]]
          n <- n + 1L
        }
        if (
          any(stringr::str_detect(these_texts, "Based on Fiscal Sponsorship: 6 Ways to Do It Right")) &&
          length(these_uls) >= n
        ) {
          fiscal_sponsorship_model <- these_uls[[n]]
          n <- n + 1L
        }
      } else {
        eligibility_criteria <- text_after_heading(
          these_texts,
          "^Eligibility criteria:\\s*"
        )
        project_types <- text_after_heading(
          these_texts,
          "^Types of projects or services we sponsor:\\s*"
        )
        services <- text_after_heading(
          these_texts,
          "Services we offer projects:\\s*"
        )
        fiscal_sponsorship_model <- text_after_heading(
          these_texts,
          "Based on Fiscal Sponsorship: 6 Ways to Do It Right: Our model\\(s\\) of fiscal sponsorship are:\\s*"
        )
      }
      
      # Work on the assumption that all are the same until proven otherwise.
      tibble::tibble(
        details_url = details,
        website = website,
        year_501c3 = year_501c3,
        year_fiscal_sponsor = year_fiscal_sponsor,
        n_sponsored = n_sponsored,
        fiscal_sponsorship_fee_description = fiscal_sponsorship_fee_description,
        eligibility_criteria = eligibility_criteria,
        project_types = project_types,
        services = services,
        fiscal_sponsorship_model = fiscal_sponsorship_model,
        description = organization_description
      )
    }
  ) |> 
  purrr::list_rbind()

fiscal_sponsor_details <- fiscal_sponsor_details |> 
  dplyr::mutate(
    eligibility_criteria = stringr::str_split(eligibility_criteria, "\\n"),
    project_types = stringr::str_split(project_types, "\\n"),
    services = stringr::str_split(services, "\\n"),
    fiscal_sponsorship_model = stringr::str_split(fiscal_sponsorship_model, "\\n")
  )

fiscal_sponsor_directory <- fiscal_sponsors |> 
  dplyr::left_join(fiscal_sponsor_details) |> 
  # Recombine the list columns to save as CSV.
  dplyr::mutate(
    dplyr::across(
      c(eligibility_criteria, project_types, services, fiscal_sponsorship_model),
      \(col) {
        purrr::map_chr(
          col,
          \(x) {
            paste(x, collapse = "|")
          }
        )
      }
    )
  )

readr::write_csv(
  fiscal_sponsor_directory,
  fs::path(working_dir, "fiscal_sponsor_directory.csv")
)
```
