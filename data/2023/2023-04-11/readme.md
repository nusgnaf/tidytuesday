# 美国鸡蛋产量

本周的数据来自[The Humane League 的美国鸡蛋生产数据集](https://thehumaneleague.org/article/E008R01-us-egg-production-data)，由[Samara Mendez](https://samaramendez.github.io/)提供。您可以在[OSF 项目](https://osf.io/z2gxn/)上找到此项目的数据集和代码。

该数据集跟踪了美国自2007年12月至2021年2月的无笼养鸡蛋供应情况。对于 TidyTuesday，我们使用了截至2021年2月的数据，但完整数据集截至目前的数据可在[OSF 项目](https://osf.io/z2gxn/)中获取。

> 在这个项目中，他们综合了一个分析准备就绪的数据集，跟踪无笼养母鸡和无笼养鸡蛋供应相对于美国总母鸡和鸡蛋数量的情况。该数据集基于美国农业部（USDA）发布的每周或每月报告。他们通过 USDA 和行业出版物中的定义和蛋制品分类补充了这些数据。数据包括无笼养母鸡的群体规模（绝对和相对）和鸡蛋生产量，以及美国所有产蛋母鸡的数据，旨在了解该行业向无笼养转变对母鸡的影响。数据覆盖范围从2007年12月到2021年2月。

### 在这里获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取数据
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣的周的自述文件和所有数据集

# 使用 ISO-8601 日期格式或者年份/周数格式！

tuesdata <- tidytuesdayR::tt_load('2023-04-11')
tuesdata <- tidytuesdayR::tt_load(2023, week = 15)

eggproduction <- tuesdata$`egg-production`
cagefreepercentages <- tuesdata$`cage-free-percentages`


# 或者手动读取数据

eggproduction  <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-04-11/egg-production.csv')
cagefreepercentages <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-04-11/cage-free-percentages.csv')

```

### 数据字典

# `egg-production.csv`

|variable       |class     |description    |
|:--------------|:---------|:--------------|
|observed_month |double    |Month in which report observations are collected,Dates are recorded in ISO 8601 format YYYY-MM-DD |
|prod_type      |character |type of egg product: hatching, table eggs      |
|prod_process   |character |type of production process and housing: cage-free (organic), cage-free (non-organic), all. The value 'all' includes cage-free and conventional housing.   |
|n_hens         |double    |number of hens produced by hens for a given month-type-process combo   |
|n_eggs         |double    |number of eggs producing eggs for a given month-type-process combo     |
|source         |character |Original USDA report from which data are sourced. Values correspond to titles of PDF reports. Date of report is included in title.   |


# `cage-free-percentages.csv`

|variable       |class     |description    |
|:--------------|:---------|:--------------|
|observed_month |double    |Month in which report observations are collected,Dates are recorded in ISO 8601 format YYYY-MM-DD |
|percent_hens   |double    |observed or computed percentage of cage-free hens relative to all table-egg-laying hens  |
|percent_eggs   |double    |computed percentage of cage-free eggs relative to all table eggs,This variable is not available for data sourced from the Egg Markets Overview report |
|source         |character |Original USDA report from which data are sourced. Values correspond to titles of PDF reports. Date of report is included in title.  |


### Cleaning Script

This data was already cleaned for the report. Raw data is also available at [US Egg Production Dataset](https://osf.io/z2gxn/).
