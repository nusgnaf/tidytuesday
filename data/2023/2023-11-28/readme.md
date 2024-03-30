# Doctor Who剧集

Doctor Who是一部历史悠久的英国电视节目。该节目于2005年复播，并自那时以来一直非常受欢迎。为了庆祝今年Doctor Who的60周年，我们有三个数据集。

本周的数据来自维基百科的[Doctor Who剧集列表](https://en.wikipedia.org/wiki/List_of_Doctor_Who_episodes_(2005%E2%80%93present))，通过[Jonathan Kitt](https://github.com/KittJonathan/datardis)的[{datardis}包](https://cran.r-project.org/package=datardis)。感谢Jonathan编译和分享这些数据！

截至2023年11月24日，该数据仅包括“复播”时代的剧集。为了增加挑战，考虑向{datardis}包提交拉取请求，更新[数据提取脚本](https://github.com/KittJonathan/datardis/tree/main/misc)，以获取“经典”时代的数据！

## 数据

```{r}
# 选项1：tidytuesdayR包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-11-28')
## OR
tuesdata <- tidytuesdayR::tt_load(2023, week = 48)

drwho_episodes <- tuesdata$drwho_episodes
drwho_directors <- tuesdata$drwho_directors
drwho_writers <- tuesdata$drwho_writers

# 选项2：直接从GitHub读取

drwho_episodes <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-11-28/drwho_episodes.csv')
drwho_directors <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-11-28/drwho_directors.csv')
drwho_writers <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-11-28/drwho_writers.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该从数据中得出**因果关系**的结论。影响所有数据的各种调节变量很多，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用R或其他编程语言创建可视化、模型、[Shiny应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `drwho_episodes.csv`

|variable        |class     |description     |
|:---------------|:---------|:---------------|
|era             |character |Whether the episode is in the "classic" or "revived" era. All data in this dataset is within the "revived" era.|
|season_number   |double    |The season number within the era. Note that some episodes are outside of a season.|
|serial_title    |character |Serial title if available |
|story_number    |character |Story number|
|episode_number  |double    |Episode number in season|
|episode_title   |character |Episode title|
|type            |character |"episode" or "special"|
|first_aired     |double    |Date the episode first aired in the U.K.|
|production_code |character |Episode's production code if available|
|uk_viewers      |double    |Number of U.K. viewers (millions)|
|rating          |double    |Episode's rating|
|duration        |double    |Episode's duration in minutes|

# `drwho_directors.csv`

|variable     |class     |description  |
|:------------|:---------|:------------|
|story_number |character |Story number|
|director     |character |Episode's director|

# `drwho_writers.csv`

|variable     |class     |description  |
|:------------|:---------|:------------|
|story_number |character |Story number|
|writer       |character |Episode's writer|

### Cleaning Script

Clean data from the [{datardis} package](https://github.com/KittJonathan/datardis).

``` r
library(tidyverse)
library(here)
library(fs)
# pak::pak("datardis")
library(datardis)

working_dir <- here::here("data", "2023", "2023-11-28")

readr::write_csv(
  datardis::drwho_episodes,
  fs::path(working_dir, "drwho_episodes.csv")
)
readr::write_csv(
  datardis::drwho_directors,
  fs::path(working_dir, "drwho_directors.csv")
)
readr::write_csv(
  datardis::drwho_writers,
  fs::path(working_dir, "drwho_writers.csv")
)
```

