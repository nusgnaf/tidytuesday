# 美国众议院选举结果

今天是美国的选举日！为了庆祝，本周的数据来自[麻省理工学院选举数据与科学实验室](https://electionlab.mit.edu/)（MEDSL）。本周向[RStudio GitHub Copilot集成](https://docs.posit.co/ide/user/ide/guide/tools/copilot.html)致敬，它推荐了MEDSL。

根据MEDSL的报告[新报告：我们在2022年如何投票](https://electionlab.mit.edu/articles/new-report-how-we-voted-2022)：

> 美国选举绩效调查（SPAE）提供了有关美国人在最近一次联邦选举中的投票体验的信息。自2008年以来，该调查一直在联邦选举后进行，是该国唯一一个明确致力于理解选民如何体验选举过程的公共意见项目。

我们特别提供了1976年至2022年的众议院选举数据。查看[MEDSL网站](https://electionlab.mit.edu/)以获取其他数据集和工具。

在您的工作中务必引用MEDSL：

```
@data{DVN/IG0UN2_2017,
author = {MIT Election Data and Science Lab},
publisher = {Harvard Dataverse},
title = {{U.S. House 1976–2022}},
UNF = {UNF:6:A6RSZvlhh8eRZ4+mvT/HRQ==},
year = {2017},
version = {V12},
doi = {10.7910/DVN/IG0UN2},
url = {https://doi.org/10.7910/DVN/IG0UN2}
}
```

## 数据

```{r}
# 选项1：tidytuesdayR包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-11-07')
## OR
tuesdata <- tidytuesdayR::tt_load(2023, week = 45)

house <- tuesdata$house

# 选项2：直接从GitHub读取

house <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-11-07/house.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该从数据中得出**因果关系**的结论。影响所有数据的各种调节变量很多，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用R或其他编程语言创建可视化、模型、[闪亮的应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `house.csv`

|variable       |class     |description    |
|:--------------|:---------|:--------------|
|year           |double    |year in which election was held |
|state          |character |state name |
|state_po       |character |U.S. postal code state abbreviation |
|state_fips     |double    |State FIPS code |
|state_cen      |double    |U.S. Census state code |
|state_ic       |double    |ICPSR state code |
|office         |character |U.S. House (constant) |
|district       |character |district number. At-large districts are coded as 0 (zero) |
|stage          |character |electoral stage (gen = general elections, pri = primary elections) |
|runoff         |logical   |runoff election |
|special        |logical   |special election |
|candidate      |character |name of the candidate as it appears in the House Clerk report |
|party          |character |party of the candidate (always entirely lowercase) (Parties are as they appear in the House Clerk report. In states that allow candidates to appear on multiple party lines, separate vote totals are indicated for each party. Therefore, for analysis that involves candidate totals, it will be necessary to aggregate across all party lines within a district. For analysis that focuses on two-party vote totals, it will be necessary to account for major party candidates who receive votes under multiple party labels. Minnesota party labels are given as they appear on the Minnesota ballots. Future versions of this file will include codes for candidates who are endorsed by major parties, regardless of the party label under which they receive votes.) |
|writein        |logical   |vote totals associated with write-in candidates |
|mode           |character |mode of voting; states with data that doesn't break down returns by mode are marked as "total" |
|candidatevotes |double    |votes received by this candidate for this particular party |
|totalvotes     |double    |total number of votes cast for this election |
|unofficial     |logical   |TRUE/FALSE indicator for unofficial result (to be updated later); this appears only for 2018 data in some cases |
|version        |double    |date when this dataset was finalized |
|fusion_ticket  |logical   |A TRUE/FALSE indicator as to whether the given candidate is running on a fusion party ticket, which will in turn mean that a candidate will appear multiple times, but by different parties, for a given election. States with fusion tickets include Connecticut, New Jersey, New York, and South Carolina. |


### Cleaning Script

Clean data and dictionary downloaded from the [Harvard Dataverse ](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/IG0UN2)
