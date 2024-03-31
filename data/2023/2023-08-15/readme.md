# 垃圾邮件

本周的数据来自 Vincent Arel-Bundock 的 Rdatasets 包（https://vincentarelbundock.github.io/Rdatasets/index.html）。

> Rdatasets 是一个包含 2246 个数据集的集合，最初是随着统计软件环境 R 及其一些附加包一起分发的。其目标是使这些数据更广泛地可用于教学和统计软件开发。

我们使用的是 [垃圾邮件](https://vincentarelbundock.github.io/Rdatasets/doc/DAAG/spam7.html) 数据集。这是 [垃圾邮件数据库](https://search.r-project.org/CRAN/refmans/kernlab/html/spam.html) 的一个子集。

这是由 Mark Hopkins、Erik Reeber、George Forman 和 Jaap Suermondt 在惠普实验室收集的数据集，并与 [UCI 机器学习库](https://archive.ics.uci.edu/dataset/94/spambase) 共享的。该数据集将4601封电子邮件分类为垃圾邮件或非垃圾邮件，并附带其他变量，指示电子邮件中某些单词和字符的频率。

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-08-15')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 33)

spam <- tuesdata$spam

# 选项 2: 直接从 GitHub 读取

spam <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-08-15/spam.csv')
```

### 数据字典

# `spam.csv`

|variable |class     |description |
|:--------|:---------|:-----------|
|crl.tot  |double    | Total length of uninterrupted sequences of capitals     |
|dollar   |double    | Occurrences of the dollar sign, as percent of total number of characters     |
|bang     |double    | Occurrences of ‘!’, as percent of total number of characters    |
|money    |double    | Occurrences of ‘money’, as percent of total number of characters    |
|n000     |double    | Occurrences of the string ‘000’, as percent of total number of words    |
|make     |double    | Occurrences of ‘make’, as a percent of total number of words       |
|yesno    |character | Outcome variable, a factor with levels 'n' not spam, 'y' spam |

### Cleaning Script

First column was removed. 

## How to Participate

- [Explore the data](https://r4ds.hadley.nz/), watching out for interesting relationships. We would like to emphasize that you should not draw conclusions about **causation** in the data. There are various moderating variables that affect all data, many of which might not have been captured in these datasets. As such, our suggestion is to use the data provided to practice your data tidying and plotting techniques, and to consider for yourself what nuances might underlie these relationships.
- Create a visualization, a model, a [shiny app](https://shiny.posit.co/), or some other piece of data-science-related output, using R or another programming language.
- [Share your output and the code used to generate it](../../../sharing.md) on social media with the #TidyTuesday hashtag.
