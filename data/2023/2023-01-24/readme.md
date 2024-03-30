# Alone

本周的数据来自[Alone数据包](https://github.com/doehm/alone)，由Dan Oehm提供。

> 此数据集包含了从电视系列[Alone](https://www.history.com/shows/alone)中收集和分享的数据，由[Dan Oehm](https://gradientdescending.com/)提供。正如Oehm在他的博客文章中所描述的，在求生电视系列“Alone”中，10名求生者被投放到一个非常偏远的地区，必须独自生存。他们的目标是在北极冬季生存100天，靠着他们的求生技能、耐力和心理韧性在野外生活。

> 此包含有四个数据集：

> * survivalists.csv: 涵盖了所有9个季节的求生者的数据框，详细说明了姓名和人口统计信息、位置和职业、结果、生存天数、退出的原因（详细和分类），以及页面URL。

> * loadouts.csv: 规则允许每个求生者携带10件物品。此数据集包含了每个求生者的装备信息。它包含了详细的物品描述和一个简化版本，以便更容易地进行汇总和分析。

> * episodes.csv: 此数据集包含了每一集的详细信息，包括标题、观看人数、开始引用和IMDb评分。新的剧集将在将来的季节结束后添加。

> * seasons.csv: 季节总结数据集包括位置、纬度和经度以及其他季节级别的信息。如果存在投放日期信息，则包括投放日期。

致谢Alone数据集

> Dan Oehm:
* Alone数据包: [https://github.com/doehm/alone](https://github.com/doehm/alone)
* Alone数据包博客文章: [https://gradientdescending.com/alone-r-package-datasets-from-the-survival-tv-series/](https://gradientdescending.com/alone-r-package-datasets-from-the-survival-tv-series/)

分析示例包含在[Dan Oehm的博客文章](https://gradientdescending.com/alone-r-package-datasets-from-the-survival-tv-series/)中。


### 在此处获取数据

```{r}
# Get the Data

# Read in with tidytuesdayR package 
# Install from CRAN via: install.packages("tidytuesdayR")
# This loads the readme and all the datasets for the week of interest

# Either ISO-8601 date or year/week works!

tuesdata <- tidytuesdayR::tt_load('2023-01-24')
tuesdata <- tidytuesdayR::tt_load(2023, week = 4)

alone <- tuesdata$alone

# Or read in the data manually

survivalists <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-01-24/survivalists.csv')
loadouts <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-01-24/loadouts.csv')
episodes <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-01-24/episodes.csv')
seasons <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-01-24/seasons.csv')
```
### Data Dictionary


# `survivalists.csv`

|variable            |class     |description         |
|:-------------------|:---------|:-------------------|
|season              |double    |The season number              |
|name                |character |Name of the survivalist                |
|age                 |double    |Age of the survivalist                 |
|gender              |character |Gender              |
|city                |character |City                |
|state               |character |State               |
|country             |character |Country             |
|result              |double    |Place survivalist finished in the season              |
|days_lasted         |double    |The number of days lasted in the game before tapping out or winning         |
|medically_evacuated |logical   |If the survivalist was medically evacuated from the game |
|reason_tapped_out   |character |The reason the survivalist tapped out of the game. NA means they were the winner. Reason being that technically if they won they never tapped out.   |
|reason_category     |character |A simplified category of the reason for tapping out  |
|team                |character |The team they were associated with (only for season 4)            |
|day_linked_up       |double    |Day the team members linked up (only for season 4)       |
|profession          |character |Profession          |
|url                 |character |URL of cast page on the history channel website. Prefix URL with https://www.history.com/shows/alone/cast                 |

# `loadouts.csv`

|variable      |class     |description   |
|:-------------|:---------|:-------------|
|version       |character |Country code for the version of the show     |
|season        |double    |The season number        |
|name          |character |Name of the survivalist          |
|item_number   |double    |Item number   |
|item_detailed |character |Detailed loadout item description |
|item          |character |Loadout item. Simplified for aggregation          |

# `episodes.csv`

|variable               |class     |description            |
|:----------------------|:---------|:----------------------|
|version                |character |Country code for the version of the show             |
|season                 |double    |The season number                 |
|episode_number_overall |double    |Episode number across seasons |
|episode                |double    |Episode                |
|title                  |character |Episode title                  |
|air_date               |double    |Date the episode originally aired               |
|viewers                |double    |Number of viewers in the US (millions)                |
|quote                  |character |The beginning quote                  |
|author                 |character |Author of the beginning quote                 |
|imdb_rating            |double    |IMDb rating of the episode            |
|n_ratings              |double    |Number of ratings given for the episode              |

# `seasons.csv`

|variable      |class     |description   |
|:-------------|:---------|:-------------|
|version       |character |Country code for the version of the show      |
|season        |double    |The season number        |
|location      |character |Location      |
|country       |character |Country       |
|n_survivors   |double    |Number of survivalists in the season. In season 4 there were 7 teams of 2.   |
|lat           |double    |Latitude           |
|lon           |double    |Longitude           |
|date_drop_off |double    |The date the survivalists were dropped off |


### Cleaning Script

No data cleaning
