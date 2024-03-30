# 垃圾轮收集数据

本周的数据来自[Mr. Trash Wheel](https://www.mrtrashwheel.com/) [巴尔的摩健康港](https://www.waterfrontpartnership.org/healthy-harbor-initiative)倡议的[垃圾轮收集数据](https://docs.google.com/spreadsheets/d/1b8Lbe-z3PNb3H8nSsSjrwK2B0ReAblL2/edit#gid=1143432795)。

> Mr. Trash Wheel 是一种半自动的垃圾拦截器，放置在河流、溪流或其他出水口的尽头。Mr. Trash Wheel 懒得在海洋中追逐垃圾，他停在原地等待垃圾流过来。Mr. Trash Wheel 以可持续的能源驱动，并且经过设计能够经受住最大的风暴，利用太阳能和水力能源的独特结合每年清除水中数百吨的垃圾。

健康港倡议有四个垃圾轮收集垃圾。Mr. Trash Wheel 是第一个开始的，从那时起又加入了另外三个。垃圾轮家族已经收集了超过2362吨的垃圾。了解更多有关[Mr. Trash Wheel如何工作的信息](https://www.mrtrashwheel.com/technology/)。

>数据收集方法

>1. 当机组人员在装载垃圾的过程中，他们会手动计数单个输送带托板上列出的每种物品的数量。在装满垃圾箱的过程中，这个过程会重复几次。然后计算每个托板上每种物品的平均数量。然后将平均数乘以托板速率，再乘以填满垃圾箱所需的时间。

>示例：
> * 托板 #1- 9个塑料瓶
> * 托板 #2- 14个塑料瓶
> * 托板 #3- 5个塑料瓶
> * 托板 #4- 12个塑料瓶
> * 平均值 = 每个托板10个塑料瓶

>输送带速度 = 每分钟2.5个托板，因此每分钟平均装载25个塑料瓶。如果填满垃圾箱需要100分钟，我们估计该垃圾箱中有2500个瓶子。

>2. 如果在装载过程中没有机组人员在场，我们将对收集的物料进行随机的蓝筹样本，并计算这些样本中的物品数量。一个满垃圾箱大约含有325个蓝筹。因此，如果从垃圾箱中取出的平均蓝筹样本包含3个聚苯乙烯容器，我们估计该垃圾箱中有975个聚苯乙烯容器。

>3. 定期进行“垃圾箱潜水”活动，志愿者会数一数整个垃圾箱中的所有东西。这些活动有助于验证我们的取样方法，并且还可以查看在我们的取样类别中未包含的存在的材料。

哪种类型的垃圾被收集得最多？不同的垃圾轮是否收集不同类型的垃圾？一年中是否有更多或更少的垃圾被收集？

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-03-05')
## OR
tuesdata <- tidytuesdayR::tt_load(2024, week = 10)

trashwheel <- tuesdata$trashwheel


# 选项 2: 直接从 GitHub 读取

trashwheel <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-03-05/trashwheel.csv')

```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该在数据中得出**因果关系**的结论。影响所有数据的各种调节变量，其中许多可能没有在这些数据集中捕获。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `trashwheel.csv`

|variable       |class     |description    |
|:--------------|:---------|:--------------|
|ID             |character |Short name for the Trash Wheel             |
|Name           |character |Name of the Trash Wheel           |
|Dumpster       |double    |Dumpster number       |
|Month          |character |Month          |
|Year           |double    |Year           |
|Date           |character |Date           |
|Weight         |double    |Weight in tons         |
|Volume         |double    |Volume in cubic yards          |
|PlasticBottles |double    |Number of plastic bottles |
|Polystyrene    |double    |Number of polystyrene items    |
|CigaretteButts |double    |Number of cigarette butts |
|GlassBottles   |double    |Number of glass bottles   |
|PlasticBags    |double    |Number of plastic bags    |
|Wrappers       |double    |Number of wrappers       |
|SportsBalls    |double    |Number of sports balls    |
|HomesPowered   |double    |Homes Powered - Each ton of trash equates to on average 500 kilowatts of electricity.  An average household will use 30 kilowatts per day.   |


### 数据清洗脚本

``` r
library(tidyverse)
library(here)

working_dir <- here::here("data", "2024", "2024-03-05")

# Download the csv files from https://docs.google.com/spreadsheets/d/1b8Lbe-z3PNb3H8nSsSjrwK2B0ReAblL2/edit#gid=1143432795

mrtrash <- read_csv("mrtrashwheel.csv")
professortrash <- read_csv("professortrash.csv")
captaintrash <- read_csv("captaintrash.csv")
gwynndatrash <- read_csv("gwynndatrash.csv")


# Check the files
glimpse(mrtrash)
glimpse(professortrash)
glimpse(captaintrash)
glimpse(gwynndatrash)

# Remove empty rows and columns

mrtrash <- head(mrtrash, -1)
mrtrash <- mrtrash[-c(15:16)]
professortrash <- head(professortrash, -1)
captaintrash <- head(captaintrash, -1)
gwynndatrash <- head(gwynnda, -1)

# Rename columns so they don't have spaces

colnames(mrtrash)

mrtrash <- mrtrash %>% 
  rename(
    Weight = "Weight (tons)",
    Volume = "Volume (cubic yards)",
    PlasticBottles = "Plastic Bottles",
    CigaretteButts = "Cigarette Butts",
    GlassBottles = "Glass Bottles",
    PlasticBags = "Plastic Bags",
    SportsBalls = "Sports Balls",
    HomesPowered = "Homes Powered*"
  )

colnames(professortrash)

professortrash <- professortrash %>% 
  rename(
    Weight = "Weight (tons)",
    Volume = "Volume (cubic yards)",
    PlasticBottles = "Plastic Bottles",
    CigaretteButts = "Cigarette Butts",
    GlassBottles = "Glass Bottles",
    PlasticBags = "Plastic Bags",
    HomesPowered = "Homes Powered*"
  )

colnames(captaintrash)

captaintrash <- captaintrash %>% 
  rename(
    Weight = "Weight (tons)",
    Volume = "Volume (cubic yards)",
    PlasticBottles = "Plastic Bottles",
    CigaretteButts = "Cigarette Butts",
    PlasticBags = "Plastic Bags",
    HomesPowered = "Homes Powered*"
  )

colnames(gwynndatrash)

gwynndatrash <- gwynndatrash %>% 
  rename(
    Weight = "Weight (tons)",
    Volume = "Volume (cubic yards)",
    PlasticBottles = "Plastic Bottles",
    CigaretteButts = "Cigarette Butts",
    PlasticBags = "Plastic Bags",
    HomesPowered = "Homes Powered*"
  )

# Add a column with the name of the trash wheel, and columns with "NA" where the dataset doesn't have that information

mrtrash <- data.frame(ID = "mister", Name = "Mister Trash Wheel", mrtrash)

# Add SportsBalls to professor
professortrash <- data.frame(ID = "professor", Name = "Professor Trash Wheel", SportsBalls = NA, professortrash)

# Add SportsBalls and GlassBottles to captain and gwynnda
captaintrash <- data.frame(ID = "captain", Name = "Captain Trash Wheel", SportsBalls = NA, GlassBottles = NA, captaintrash)
gwynndatrash <- data.frame(ID = "gwynnda", Name = "Gwynnda Trash Wheel", SportsBalls = NA, GlassBottles = NA, gwynndatrash)

# Join the dataframes together

trashwheel <- rbind(mrtrash, professortrash, captaintrash, gwynndatrash)



readr::write_csv(
  trashwheel,
  fs::path(working_dir, "trashwheel.csv")
)
```
