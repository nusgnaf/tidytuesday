# 节日剧集

节日快乐！上周我们探索了["节日"电影](../2023-12-12/readme.md)。本周我们将探索"节日"电视剧集：即电视剧中标题含有"节日"、"圣诞"、"光明节"或"坎萨节"（或其变体）的单集！

本周的数据同样来自[互联网电影数据库（IMDb）](https://developer.imdb.com/non-commercial-datasets/)。我们没有使用完全相同数据集的文章，但您可能会从 TVTropes 的[节日剧集](https://tvtropes.org/pmwiki/pmwiki.php/Main/HolidayEpisode)文章中获得灵感。

## 数据

如果您想深入了解，可以使用以下代码下载更大的数据集：

```{r}
holiday_episodes <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-12-19/holiday_episodes.csv')
holiday_episode_genres <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-12-19/holiday_episode_genres.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该从数据中得出**因果关系**的结论。影响所有数据的各种调节变量很多，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用R或其他编程语言创建可视化、模型、[闪亮的应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `holiday_episodes.csv`

|variable               |class     |description            |
|:----------------------|:---------|:----------------------|
|tconst                 |character |alphanumeric unique identifier of the individual episode|
|parent_tconst          |character |alphanumeric unique identifier of the parent TV series|
|season_number          |double    |season number the episode belongs to|
|episode_number         |double    |episode number of the tconst in the TV series|
|primary_title   |character |the more popular title / the title used by the filmmakers on promotional materials at the point of release |
|original_title  |character |original title, in the original language |
|year            |double    |the release year of a title |
|runtime_minutes |double    |primary runtime of the title, in minutes |
|genres          |character |includes up to three genres associated with the title (comma-delimited) |
|simple_title    |character |the title in lowercase, with punctuation removed, for easier filtering and grouping |
|average_rating  |double    |weighted average of all the individual user ratings on IMDb |
|num_votes       |double    |number of votes the title has received on IMDb (titles with fewer than 10 votes were not included in this dataset) |
|parent_title_type      |character |the type/format of the title ("tvMiniSeries" or "tvSeries"|
|parent_primary_title   |character |the more popular title / the title used by the filmmakers on promotional materials at the point of release (for the parent TV series)|
|parent_original_title  |character |original title, in the original language (for the parent TV series) |
|parent_start_year      |double    |the series start year|
|parent_end_year        |double    |the series end year|
|parent_runtime_minutes |double    |primary runtime of the TV series, in minutes |
|parent_genres          |character |includes up to three genres associated with the TV series|
|parent_simple_title    |character |the title in lowercase, with punctuation removed, for easier filtering and grouping (for the parent TV series)|
|parent_average_rating  |double    |weighted average of all the individual user ratings on IMDb (for the parent TV series) |
|parent_num_votes       |double    |number of votes the title has received on IMDb (for the parent TV series; titles with fewer than 10 votes were not included in this dataset) |
|christmas       |logical   |whether the episode title includes "christmas", "xmas", "x mas", etc|
|hanukkah        |logical   |whether the episode title includes "hanukkah", "chanukah", etc|
|kwanzaa         |logical   |whether the episode title includes "kwanzaa"|
|holiday         |logical   |whether the episode title includes the word "holiday"|

# `holiday_episode_genres.csv`

|variable |class     |description |
|:--------|:---------|:-----------|
|tconst   |character |alphanumeric unique identifier of the episode title |
|genres   |character |genres associated with the episode, one row per genre |


### Cleaning Script

``` r
library(tidyverse)
library(here)
library(fs)
library(janitor)

working_dir <- here::here("data", "2023", "2023-12-19")

imdb_ratings <- readr::read_tsv(
  "https://datasets.imdbws.com/title.ratings.tsv.gz",
  na = c("", "NA", "\\N")
) |> 
  janitor::clean_names() |> 
  dplyr::filter(num_votes >= 10)
imdb_titles <- readr::read_tsv(
  "https://datasets.imdbws.com/title.basics.tsv.gz",
  na = c("", "NA", "\\N")
) |> 
  janitor::clean_names() |> 
  # A handful of titles have miscoded data, which can be detected by cases where
  # the "isAdult" field has a value other than 0 or 1. That's convenient,
  # because I want to get rid of anything other than 0.
  dplyr::filter(is_adult == 0) |>
  dplyr::select(-is_adult) |>
  dplyr::mutate(
    # Create a column for easier title searching.
    simple_title = tolower(primary_title) |> 
      stringr::str_remove_all("[[:punct:]]")
  )

rated_titles <- imdb_titles |> 
  dplyr::inner_join(imdb_ratings, by = "tconst")

imdb_episodes <- readr::read_tsv(
  "https://datasets.imdbws.com/title.episode.tsv.gz",
  na = c("", "NA", "\\N")
) |> 
  janitor::clean_names()

holiday_episodes <- imdb_episodes |> 
  dplyr::inner_join(rated_titles, by = "tconst") |> 
  dplyr::select(-"title_type", -"end_year") |>
  dplyr::rename("year" = "start_year") |>
  dplyr::inner_join(
    dplyr::rename_with(rated_titles, ~ paste0("parent_", .x)),
    by = "parent_tconst"
  ) |> 
  dplyr::filter(
    stringr::str_detect(simple_title, "holiday") |
      stringr::str_detect(simple_title, "christmas") |
      stringr::str_detect(simple_title, "\\bx mas\\b") |
      stringr::str_detect(simple_title, "xmas") |
      # Let's catch both "Hanukkah" and "Chanukah" (and some other variants
      # while we're at it)
      stringr::str_detect(simple_title, "hanuk") | 
      stringr::str_detect(simple_title, "kwanzaa")
  ) |> 
  dplyr::mutate(
    christmas = stringr::str_detect(simple_title, "christmas") | 
      stringr::str_detect(simple_title, "x *mas"),
    hanukkah = stringr::str_detect(simple_title, "hanuk"),
    kwanzaa = stringr::str_detect(simple_title, "kwanzaa"),
    holiday = stringr::str_detect(simple_title, "holiday")
  )

holiday_episode_genres <- holiday_episodes |> 
  dplyr::select(tconst, genres) |> 
  tidyr::separate_longer_delim(genres, ",")

readr::write_csv(
  holiday_episodes,
  fs::path(working_dir, "holiday_episodes.csv")
)
readr::write_csv(
  holiday_episode_genres,
  fs::path(working_dir, "holiday_episode_genres.csv")
)
```
