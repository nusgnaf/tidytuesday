# 全球人类日

本周的数据来自位于蒙特利尔麦吉尔大学的[人类时间项目](https://www.humanchronome.org/)，其研究论文为[《PNAS 中的全球人类日》](https://www.pnas.org/doi/10.1073/pnas.2219564120#sec-2)，以及[Zenodo 上的相关数据集](https://zenodo.org/record/8040631)。

> 大约80亿人每天活动确切地占用了24小时，这为世界带来了严格的物理限制，限制了世界上能够实现的变化。这些活动构成了人类行为的基础，由于社会和经济的全球一体化，许多这些活动跨越国界进行交互。该项目使用一种广义的、基于物理结果的分类方法估计了所有人类如何度过他们的时间，这种方法有助于整合来自数百个不同数据集的数据。

请参阅他们的[补充材料](https://www.pnas.org/doi/10.1073/pnas.2219564120#supplementary-materials)以了解他们的方法详情和额外的可视化。

[Zenodo 数据集](https://zenodo.org/record/8040631)包括用于创建论文中使用的数据集的输入数据和脚本。数据集来自输出文件 "all_countries.csv"、"global_human_day.csv" 和 "global_economic_activity.csv"，以及输入数据 "country_regions.csv"。输出数据文件是从收集的数据中创建的聚合输出数据，是在 'scripts' 目录中的脚本中生成的。

感谢[Data is Plural 2023-07-13 新闻通讯](https://www.data-is-plural.com/archive/2023-07-12-edition/)提供了数据。

## 数据

```{r}
# 选项1：tidytuesdayR 包
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-09-12')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 37)

all_countries <- tuesdata$all_countries
country_regions <- tuesdata$country_regions
global_human_day <- tuesdata$global_human_day
global_economic_activity <- tuesdata$global_economic_activity

# 选项2：直接从 GitHub 读取

all_countries <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-09-12/all_countries.csv')
country_regions <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-09-12/country_regions.csv')
global_human_day <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-09-12/global_human_day.csv')
global_economic_activity <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-09-12/global_economic_activity.csv')
```

## 参与方式

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应在数据中得出关于**因果关系**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并考虑自己可能潜在的这些关系背后的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `all_countries.csv`

|variable            |class     |description         |
|:-------------------|:---------|:-------------------|
|Category            |character |M24 categories            |
|Subcategory         |character |M24 subcategories         |
|country_iso3        |character |Country code in iso3        |
|region_code         |character |Region code        |
|population          |double    |Population         |
|hoursPerDayCombined |double    |Hours per day combined for the country |
|uncertaintyCombined |double    |Uncertainty combined. Uncertainty is in units variance. |

# `country_regions.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|region_code       |character |Region code       |
|region_name       |character |Region name       |
|country_name      |character |Country name      |
|M49_code          |double    |M49 code      |
|country_iso2      |character |Country code in iso2      |
|country_iso3      |character |Country code in iso3      |
|alt_country_name  |character |Alternative country name  |
|alt_country_name1 |character |Alternative country name 1 |
|alt_country_name2 |character |Alternative country name 2 |
|alt_country_name3 |character |Alternative country name 3 |
|alt_country_name4 |character |Alternative country name 4 |
|alt_country_name5 |character |Alternative country name 5 |
|alt_country_name6 |character |Alternative country name 6 |
|other_code1       |character |Other country code 1       |
|other_code2       |character |Other country code 2       |

# `global_human_day.csv`

|variable    |class     |description |
|:-----------|:---------|:-----------|
|Subcategory |character |M24 subcategory |
|hoursPerDay |double    |Hours per day for all countries |
|uncertainty |double    |Uncertainty in units variance. |

# `global_economic_activity.csv`

|variable    |class     |description |
|:-----------|:---------|:-----------|
|Subcategory |character |M24 subcategory |
|hoursPerDay |double    |Hours per day for all countries. |
|uncertainty |double    |Uncertainty in units variance. |

### Cleaning Script

``` r
library(tidyverse)

# Read in the data file all_countries.csv from https://zenodo.org/record/8040631

all_countries <- read_csv("all_countries.csv")

# Change variable name to be consistent between files

colnames(all_countries)[3] <- "country_iso3"

# Remove columns on data status

all_countries = subset(all_countries, select = -c(dataStatus,dataStatusEconomic))

# write out data
readr::write_csv(
  all_countries, "all_countries.csv")

```
