# 美国的闹鬼地点

万圣节即将到来，因此我们正在探索一个令人毛骨悚然的数据集：美国的闹鬼地点编译。该数据集由[Tim Renner](https://github.com/timothyrenner/shadowlands-haunted-places)编制，使用了[The Shadowlands闹鬼地点索引](https://www.theshadowlands.net/places/)，并在[data.world](https://data.world/timothyrenner/haunted-places)上分享。

我们还利用这个数据集提醒大家，[下周将有几个用于空间数据的R包将被放入墓地](https://geocompx.org/post/2023/rgdal-retirement/index.html)。不要被它们的消亡所欺骗！

## 数据

```{r}
# 选项1：tidytuesdayR 包
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-10-10')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 41)

haunted_places <- tuesdata$haunted_places

# 选项2：直接从 GitHub 读取

haunted_places <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-10-10/haunted_places.csv')

```

## 参与方式

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应在数据中得出关于**因果关系**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并考虑自己可能潜在的这些关系背后的细微差别。
- 使用R或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `haunted_places.csv`

|variable       |class     |description    |
|:--------------|:---------|:--------------|
|city           |character |The city where the place is located. |
|country        |character |The country where the place is located (always "United States") |
|description    |character |A text description of the place. The amount of detail in these descriptions is highly variable. |
|location       |character |A title for the haunted place. |
|state          |character |The US state where the place is located. |
|state_abbrev   |character |The two-letter abbreviation for the state. |
|longitude      |double    |Longitude of the place. |
|latitude       |double    |Latitude of the place. |
|city_longitude |double    |Longitude of the city center. |
|city_latitude  |double    |Latitude of the city center. |

### Cleaning Script

Data downloaded directly from https://query.data.world/s/glc736mqf4dxrqe6nbsamblqndemb6?dws=00000
