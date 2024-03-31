# 难民

本周的数据来自 PopulationStatistics {refugees} R 包。

> {refugees} 是一个旨在方便访问联合国难民事务高级专员公署（UNHCR）难民数据查询器的 R 包。它提供了一个易于使用的界面，用于访问覆盖了逾70年的强迫流离人口的数据库，包括难民、寻求庇护者、国内流离者、无国籍人士等。

该软件包提供了来自三个主要来源的数据：

- 自1951年以来联合国难民署的年度统计数据。
- 来自巴勒斯坦难民救济与工程署（UNRWA）的数据，具体为 UNRWA 授权的注册巴勒斯坦难民。
- 来自国内流离监测中心（IDMC）有关由于冲突或暴力而在其国家内部流离失所的人员的数据。

{refugees} 软件包包括八个数据集。我们正在使用 `population` 数据集，其中包含了2010年至2022年的数据。

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-08-22')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 34)

population <- tuesdata$population

# 选项 2: 直接从 GitHub 读取

population <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-08-22/population.csv')
```

### 数据字典

# `population.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|year              |double    |The year.              |
|coo_name          |character |Country of origin name.        |
|coo               |character |Country of origin UNHCR code.   |
|coo_iso           |character |Country of origin ISO code.  |
|coa_name          |character |Country of asylum name.    |
|coa               |character |Country of asylum UNHCR code.  |
|coa_iso           |character |Country of asylum ISO code.    |
|refugees          |double    |The number of refugees.   |
|asylum_seekers    |double    |The number of asylum-seekers.  |
|returned_refugees |double    |The number of returned refugees. |
|idps              |double    |The number of internally displaced persons.     |
|returned_idps     |double    |The number of returned internally displaced persons.  |
|stateless         |double    |The number of stateless persons.  |
|ooc               |double    |The number of others of concern to UNHCR.   |
|oip               |double    |The number of other people in need of international protection.     |
|hst               |double    |The number of host community members.     |


### Cleaning Script

``` r
### Data from the {refugees} R package

library(tidyverse)
library(refugees)

populations <- refugees::population

# Get just the years from 2010 to 2022

populations2010 <- filter(populations, year >= 2010)

write_csv(populations2010, "population.csv")
```

## How to Participate

- [Explore the data](https://r4ds.hadley.nz/), watching out for interesting relationships. We would like to emphasize that you should not draw conclusions about **causation** in the data. There are various moderating variables that affect all data, many of which might not have been captured in these datasets. As such, our suggestion is to use the data provided to practice your data tidying and plotting techniques, and to consider for yourself what nuances might underlie these relationships.
- Create a visualization, a model, a [shiny app](https://shiny.posit.co/), or some other piece of data-science-related output, using R or another programming language.
- [Share your output and the code used to generate it](../../../sharing.md) on social media with the #TidyTuesday hashtag.
