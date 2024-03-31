# 编程语言

本周的数据来自[编程语言数据库](https://pldb.com/index.html)。感谢[Jesus M. Castagnetto](https://github.com/rfordatascience/tidytuesday/issues/530)的建议！

PLDB有一个[博客](https://pldb.com/posts/index.html)，其中有许多文章探讨了数据，比如[每种编程语言都支持行注释吗？](https://pldb.com/posts/does-every-programming-language-support-line-comments.html)。

数据是由用户提交的，所以在确定任何特别令人惊讶的发现之前，您可能需要确认其准确性！

### 在此处获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# 可以使用 ISO-8601 日期或年/周

tuesdata <- tidytuesdayR::tt_load('2023-03-21')
tuesdata <- tidytuesdayR::tt_load(2023, week = 12)

languages <- tuesdata$languages

# 或者手动读取数据

languages <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-03-21/languages.csv')
```

### 数据字典

# `languages.csv`

The [full data dictionary](https://pldb.com/csv.html) is available from PLDB.com.

|variable                          |class     |description |
|:---------------------------------|:---------|:-----------|
|pldb_id                           |character |A standardized, uniquified version of the language name, used as an ID on the PLDB site.|
|title                             |character |The official title of the language.|
|description                       |character |Description of the repo on GitHub.|
|type                              |character |Which category in PLDB's subjective ontology does this entity fit into.|
|appeared                          |double    |What year was the language publicly released and/or announced?|
|creators                          |character |Name(s) of the original creators of the language delimited by " and "|
|website                           |character |URL of the official homepage for the language project.|
|domain_name                       |character |If the project website is on its own domain.|
|domain_name_registered            |double    |When was this domain first registered?|
|reference                         |character |A link to more info about this entity.|
|isbndb                            |double    |Books about this language from [ISBNdb](https://isbndb.com/).|
|book_count                        |double    |Computed; the number of books found for this language at isbndb.com|
|semantic_scholar                  |integer   |Papers about this language from Semantic Scholar.|
|language_rank                     |double    |Computed; A rank for the language, taking into account various online rankings. The computation for this column is not currently clear.|
|github_repo                       |character |URL of the official GitHub repo for the project if it hosted there.|
|github_repo_stars                 |double    |How many stars of the repo?|
|github_repo_forks                 |double    |How many forks of the repo?|
|github_repo_updated               |double    |What year was the last commit made?|
|github_repo_subscribers           |double    |How many subscribers to the repo?|
|github_repo_created               |double    |When was the *Github repo* for this entity created?|
|github_repo_description           |character |Description of the repo on GitHub.|
|github_repo_issues                |double    |How many isses on the repo?|
|github_repo_first_commit          |double    |What year the first commit made in this git repo?|
|github_language                   |character |GitHub has a set of supported languages as defined [here](https://raw.githubusercontent.com/github/linguist/master/lib/linguist/languages.yml)|
|github_language_tm_scope          |character |The TextMate scope that represents this programming language.|
|github_language_type              |character |Either data, programming, markup, prose, or nil.|
|github_language_ace_mode          |character |A String name of the Ace Mode used for highlighting whenever a file is edited. This must match one of the filenames in http://git.io/3XO_Cg. Use "text" if a mode does not exist.|
|github_language_file_extensions   |character |An Array of associated extensions (the first one is considered the primary extension, the others should be listed alphabetically).|
|github_language_repos             |double    |How many repos for this language does GitHub report?|
|wikipedia                         |character |URL of the entity on Wikipedia, if and only if it has a page dedicated to it.|
|wikipedia_daily_page_views        |double    |How many page views per day does this Wikipedia page get? Useful as a signal for rankings. Available via WP api.|
|wikipedia_backlinks_count         |double    |How many pages on WP link to this page?|
|wikipedia_summary                 |character |What is the text summary of the language from the Wikipedia page?|
|wikipedia_page_id                 |double    |Waht is the internal ID for this entity on WP?|
|wikipedia_appeared                |double    |When does Wikipedia claim this entity first appeared?|
|wikipedia_created                 |double    |When was the *Wikipedia page* for this entity created?|
|wikipedia_revision_count          |double    |How many revisions does this page have?|
|wikipedia_related                 |character |What languages does Wikipedia have as related?|
|features_has_comments             |logical   |Does this language have a comment character?|
|features_has_semantic_indentation |logical   |Does indentation have semantic meaning in this language?|
|features_has_line_comments        |logical   |Does this language support inline comments (as opposed to comments that must span an entire line)?|
|line_comment_token                |character |Defined as a token that can be placed anywhere on a line and starts a comment that cannot be stopped except by a line break character or end of file.|
|last_activity                     |double    |Computed; The most recent of any year field in the PLDB for this language.|
|number_of_users                   |double    |Computed; "Crude user estimate from a linear model.|
|number_of_jobs                    |double    |Computed; The estimated number of job openings for programmers in this language.|
|origin_community                  |character |In what community(ies) did the language first originate?|
|central_package_repository_count  |double    |Number of packages in a central repository. If this value is not known, it is set to 0 (so "0" can mean "no repository exists", "the repository exists but is empty" (unlikely), or "we do not know if a repository exists". This value is definitely incorrect for R.|
|file_type                         |character |What is the file encoding for programs in this language?|
|is_open_source                    |logical   |Is it an open source project?|


### Cleaning Script

```r
library(tidyverse)
library(janitor)
library(knitr)

languages_url <- "https://pldb.com/languages.csv"

languages_raw <- read_csv(
  languages_url, 
  # Some of the columns are very sparse, so have readr use everything for
  # guessing.
  guess_max = 4303
) |> 
  clean_names() |>
  # The semantic_scholar column is misformed for a handful of languages. It's ok
  # to introduce NAs here.
  mutate(
    semantic_scholar = as.integer(semantic_scholar)
  )
  

# Almost all columns are almost completely empty. Keep the columns that have
# more than 10% coverage.
good_lang_cols <- languages_raw |> 
  summarize(
    across(everything(), ~sum(!is.na(.x)))
  ) |>
  tidyr::pivot_longer(
    everything(),
    names_to = "column",
    values_to = "non_empty"
  ) |> 
  mutate(
    coverage = non_empty/nrow(languages_raw)
  ) |> 
  filter(coverage > 0.1) |> 
  pull(column)

languages <- languages_raw |> 
  select(!!!good_lang_cols) |> 
  # This column references a site that doesn't want to be used in projects.
  select(-hopl) |> 
  # A couple columns are only relevant in the context of the mixed table with
  # non-languages included.
  select(-number_of_repos, -rank, -paper_count) |> 
  # Some columns are internal metadata that is no longer true with this subset.
  select(-fact_count, -example_count) |> 
  # I looked at R specifically, and the "country" column was inaccurate. Let's
  # not confuse people with known bad data.
  select(-country) |> 
  # Organize the columns.
  select(
    pldb_id,
    title,
    description,
    type,
    appeared,
    creators,
    website,
    starts_with("domain_name"),
    reference,
    isbndb,
    book_count,
    semantic_scholar,
    language_rank,
    starts_with("github_"),
    starts_with("wikipedia"),
    starts_with("features_"),
    line_comment_token,
    everything()
  )

write_csv(
  languages, 
  file = here::here(
    "data",
    "2023",
    "2023-03-21",
    "languages.csv"
  )
)

# Use the online dictionary to help with the dictionary in the post.
dictionary_url <- "https://pldb.com/columns.csv"
dictionary <- read_csv(dictionary_url) |> 
  clean_names() |> 
  # I only need the column name and description for our dictionary.
  select(column, description) |> 
  # I cleaned the column names, so let's do the same here.
  mutate(
    column = make_clean_names(column)
  ) |> 
  # We don't need the extras.
  filter(
    column %in% colnames(languages)
  )
# Arrange dictionary to match the order of colnames(languages).
dictionary <- dictionary[match(colnames(languages), dictionary$column), ]

dictionary |> 
  mutate(
    class = map_chr(languages, typeof)
  ) |> 
  select(
    variable = column,
    class,
    description
  ) |> 
  knitr::kable()
```
