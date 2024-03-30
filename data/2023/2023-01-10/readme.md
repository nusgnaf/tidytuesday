### 请为您的帖子添加替代文本

请为您发布的所有`#TidyTuesday`图形添加替代文本（alt text）。

Twitter提供了有关如何为图像添加替代文本的[指南](https://help.twitter.com/en/using-twitter/picture-descriptions)。

DataViz Society/Nightingale（通过Amy Cesal提供）有一篇关于编写_好的_图表/图形替代文本的[文章](https://medium.com/nightingale/writing-alt-text-for-data-visualization-2a218ef43f81)。

> 这是一个编写数据可视化替代文本的简单公式：
> ### 图表类型
> 让部分视力受损的人知道图表类型是什么，并为理解其余视觉提供上下文是有帮助的。
> 示例：线图
> ### 数据类型
> 图表中包含了哪些数据？X和Y轴标签可能会帮助您弄清楚这一点。
> 示例：过去一年每天销售的香蕉数量
> ### 包含图表的原因
> 想想为什么要包含这个可视化内容。它展示了什么有意义的信息。每个可视化内容都应该有一个要点，并且您应该告诉人们要寻找什么。
> 示例：冬季月份的香蕉销售量更高
> ### 数据或来源链接
> 请不要在您的替代文本中包含此信息，但应该在周围的文本中包含此信息。人们应该能够点击链接查看源数据或深入探究可视化内容。这提供了关于您的数据来源的透明度，并让人们探索数据。
> 示例：数据来自美国农业部

宾夕法尼亚州立大学有一篇关于为图表和表格编写替代文本描述的[文章](https://accessibility.psu.edu/images/charts/)。

> 图表、图形和地图使用视觉元素向用户传达复杂的图像。但由于它们是图像，对色盲用户和屏幕阅读器用户提供了严重的无障碍问题。有关如何使图表更易访问的详细信息，请参阅[此页面上的示例](https://accessibility.psu.edu/images/charts/)。

`{rtweet}`包包括[以编程方式添加替代文本](https://docs.ropensci.org/rtweet/reference/post_tweet.html)的功能。

需要**提醒**吗？有[扩展程序](https://chrome.google.com/webstore/detail/twitter-required-alt-text/fpjlpckbikddocimpfcgaldjghimjiik/related)可以强制您记住为带媒体的推文添加替代文本。

# 鸟类喂食器观察数据

本周的数据来自[Project FeederWatch](https://feederwatch.org/explore/raw-dataset-requests/)。

> FeederWatch是一个从11月到4月进行的关于访问北美后院、自然中心、社区区域和其他地点的鸟类的调查。公民科学家可以在吸引鸟类的植被、栖息地、水源或食物的区域观察鸟类。时间安排完全灵活。人们可以在选择的日期上随意观察鸟类，然后在网上输入观察到的数量。这使任何人都可以追踪家周围的鸟类状况，并为鸟类分布和数量的大陆数据集做出贡献。

> FeederWatch数据显示了每个冬季在整个大陆上数千个地点的鸟类物种访问饲料器。数据还显示了每个物种的个体数量。这些信息可以用来衡量鸟类物种的冬季分布范围和数量随时间的变化。

本周的TidyTuesday包含2021年数据的子集，但自1988年以来的数据可通过[FeederWatch原始数据集下载页面](https://feederwatch.org/explore/raw-dataset-requests/)下载。

> Project FeederWatch由康奈尔鸟类学实验室和加拿大鸟类运营。自2016年以来，Project FeederWatch由Wild Bird Unlimited赞助。

> 对FeederWatch的感谢。

> 康奈尔鸟类学实验室和加拿大鸟类致力于使通过我们的公民科学计划收集的数据免费提供给学生、记者和普通公众。

> “这个独特的数据集完全依赖于我们成千上万的志愿者参与者的努力。我们要求所有数据分析人员感谢为FeederWatch的实现做出了贡献的成千上万的参与者，以及为开发和管理该项目的康奈尔鸟类学实验室和加拿大鸟类。”

[分析示例](https://feederwatch.org/explore/raw-dataset-requests/)与原始数据一起提供，并且有一个部分可以[探索](https://feederwatch.org/explore/)数据。

有关分析此数据集的更多详细信息：
[30多年来在北美补充喂鸟站进行的标准化鸟类计数：Project FeederWatch的公民科学数据报告](https://www.frontiersin.org/articles/10.3389/fevo.2021.619682/full) 由David N. Bonter和Emma I. Greig

### 在此处获取数据

鸟类喂食器观察数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# ISO-8601日期或年/周均可使用！

tuesdata <- tidytuesdayR::tt_load('2023-01-10')
tuesdata <- tidytuesdayR::tt_load(2023, week = 02)

feederwatch <- tuesdata$feederwatch

# 或者手动读取数据

feederwatch <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-01-10/PFW_2021_public.csv')
site_data <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-01-10/PFW_count_site_data_public_2021.csv')

```
### 数据字典

> The [Project FeederWatch Data Dictionary](https://drive.google.com/file/d/1kHmx2XhA2MJtEyTNMpwqTQEnoa9M7Il2/view?usp=sharing) explains all fields and codes used in the database and is essential for understanding the dataset.

# `PFW_2021_public.csv`


|variable           |class     |description        |
|:------------------|:---------|:------------------|
|loc_id             |character |Unique identifier for each survey site |
|latitude           |double    |Latitude in decimal degrees for each survey site |
|longitude          |double    |Longitude in decimal degrees for each survey site |
|subnational1_code  |character |Country abbreviation and State or Province abbreviation of each survey site. Note that the files may contain some "XX" locations. These are sites that were incorrectly placed by the user (e.g., site plotted in the ocean.) |
|entry_technique    |character |Variable indicating method of site localization |
|sub_id             |character |Unique identifier for each checklist |
|obs_id             |character |Unique identifier for each observation of a species |
|Month              |double    |Month of 1st day of two-day observation period |
|Day                |double    |Day of 1st day of two-day observation period |
|Year               |double    |Year of 1st day of two-day observation period |
|PROJ_PERIOD_ID     |character |Calendar year of end of FeederWatch season |
|species_code       |character |Bird species observed, stored as 6-letter species codes |
|how_many           |double    |Maximum number of individuals seen at one time during observation period |
|valid              |double    |Validity of each observation based on flagging system |
|reviewed           |double    |Review state of each observation based on flagging system |
|day1_am            |double    |Variable indicating if observer watched during morning of count Day 1 |
|day1_pm            |double    |Variable indicating if observer watched during afternoon of count Day 1 |
|day2_am            |double    |Variable indicating if observer watched during morning of count Day 2 |
|day2_pm            |double    |Variable indicating if observer watched during afternoon of count Day 2 |
|effort_hrs_atleast |double    |Participant estimate of survey time  for each checklist |
|snow_dep_atleast   |double    |Participant estimate of minimum snow depth during a checklist |
|Data_Entry_Method  |character |Data entry method for each checklist (e.g., web, mobile app or paper form) |

# `PFW_count_site_data_public_2021.csv`

|variable                     |class     |description                  |
|:----------------------------|:---------|:----------------------------|
|loc_id                       |character |loc_id                       |
|proj_period_id               |character |proj_period_id               |
|yard_type_pavement           |double    |yard_type_pavement           |
|yard_type_garden             |double    |yard_type_garden             |
|yard_type_landsca            |double    |yard_type_landsca            |
|yard_type_woods              |double    |yard_type_woods              |
|yard_type_desert             |double    |yard_type_desert             |
|hab_dcid_woods               |double    |habitat type decidious woods               |
|hab_evgr_woods               |double    |habitat type evergreen woods               |
|hab_mixed_woods              |double    |habitat type mixed woods              |
|hab_orchard                  |double    |habitat type orchard                  |
|hab_park                     |double    |habitat type park                     |
|hab_water_fresh              |double    |habitat type fresh water              |
|hab_water_salt               |double    |habitat type salt water               |
|hab_residential              |double    |habitat type residential              |
|hab_industrial               |double    |habitat type industrial               |
|hab_agricultural             |double    |habitat type agricultural             |
|hab_desert_scrub             |double    |habitat type desert_scrub             |
|hab_young_woods              |double    |habitat type young_woods              |
|hab_swamp                    |double    |habitat type swamp                    |
|hab_marsh                    |double    |habitat type marsh                    |
|evgr_trees_atleast           |double    |minimum number of trees or shrubs in the count area - evergreen trees           |
|evgr_shrbs_atleast           |double    |minimum number of trees or shrubs in the count area - evergreen shrubs          |
|dcid_trees_atleast           |double    |minimum number of trees or shrubs in the count area - deciduous trees           |
|dcid_shrbs_atleast           |double    |minimum number of trees or shrubs in the count area - deciduous srubs        |
|fru_trees_atleast            |double    |minimum number of trees or shrubs in the count area - fruit trees            |
|cacti_atleast                |double    |minimum number of trees or shrubs in the count area - cacti               |
|brsh_piles_atleast           |double    |minimum number of brush piles located within the count area           |
|water_srcs_atleast           |double    |minimum number of water sources located within the count area         |
|bird_baths_atleast           |double    |minimum number of bird baths located within the count area      |
|nearby_feeders               |double    |presence or absense of feeders              |
|squirrels                    |double    |do squirrels take food from feeders at least 3 times per week?                   |
|cats                         |double    |are cats active within 30 m of the feeders for at least 30 minutes 3 days per week?                        |
|dogs                         |double    |are dogs active within 30 m of the feeders for at least 30 minutes 3 days per week?                          |
|humans                       |double    |are humans active within 30 m of the feeders for at least 30 minutes 3 days per week?                        |
|housing_density              |double    |participant estimated housing density of neighborhood          |
|fed_yr_round                 |double    |fed_yr_round                 |
|fed_in_jan                   |double    |fed_in_jan                   |
|fed_in_feb                   |double    |fed_in_feb                   |
|fed_in_mar                   |double    |fed_in_mar                   |
|fed_in_apr                   |double    |fed_in_apr                   |
|fed_in_may                   |double    |fed_in_may                   |
|fed_in_jun                   |double    |fed_in_jun                   |
|fed_in_jul                   |double    |fed_in_jul                   |
|fed_in_aug                   |double    |fed_in_aug                   |
|fed_in_sep                   |double    |fed_in_sep                   |
|fed_in_oct                   |double    |fed_in_oct                   |
|fed_in_nov                   |double    |fed_in_nov                   |
|fed_in_dec                   |double    |fed_in_dec                   |
|numfeeders_suet              |double    |numfeeders suet              |
|numfeeders_ground            |double    |numfeeders ground            |
|numfeeders_hanging           |double    |numfeeders hanging           |
|numfeeders_platfrm           |double    |numfeeders platfrm           |
|numfeeders_humming           |double    |numfeeders hummingbird           |
|numfeeders_water             |double    |numfeeders water dispensers             |
|numfeeders_thistle           |double    |numfeeders thistle           |
|numfeeders_fruit             |double    |numfeeders fruit             |
|numfeeders_hopper            |double    |numfeeders hopper            |
|numfeeders_tube              |double    |numfeeders tube              |
|numfeeders_other             |double    |numfeeders other             |
|population_atleast           |double    |participant estimated population of city or town         |
|count_area_size_sq_m_atleast |double    |participant estimated area of survey site |

### Cleaning Script

```r
# Download the raw data.

PFW_2021_public <- readr::read_csv("https://clo-pfw-prod.s3.us-west-2.amazonaws.com/data/PFW_2021_public.csv")
dplyr::glimpse(PFW_2021_public)

# There are almost three million rows! The file is too big for github, let's
# subsample.

set.seed(424242)
PFW_2021_public_subset <- dplyr::slice_sample(PFW_2021_public, n = 1e5)

readr::write_csv(PFW_2021_public_subset, here::here("data", "2023", "2023-01-10", "PFW_2021_public.csv"))
```
