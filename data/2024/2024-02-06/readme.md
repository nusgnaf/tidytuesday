# 一些世界遗产地

本周我们将探索[联合国教科文组织世界遗产地](https://whc.unesco.org/en/list)的一个非常小的子集。 [1 数据集，100 可视化项目](https://100.datavizproject.com/) 使用此数据集来探索可视化简单数据集的不同方式。现在轮到你来尝试了！尝试重新创建其中一些图表，或者自己创造。您可以通过创建可用于其他数据集的可视化类型，或者从他人那里获取灵感，来扩展您的数据可视化代码工具箱。分享您最喜欢的！

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-02-06')
## OR
tuesdata <- tidytuesdayR::tt_load(2024, week = 6)

heritage <- tuesdata$heritage

# 选项 2: 直接从 GitHub 读取

heritage <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-02-06/heritage.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该在数据中得出**因果关系**的结论。影响所有数据的各种调节变量，其中许多可能没有在这些数据集中捕获。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。


### 数据字典

# `heritage.csv`

|变量 |类型     |描述 |
|:--------|:---------|:-----------|
|country  |字符型   |国家 |
|2004     |整型   |2004年的联合国教科文组织世界遗产地数量 |
|2022     |整型   |2022年的联合国教科文组织世界遗产地数量 |

### 清洗脚本

数据来自[1 dataset, 100 visualizations](https://100.datavizproject.com/)。无清洗。
