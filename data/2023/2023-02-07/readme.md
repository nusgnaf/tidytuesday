# 大科技股票价格

本周的数据来自[Kaggle](https://www.kaggle.com/datasets/evangower/big-tech-stock-prices)（由[Evan Gower](https://github.com/rfordatascience/tidytuesday/issues/509)提供），数据来源于Yahoo Finance。

> 此数据集包含了14家不同科技公司的日股票价格和成交量，包括苹果（AAPL）、亚马逊（AMZN）、谷歌母公司（GOOGL）、Meta Platforms（META）等等！

许多文章都研究了“大科技”股票价格的崩溃，包括[morningstar.com的这篇文章](https://www.morningstar.com/articles/1129535/5-charts-on-big-tech-stocks-collapse)。

注意：所有的`stock_symbol`都有3271个价格，除了META（2688个）和TSLA（3148个），因为它们在检查期间的部分时间内没有公开交易。

### 在此处获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# 可以使用ISO-8601日期或年/周

tuesdata <- tidytuesdayR::tt_load('2023-02-07')
tuesdata <- tidytuesdayR::tt_load(2023, week = 6)

big_tech_stock_prices <- tuesdata$big_tech_stock_prices
big_tech_companies <- tuesdata$big_tech_companies

# 或者手动读取数据

big_tech_stock_prices <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-07/big_tech_stock_prices.csv')
big_tech_companies <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-07/big_tech_companies.csv')
```

### 数据字典

# `big_tech_stock_prices.csv`

|variable     |class     |description  |
|:------------|:---------|:------------|
|stock_symbol |character |stock_symbol |
|date         |double    |date         |
|open         |double    |The price at market open.|
|high         |double    |The highest price for that day.|
|low          |double    |The lowest price for that day.|
|close        |double    |The price at market close, adjusted for splits.|
|adj_close    |double    |The closing price after adjustments for all applicable splits and dividend distributions. Data is adjusted using appropriate split and dividend multipliers, adhering to Center for Research in Security Prices (CRSP) standards.|
|volume       |double    |The number of shares traded on that day.|

# `big_tech_companies.csv`

|variable     |class     |description  |
|:------------|:---------|:------------|
|stock_symbol |character |stock_symbol |
|company      |character |Full name of the company.|


### Cleaning Script

```r
library(fs)
library(tidyverse)
library(here)
library(janitor)

# Source for datasets. The datasets were downloaded and extracted to an
# "archive" folder within the working directory for processing, but they are not
# included in this repo.
"https://www.kaggle.com/datasets/evangower/big-tech-stock-prices"

# This is mostly equivalent to fs::dir_map, but we need to keep the info from
# the filename.
big_tech_stock_prices_list <- purrr::map(
  fs::dir_ls(
    here::here("data", "2023", "2023-02-07", "archive"),
    glob = "*.csv"
  ),
  \(path) {
    ticker <- fs::path_file(path) |> fs::path_ext_remove()
    readr::read_csv(
      file = path,
      col_types = cols(
        Date = col_date(format = ""),
        Open = col_double(),
        High = col_double(),
        Low = col_double(),
        Close = col_double(),
        `Adj Close` = col_double(),
        Volume = col_double()
      )
    ) |> 
      dplyr::mutate(stock_symbol = ticker, .before = 1)
  }
)

big_tech_stock_prices <- purrr::list_rbind(big_tech_stock_prices_list) |> 
  janitor::clean_names()
dplyr::glimpse(big_tech_stock_prices)

readr::write_csv(
  big_tech_stock_prices,
  here::here(
    "data", "2023", "2023-02-07",
    "big_tech_stock_prices.csv"
  )
)

big_tech_stock_prices |> 
  dplyr::count(stock_symbol, sort = TRUE)


# Make a lookup for the symbols.
tibble::tibble(
  stock_symbol = c(
    "AAPL",
    "ADBE",
    "AMZN",
    "CRM",
    "CSCO",
    "GOOGL",
    "IBM",
    "INTC",
    "META",
    "MSFT",
    "NFLX",
    "NVDA",
    "ORCL",
    "TSLA"
  ),
  company = c(
    "Apple Inc.",
    "Adobe Inc.",
    "Amazon.com, Inc.",
    "Salesforce, Inc.",
    "Cisco Systems, Inc.",
    "Alphabet Inc.",
    "International Business Machines Corporation",
    "Intel Corporation",
    "Meta Platforms, Inc.",
    "Microsoft Corporation",
    "Netflix, Inc.",
    "NVIDIA Corporation",
    "Oracle Corporation",
    "Tesla, Inc."
  )
) |> 
  readr::write_csv(
    here::here(
      "data", "2023", "2023-02-07",
      "big_tech_companies.csv"
    )
  )
```
