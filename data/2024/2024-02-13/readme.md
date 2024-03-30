# 情人节消费者数据

情人节快乐！本周我们将探索[情人节调查数据](https://www.kaggle.com/datasets/infinator/happy-valentines-day-2022)。美国国家零售联合会(National Retail Federation)进行了调查，并创建了一个[情人节数据中心](https://nrf.com/research-insights/holiday-data-and-trends/valentines-day/valentines-day-data-center)，您可以在其中探索消费者如何庆祝的数据。

> 美国国家零售联合会(NRF)每年对消费者庆祝情人节的计划进行调查已经超过十年了。深入研究过去十年的数据，并使用交互式图表来探索总支出、平均支出、计划礼物类型和每种礼物类型的支出的人口统计数据。

NRF一直在继续收集数据。本周的数据从2010年到2022年，由Suraj Das整理为Kaggle数据集。在历史调查中，性别只收集为'男性'和'女性'，这并不能准确地包括所有性别。

如果您正在寻找其他情人节类型的数据集，请查看以前关于[巧克力](https://github.com/rfordatascience/tidytuesday/tree/master/data/2022/2022-01-18)或[桌游](https://github.com/rfordatascience/tidytuesday/tree/master/data/2022/2022-01-25)的数据集（这是一个很好的情人节活动！）。

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-02-13')
## OR
tuesdata <- tidytuesdayR::tt_load(2024, week = 7)

historical_spending <- tuesdata$historical_spending
gifts_age <- tuesdata$gifts_age
gifts_gender <- tuesdata$gifts_gender


# 选项 2: 直接从 GitHub 读取

historical_spending <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-02-13/historical_spending.csv')
gifts_age <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-02-13/gifts_age.csv')
gifts_gender <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-02-13/gifts_gender.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该在数据中得出**因果关系**的结论。影响所有数据的各种调节变量，其中许多可能没有在这些数据集中捕获。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `historical_spending.csv`

|variable           |class  |description        |
|:------------------|:------|:------------------|
|Year               |double |Year               |
|PercentCelebrating |double |Percent of people celebrating Valentines Day |
|PerPerson          |double |Average amount each person is spending          |
|Candy              |double |Average amount spending on candy              |
|Flowers            |double |Average amount spending on flowers            |
|Jewelry            |double |Average amount spending on jewelry            |
|GreetingCards      |double |Average amount spending on greeting cards      |
|EveningOut         |double |Average amount spending on an evening out         |
|Clothing           |double |Average amount spending on clothing           |
|GiftCards          |double |Average amount spending on gift cards          |

# `gifts_age.csv`

|variable            |class     |description         |
|:-------------------|:---------|:-------------------|
|Age                 |character |Age                 |
|SpendingCelebrating |double    |Percent spending money on or celebrating Valentines Day |
|Candy               |double    |Average percent spending on candy               |
|Flowers             |double    |Average percent spending on flowers         |
|Jewelry             |double    |Average percent spending on jewelry           |
|GreetingCards       |double    |Average percent spending on greeting cards    |
|EveningOut          |double    |Average percent spending on an evening out       |
|Clothing            |double    |Average percent spending on clothing          |
|GiftCards           |double    |Average percent spending on gift cards           |

# `gifts_gender.csv`

|variable            |class     |description         |
|:-------------------|:---------|:-------------------|
|Gender                 |character |Gender only including Men or Women                 |
|SpendingCelebrating |double    |Percent spending money on or celebrating Valentines Day |
|Candy               |double    |Average percent spending on candy               |
|Flowers             |double    |Average percent spending on flowers         |
|Jewelry             |double    |Average percent spending on jewelry           |
|GreetingCards       |double    |Average percent spending on greeting cards    |
|EveningOut          |double    |Average percent spending on an evening out       |
|Clothing            |double    |Average percent spending on clothing          |
|GiftCards           |double    |Average percent spending on gift cards           |

### 清洗脚本

数据从[Kaggle数据集](https://www.kaggle.com/datasets/infinator/happy-valentines-day-2022)中下载。

来自historical_gift_trends_per_person_spending.csv、historical_spending_average_expected_spending.csv和historical_spending_percent_celebrating.csv的数据被合并到historical_spending.csv中。

来自planned_gifts_age.csv和spending_or_celebrating_age_1.csv的数据被合并到gifts_age.csv中。

来自planned_gifts_gender.csv和spending_or_celebrating_gender_1.csv的数据被合并到gifts_gender.csv中。

所有数值中的百分号和美元符号已被移除。
