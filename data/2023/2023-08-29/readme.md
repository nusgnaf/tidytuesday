# 合理使用

本周的数据来自[美国版权办公室合理使用指数](https://www.copyright.gov/fair-use/fair-index.html)。

> 合理使用是美国版权法的一个长期而重要的方面。该指数的目标是通过呈现一个可搜索的法院意见数据库，包括按类别和使用类型（例如音乐、互联网/数字化、模仿）分类，使合理使用原则和应用对公众更加可访问和可理解。

本周有两个数据集，它们的行是对齐的，但值可能不会完全对齐，因此在实际数据处理中经常会遇到这种情况。

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-08-29')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 35)

fair_use_cases <- tuesdata$fair_use_cases
fair_use_findings <- tuesdata$fair_use_findings

# 选项 2: 直接从 GitHub 读取

fair_use_cases <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-08-29/fair_use_cases.csv')
fair_use_findings <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-08-29/fair_use_findings.csv')
```

## 参与方式

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应该在数据中得出**因果关系**的结论。有许多调节变量会影响所有数据，其中许多可能没有被这些数据集捕获到。因此，我们的建议是使用所提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 利用 R 或其他编程语言创建可视化、模型、[shiny 应用](https://shiny.posit.co/)或其他数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `fair_use_cases.csv`

|variable       |class     |description    |
|:--------------|:---------|:--------------|
|case           |character |The name and number of the case. |
|year           |integer   |The year in which the case was decided. |
|court          |character |The court in which the ruling was made. |
|jurisdiction   |character |The jurisdiction of that court. |
|categories     |character |A comma- or semicolon-separated list of categories to which the case belongs. These have *not* been normalized. |
|outcome        |character |A string describing the outcome of the case. |
|fair_use_found |logical   |Whether fair use was found by the court. `FALSE` might sometimes indicate a more complicated finding.|

# `fair_use_findings.csv`

|variable    |class     |description |
|:-----------|:---------|:-----------|
|title       |character |The title of the case. |
|case_number |character |The case number or numbers of the case. |
|year        |character |The year in which the finding was made (or findings were made). |
|court       |character |The court or courts involved. |
|key_facts   |character |The key facts of the case. |
|issue       |character |A brief description of the fair use issue. |
|holding     |character |The decision of the court in paragraph form. |
|tags        |character |Comma- or semicolon-separated tags for this case. |
|outcome     |character |A brief description of the outcome of the case. These fields have not been normalized. |

### Cleaning Script

``` r
# I started a package to deal with tables like those found in this week's data.
# Check it out at https://jonthegeek.github.io/tableguess

# remotes::install_github("jonthegeek/tableguess")
library(tableguess)
library(tidyverse)
library(stringi)
library(janitor)
library(pdftools)
library(withr)
library(rvest)
library(here)

## Explore ---------------------------------------------------------------------

# I explored 1 PDF to get my bearings. I show this here so the cleaning script
# makes more sense.

sample_pdf_url <- "https://www.copyright.gov/fair-use/summaries/de-fontbrune-v-wofsy-39-F-4th-1214-9th-Cir-2022.pdf"
sample_pdf_file <- withr::local_tempfile()
download.file(sample_pdf_url, sample_pdf_file, mode = "wb")

pdf_contents <- pdf_text(sample_pdf_file) |> 
  # stringi, which stringr wraps, has a nice function to split by \n.
  stringi::stri_split_lines1()
cat(pdf_contents, sep = "\n")

# The first 2 lines are the case title and number, let's grab those.
case_title <- stringr::str_squish(pdf_contents[[1]])
case_number <- stringr::str_squish(pdf_contents[[2]])
# Further structure: NUMBER (COURT YEAR_OR_DATE)

# The last line of the PDf is the same "Source" line in all of these PDFs.

# I created a simple function to make it easier to get rid of the stuff that we
# don't want in each PDF. In these I always want to get rid of the first 2 lines
# + potentially a blank line after that + the Source line.
remove_unused_lines <- function(vctr) {
  # Because the length of the title/case number(s) can vary, look for the row
  # that starts with Year.
  start_row <- stringr::str_which(
    vctr, "^ {0,3}Year"
  )[[1]]
  
  # Remove the "Source" line *and anything after it*, in case it spans 2 lines.
  source_line <- stringr::str_which(
    vctr, 
    stringr::fixed("Source: U.S. Copyright Office Fair Use Index.")
  )
  
  cleaned <- vctr[start_row:(source_line - 1)]
  
  # Get rid of any blank lines.
  cleaned |> 
    stringr::str_subset("^$", negate = TRUE)
}

pdf_contents |> 
  remove_unused_lines() |>
  tableguess::extract_table(orientation = "vertical") |> 
  tibble::as_tibble()

## Function to extract case data -----------------------------------------------

scrape_case_pdf <- function(pdf_url) {
  temp_filename <- withr::local_tempfile()
  download.file(pdf_url, temp_filename, mode = "wb")
  
  pdf_contents <- pdftools::pdf_text(temp_filename) |> 
    # Some span 2 pages.
    paste0(collapse = "") |> 
    stringi::stri_split_lines1()
  
  case_title <- stringr::str_squish(pdf_contents[[1]])
  
  # In at least one case there are multiple numbers. Take 2 through the "Year"
  # row.
  year_row <- stringr::str_which(pdf_contents, "^ {0,3}Year")[[1]]
  case_number <- stringr::str_squish(
    paste(pdf_contents[2:(year_row - 1)], collapse = " ")
  )
  
  extracted_tbl <- 
    pdf_contents |> 
    remove_unused_lines() |>
    # In a single (observed) case, the final row is missing the spaces present
    # on all other rows.
    stringr::str_replace(
      "^( {0,2})Outcome +",
      "\\1Outcome      "
    ) |> 
    tableguess::extract_table(orientation = "vertical") |> 
    tibble::as_tibble()
  
  # In at least one case, the "Key Facts" column looks like two columns to the
  # guesser, so I need to clean that up.
  if (all(colnames(extracted_tbl)[3:4] == c("Key", "Facts"))) {
    extracted_tbl$`Key Facts` <- paste(
      extracted_tbl$Key,
      extracted_tbl$Facts
    )
    extracted_tbl$Key <- NULL
    extracted_tbl$Facts <- NULL
  }
  
  # A couple cases have slightly weird column names, fix those.
  if ("Issue(s)" %in% colnames(extracted_tbl)) {
    extracted_tbl <- extracted_tbl |> 
      dplyr::rename("Issue" = "Issue(s)")
  }
  if ("Issues" %in% colnames(extracted_tbl)) {
    extracted_tbl <- extracted_tbl |> 
      dplyr::rename("Issue" = "Issues")
  }
  if ("Holding(s)" %in% colnames(extracted_tbl)) {
    extracted_tbl <- extracted_tbl |> 
      dplyr::rename("Holding" = "Holding(s)")
  }
  if ("Holdings" %in% colnames(extracted_tbl)) {
    extracted_tbl <- extracted_tbl |> 
      dplyr::rename("Holding" = "Holdings")
  }
  
  extracted_tbl |> 
    # Manually clean the names so we get an error if something is different.
    dplyr::rename(
      "year" = "Year",
      "court" = "Court",
      "key_facts" = "Key Facts",
      "issue" = "Issue",
      "holding" = "Holding",
      "tags" = "Tags",
      "outcome" = "Outcome"
    ) |> 
    dplyr::mutate(
      title = case_title,
      case_number = case_number,
      .before = "year" 
    )
}

# Add a catcher so we can parse most, then come back and clean up. I used this 
# to refine the script until it worked for all cases.
scrape_case_pdf_safe <- function(pdf_url) {
  rlang::try_fetch(
    scrape_case_pdf(pdf_url),
    error = function(cnd) {
      tibble::tibble(
        title = pdf_url,
        case_number = paste(cnd, collapse = "\n"),
        year = "error",
        court = "",
        issue = "",
        holdings = "",
        tags = "",
        outcome = "",
        key_facts = ""
      )
    }
  )
} 

scrape_case_pdf_safe(sample_pdf_url)

# Now I know enough to scrape!

## Scrape the data -------------------------------------------------------------

fair_use_index_url <- "https://www.copyright.gov/fair-use/fair-index.html"

# Get the bulk of the data using rvest. I like to grab the raw html separate
# from the pipe, so I don't hit the site over and over as I experiment.
raw_html <- rvest::read_html(fair_use_index_url)
raw_html

# There's only one table. We can get it cleanly by pulling out that one table
# before passing it to html_table.
fair_use_cases <- raw_html |> 
  rvest::html_element("table") |> 
  rvest::html_table() |> 
  janitor::clean_names() |> 
  dplyr::mutate(
    dplyr::across(
      c(case, court, jurisdiction, categories, outcome),
      stringr::str_squish
    ),
    # Case #60 has ", affirmed 2018" in the year, breaking the format. In a
    # real, automated process we'd probably deal with particular cases, or keep
    # the original version of the year as a separate column.
    year = stringr::str_extract(year, "\\d+") |> 
      as.integer(),
    # court has some inconsistencies, normalize
    court = stringr::str_replace(court, "Cir\\.", "Circuit") |> 
      stringr::str_replace(
        "E\\.D\\.N\\.Y$",
        "E.D.N.Y."
      ),
    # Likewise with jurisdiction
    jurisdiction = dplyr::case_match(
      jurisdiction,
      "D.C. Circuit" ~ "District of Columbia Circuit",
      "First Circuit" ~ "1st Circuit",
      "Second Circuit" ~ "2nd Circuit",
      "Third Circuit" ~ "3rd Circuit",
      "Fourth Circuit" ~ "4th Circuit",
      "Fifth Circuit" ~ "5th Circuit",
      "Sixth Circuit" ~ "6th Circuit",
      "Seventh Circuit" ~ "7th Circuit",
      "Eighth Circuit" ~ "8th Circuit",
      "Ninth Circuit" ~ "9th Circuit",
      "Tenth Circuit" ~ "10th Circuit",
      "Eleventh Circuit" ~ "11th Circuit",
      .default = jurisdiction
    ),
    # The outcome column has some specific info that we won't always deal with,
    # but let's pull out the straightforward yes/no part of that.
    fair_use_found = stringr::str_detect(
      tolower(outcome),
      "fair use found"
    )
    # I don't clean categories here, but it might be a useful thing to do!
  )
dplyr::glimpse(fair_use_cases)

# We need to get the PDF URLs. Two cases have 2 PDF links each, but they turned
# out to be to the same file. Still, let's be careful to only grab the first URL
# in each row!
pdf_urls <-
  raw_html |> 
  rvest::html_element("table") |> 
  # We want the first TD (cell) in each row, and then the first A (link) in each
  # of those.
  rvest::html_elements("tr>td:first-of-type>a:first-of-type") |>
  rvest::html_attr("href") |> 
  utils::URLencode() |> 
  rvest::url_absolute(fair_use_index_url)

stopifnot(
  "PDF/Case mismatch" = length(pdf_urls) == nrow(fair_use_cases)
)

fair_use_findings <-
  pdf_urls |>
  purrr::map(scrape_case_pdf_safe) |> 
  purrr::list_rbind()

write_csv(
  fair_use_cases,
  here::here(
    "data",
    "2023",
    "2023-08-29",
    "fair_use_cases.csv"
  )
)

write_csv(
  fair_use_findings,
  here::here(
    "data",
    "2023",
    "2023-08-29",
    "fair_use_findings.csv"
  )
)
```
