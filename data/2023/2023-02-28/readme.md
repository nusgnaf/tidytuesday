# 非洲语言情感分析

本周的数据来自 [AfriSenti: Sentiment Analysis dataset for 14 African languages](https://github.com/afrisenti-semeval/afrisent-semeval-2023)，通过 [@shmuhammad2004](https://github.com/shmuhammad2004)（[相关论文](https://arxiv.org/pdf/2302.08956.pdf)的通讯作者，也是 [R4DS Online Learning Community Slack](https://r4ds.io/join) 的活跃成员）。

> 这个存储库包含了 SemEval 2023 共享任务 12 的数据：非洲语言情感分析（AfriSenti-SemEval）。

源代码库还包括几种语言的情感词典。

### 在此处获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# 可以使用 ISO-8601 日期或年/周

tuesdata <- tidytuesdayR::tt_load('2023-02-28')
tuesdata <- tidytuesdayR::tt_load(2023, week = 9)

afrisenti <- tuesdata$afrisenti
languages <- tuesdata$languages
language_scripts <- tuesdata$language_scripts
language_countries <- tuesdata$language_countries
country_regions <- tuesdata$country_regions

# 或者手动读取数据

afrisenti <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-28/afrisenti.csv')
languages <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-28/languages.csv')
language_scripts <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-28/language_scripts.csv')
language_countries <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-28/language_countries.csv')
country_regions <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-28/country_regions.csv')
```

### 数据字典

# `afrisenti.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|language_iso_code |character |The unique code used to identify the language |
|tweet             |character |The text content of a tweet |
|label             |character |A sentiment label of positive, negative, or neutral assigned by a native speaker of that language |
|intended_use      |character |Whether the data came from the dev, test, or train set for that language |

# `languages.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|language_iso_code |character |The unique code used to identify the language |
|language          |character |The name of the language |

# `language_scripts.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|language_iso_code |character |The unique code used to identify the language |
|script            |character |The script used to write the language |

# `language_countries.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|language_iso_code |character |The unique code used to identify the language |
|country           |character |A country in which the language is spoken |

# `country_regions.csv`

|variable |class     |description |
|:--------|:---------|:-----------|
|country  |character |A country in which the language is spoken |
|region   |character |The region of Africa in which that country is categorized. Note that Mozambique is categorized as "East Africa", "Southern Africa", and "Southeastern Africa" |


### Cleaning Script

```r
library(tidyverse)

# Read in the data
language_abbrevs <- c(
  "amh", "arq", "ary", 
  "hau", "ibo", "kin",
  "oro", "pcm", "por",
  "swa", "tir", "tso", 
  "twi", "yor"
)
# amh == ama, por == pt-MZ, oro == orm

splits <- c("dev", "test", "train")

afrisenti <- purrr::map(
  language_abbrevs,
  \(language_abbr) {
    purrr::map(
      splits,
      \(split) {
        readr::read_tsv(
          paste0(
            "https://raw.githubusercontent.com/afrisenti-semeval/afrisent-semeval-2023/main/data/",
            language_abbr, "/", split, ".tsv"
          )
        ) |> 
          dplyr::mutate(
            language_abbreviation = language_abbr,
            intended_use = split
          )
      }
    ) |> 
      purrr::list_rbind()
  }
) |> 
  purrr::list_rbind()

glimpse(afrisenti)

# oro has an extra column.
afrisenti |> 
  dplyr::filter(!is.na(ID)) |> 
  dplyr::count(language_abbreviation)

# Drop the extra column, and arrange the columns. Also recode the
# language_abbreviation to formal ISO codes.
afrisenti <- afrisenti |> 
  dplyr::mutate(
    language_iso_code = dplyr::recode(
      language_abbreviation,
      por = "pt-MZ",
      oro = "orm"
    )
  ) |> 
  dplyr::select(
    language_iso_code,
    tweet,
    label,
    intended_use
  ) |> 
  dplyr::arrange(language_iso_code, label, intended_use)

# Also include information about the languages. Start with Table 2 from
# https://arxiv.org/pdf/2302.08956.pdf but make it tidy.
languages_untidy <- tibble::tribble(
  ~language, ~language_iso_code, ~region, ~country, ~script,
  "Amharic", "amh", "East Africa", "Ethiopia", "Ethiopic",
  "Algerian Arabic/Darja", "arq", "North Africa", "Algeria", "Arabic",
  "Hausa", "hau", "West Africa", "Nigeria", "Latin",
  "Hausa", "hau", "West Africa", "Ghana", "Latin",
  "Hausa", "hau", "West Africa", "Cameroon", "Latin",
  "Igbo", "ibo", "West Africa", "Nigeria", "Latin",
  "Kinyarwanda", "kin", "East Africa", "Rwanda", "Latin",
  "Moroccan Arabic/Darija", "ary", "Northern Africa", "Morocco", "Arabic",
  "Moroccan Arabic/Darija", "ary", "Northern Africa", "Morocco", "Latin",
  "Mozambican Portuguese", "pt-MZ", "Southeastern Africa", "Mozambique", "Latin",
  "Nigerian Pidgin", "pcm", "West Africa", "Nigeria", "Latin",
  "Nigerian Pidgin", "pcm", "West Africa", "Ghana", "Latin",
  "Nigerian Pidgin", "pcm", "West Africa", "Cameroon", "Latin",
  "Oromo", "orm", "East Africa", "Ethiopia", "Latin",
  "Swahili", "swa", "East Africa", "Tanzania", "Latin",
  "Swahili", "swa", "East Africa", "Kenya", "Latin",
  "Swahili", "swa", "East Africa", "Mozambique", "Latin",
  "Tigrinya", "tir", "East Africa", "Ethiopia", "Ethiopic",
  "Twi", "twi", "West Africa", "Ghana", "Latin",
  "Xitsonga", "tso", "Southern Africa", "Mozambique", "Latin",
  "Xitsonga", "tso", "Southern Africa", "South Africa", "Latin",
  "Xitsonga", "tso", "Southern Africa", "Zimbabwe", "Latin",
  "Xitsonga", "tso", "Southern Africa", "Eswatini", "Latin",
  "Yorùbá", "yor", "West Africa", "Nigeria", "Latin"
)

languages <- languages_untidy |> 
  dplyr::distinct(language_iso_code, language) |> 
  dplyr::arrange(language_iso_code, language)

language_countries <- languages_untidy |> 
  dplyr::distinct(language_iso_code, country) |> 
  dplyr::arrange(language_iso_code, country)

language_scripts <- languages_untidy |> 
  dplyr::distinct(language_iso_code, script) |> 
  dplyr::arrange(language_iso_code, script)

country_regions <- languages_untidy |> 
  dplyr::distinct(country, region) |> 
  dplyr::arrange(country, region)

# Save the data.
write_csv(
  afrisenti,
  here::here(
    "data", "2023", "2023-02-28",
    "afrisenti.csv"
  )
)

write_csv(
  languages,
  here::here(
    "data", "2023", "2023-02-28",
    "languages.csv"
  )
)

write_csv(
  language_scripts,
  here::here(
    "data", "2023", "2023-02-28",
    "language_scripts.csv"
  )
)

write_csv(
  language_countries,
  here::here(
    "data", "2023", "2023-02-28",
    "language_countries.csv"
  )
)

write_csv(
  country_regions,
  here::here(
    "data", "2023", "2023-02-28",
    "country_regions.csv"
  )
)
```
