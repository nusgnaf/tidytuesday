# 恐怖传说

万圣节快乐！为了庆祝，本周我们将探索来自[Snopes.com](https://www.snopes.com/)的恐怖传说！

> 由于都市传说通常是表达我们对似乎平静无波的世界表面下潜藏的危险的恐惧的一种方式，因此恐怖传说是都市民间传说中最丰富的内容之一并不足为奇。我们担心那些我们无力阻止的可怕事故，害怕那些随机选择受害者的无名杀手。我们无法保护自己免受悄悄潜入我们工作、玩耍和购物场所的有毒动物的侵害，也无法阻止昆虫的袭击，它们侵入我们的家园和我们的身体。我们对可能潜藏在我们食物中的污染物感到厌恶。我们害怕外国人和外国地方。我们担心孩子在充斥着毒品、绑架者和毒药的世界中的安全。我们永远不知道下一个拐角可能等待我们的是什么可怕的发现。即使我们设法逃脱了所有这些恐怖，我们自己的虚荣心也可能会毁了我们。

本周您可能希望深入了解文章的细节，特别是如果评级是“混合”。
每个观察都包括到该文章的URL，您可以使用 `utils::browseURL()` 函数直接从R中打开它。

## 数据

```{r}
# 选项1：tidytuesdayR 包
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-10-31')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 44)

horror_articles <- tuesdata$horror_articles

# 选项2：直接从 GitHub 读取

horror_articles <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-10-31/horror_articles.csv')
```

## 参与方式

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应在数据中得出关于**因果关系**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并考虑自己可能潜在的这些关系背后的细微差别。
- 使用R或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `horror_articles.csv`

|variable  |class     |description |
|:---------|:---------|:-----------|
|title     |character |The title of this article. |
|url       |character |The url for this article. |
|rating    |character |Whether the claim was found to be "false", "true", or somewhere in-between. |
|subtitle  |character |A subtitle for this article. |
|author    |character |The researcher who investigated this claim. |
|published |Date      |The date when the article first appeared on Snopes. |
|claim     |character |The claim being investigated. |

### Cleaning Script

``` r
library(tidyverse)
library(here)
library(fs)
library(rvest)

working_dir <- here::here("data", "2023", "2023-10-31")

urls <- paste0(
  "https://www.snopes.com/fact-check/category/horrors/?pagenum=",
  1:15
)

extract_rating <- function(article_page) {
  rating <- article_page |> 
    rvest::html_element(".rating_title_wrap") |> 
    rvest::html_text2() |> 
    stringr::str_remove("About this rating")
  if (is.na(rating)) {
    rating <- article_page |> 
      rvest::html_element(".status_color") |> 
      rvest::html_text2()
  }
  if (is.na(rating)) {
    rating <- article_page |> 
      rvest::html_elements("noindex") |> 
      rvest::html_text2() |> 
      stringr::str_squish() |> 
      stringr::str_subset("^Status:") |> 
      stringr::str_remove("Status:")
  }
  rating <- tolower(rating) |> 
    stringr::str_squish() |> 
    stringr::str_remove("\\.|\\:")
  rating <- dplyr::case_match(
    rating,
    c(
      "a number of real entries, one unknown, and one fiction",
      "multiple",
      "multiple — see below",
      "two real entries, the others are fiction"
    ) ~ "mixture",
    .default = rating
  )
  return(rating)
}

extract_claim <- function(article_page) {
  claim <- article_page |> 
    rvest::html_element(".claim_cont") |> 
    rvest::html_text2() |> 
    stringr::str_squish()
  if (is.na(claim)) {
    claim <- rvest::html_elements(article_page, "p") |> 
      rvest::html_text2() |> 
      stringr::str_subset("^Claim:") |> 
      stringr::str_remove("Claim:") |> 
      stringr::str_squish()
  }
  return(claim)
}

horror_articles <- urls |>
  purrr::map(
    \(article_list_url) {
      article_list_url |> 
        rvest::read_html() |> 
        rvest::html_elements(".article_wrapper") |> 
        purrr::map(
          \(article) {
            # Grabbbing info from this page can result in truncation. Instead grab the
            # URL and dig into that.
            url <- article |>
              rvest::html_element("a") |>
              rvest::html_attr("href")
            article_page <- rvest::read_html(url)
            tibble::tibble(
              title = article_page |>
                rvest::html_element("h1") |> 
                rvest::html_text2(),
              url = url,
              # Failed for some articles <= 2015-05-16
              rating = extract_rating(article_page),
              subtitle = article_page |>
                rvest::html_element("h2") |> 
                rvest::html_text2(),
              author = article_page |> 
                rvest::html_element(".author_name") |> 
                rvest::html_text() |> 
                stringr::str_squish(),
              published = article |> 
                rvest::html_element(".article_date") |> 
                rvest::html_text2() |> 
                lubridate::mdy(),
              # Failed for some articles <= 2015-05-16
              claim = extract_claim(article_page)
            )
          }
        ) |> 
        purrr::list_rbind()
    }
  ) |> 
  purrr::list_rbind()

readr::write_csv(
  horror_articles,
  fs::path(working_dir, "horror_articles.csv")
)
```
