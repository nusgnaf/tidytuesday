# 2021-2022英超联赛比赛数据

本周的数据来自[Kaggle](https://www.kaggle.com/datasets/evangower/premier-league-match-data)，是由Evan Gower提供的[2021-2022英超联赛比赛数据](https://www.kaggle.com/datasets/evangower/premier-league-match-data)。

您可以探索英格兰足球超级联赛2021-22赛季的每场比赛和每支球队的比赛日统计数据。

数据包括参赛球队、日期、裁判以及主队和客队的统计数据，如犯规、射门、罚牌等！此外，还包括赛季每周排名的数据集。

这些数据是从英超联赛的官方网站收集而来。Evan 使用 Google 表格对数据进行了清洗。

Evan 进行了一项名为[如果比赛在半场结束时结束，谁会赢得英超联赛？](https://www.kaggle.com/code/evangower/who-wins-the-epl-if-games-end-at-half-time/)的分析，并且有一篇[来自 The Athletic 的文章](https://theathletic.com/3459766/2022/07/29/liverpool-manchester-city-premier-league-fouls-yellow-card/)讨论了每张黄牌所造成的犯规。

### 在这里获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取数据
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣的周的自述文件和所有数据集

# 使用 ISO-8601 日期格式或者年份/周数格式！

tuesdata <- tidytuesdayR::tt_load('2023-04-04')
tuesdata <- tidytuesdayR::tt_load(2023, week = 14)

soccer <- tuesdata$soccer

# 或者手动读取数据

soccer <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-04-04/soccer21-22.csv')
```

### 数据字典

# `soccer21-22.csv`

|variable |class     |description |
|:--------|:---------|:-----------|
|Date     |character |The date when the match was played  |
|HomeTeam |character |The home team    |
|AwayTeam |character |The away team    |
|FTHG     |double    |Full time home goals        |
|FTAG     |double    |Full time away goals        |
|FTR      |character |Full time result         |
|HTHG     |double    |Halftime home goals        |
|HTAG     |double    |Halftime away goals        |
|HTR      |character |Halftime results         |
|Referee  |character |Referee of the match    |
|HS       |double    |Number of shots taken by the home team          |
|AS       |double    |Number of shots taken by the away team          |
|HST      |double    |Number of shots on target by the home team   |
|AST      |double    |Number of shots on target by the away team   |
|HF       |double    |Number of fouls by the home team   |
|AF       |double    |Number of fouls by the away team    |
|HC       |double    |Number of corners taken by the home team |
|AC       |double    |Number of corners taken by the away team |
|HY       |double    |Number of yellow cards received by the home team |
|AY       |double    |Number of yellow cards received by the away team  |
|HR       |double    |Number of red cards received by the home team  |
|AR       |double    |Number of red cards received by the away team  |

### Cleaning Script

No data cleaning
