# 罗伊·肯特的 F**k 计数

对于Deepsha Menghani关于[Quarto中数据可视化动画和交互性的讲座](https://deepshamenghani.github.io/posit_plotly_crosstalk/#/title-slide)，她以2倍速观看了《泰德·拉索》的每一集，并认真记录了每一个 F*bomb 和手势参考，然后将其制作成了[richmondway R包](https://github.com/deepshamenghani/richmondway)！

泰德·拉索是什么，罗伊·肯特是谁？

[泰德·拉索](https://en.wikipedia.org/wiki/Ted_Lasso)是一部电视剧，"讲述了泰德·拉索，一个美国大学橄榄球教练被聘请来指导一支英格兰足球队，他的秘密意图是他的经验不足会导致失败，但是他乡村、乐观的领导风格意外地取得了成功。"

[罗伊·肯特](https://ted-lasso.fandom.com/wiki/Roy_Kent)是其中的一个主要角色，从 AFC 里士满队的队长变成了教练之一。特别是在早期季节，他是一个话不多的人，但他说的话中有很多是 f**k，表达了各种情绪 - 愤怒、悲伤、快乐、欢笑、爱、惊讶、思考和欢欣。

该数据集包括了每一集中使用的 f**k 的数量、百分比和上下文。

## 数据

```{r}
# 滚动到代码块的末尾，看到如何将数据重新组合成图形！

# 选项1：tidytuesdayR 包
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-09-26')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 39)

richmondway <- tuesdata$richmondway

# 选项2：直接从 GitHub 读取

richmondway <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-09-26/richmondway.csv')
```

## 参与方式

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应在数据中得出关于**因果关系**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并考虑自己可能潜在的这些关系背后的细微差别。
- 使用R或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

Source Created by Deepsha Menghani by watching the show and counting the number of F-cks used in sentences and as gestures.

# `richmondway.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|Character         |character |Character single value - Roy Kent         |
|Episode_order     |double    |The order of the episodes from the first to the last    |
|Season            |double    |The season 1, 2 or 3 associated with the count        |
|Episode           |double    |The episode within the season associated with the count          |
|Season_Episode    |character |Season and episode as a combined variable   |
|F_count_RK        |double    |Roy Kent's F-ck count in that season and episode     |
|F_count_total     |double    |Total F-ck count by all characters combined including Roy Kent in that season and episode |
|cum_rk_season     |double    |Roy Kent's cumulative F-ck count within that season    |
|cum_total_season  |double    |Cumulative total F-ck count by all characters combined including Roy Kent within that season |
|cum_rk_overall    |double    |Roy Kent's cumulative F-ck count across all episodes and seasons until that episode  |
|cum_total_overall |double    |Cumulative total F-ck count by all characters combined including Roy Kent across all episodes and seasons until that episode |
|F_score           |double    |Roy Kent's F-count divided by the total F-count in the episode     |
|F_perc            |double    |F-score as percentage  |
|Dating_flag       |character |Flag of yes or no for whether during the episode Roy Kent was dating the characted Keeley     |
|Coaching_flag     |character |Flag of yes or no for whether during the episode Roy Kent was coaching the team  |
|Imdb_rating       |double    |Imdb rating of that episode   |


### Cleaning Script

The data was collected and created as an R package by Deepsha Menghani. 

``` r
library(tidyverse)

devtools::install_github("deepshamenghani/richmondway")
library(richmondway)

write_csv(richmondway, "richmondway.csv")

```
