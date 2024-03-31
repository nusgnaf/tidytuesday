# 全球表面温度

本周的数据来自[NASA GISS表面温度分析（GISTEMP v4）](https://data.giss.nasa.gov/gistemp/)。这些数据集是全球和半球月平均值和纬向年平均值的表格。它们结合了陆地表面、空气和海表面水温异常（陆海温度指数，L-OTI）。表中的值是相对于1951-1980年的对应值的偏差。

> GISS表面温度分析版本4（GISTEMP v4）是全球表面温度变化的估计值。图表每月中旬更新一次，使用来自NOAA GHCN v4（气象站）和ERSST v5（海洋区域）的当前数据文件，组合方式如他们的出版物Hansen等人（2010年）和Lenssen等人（2019年）所描述的。这些更新的文件包含上个月的报告，以及以前月份的迟报和更正。

> 在比较季节温度时，使用基于温度的“气象季节”是方便的，定义为整月的分组。因此，12月至1月至2月（DJF）是北半球气象冬季，3月至4月至5月（MAM）是北半球气象春季，6月至7月至8月（JJA）是北半球气象夏季，9月至10月至11月（SON）是北半球气象秋季。将这四个季节串在一起，您就得到了从12月1日开始，到11月30日结束的气象年（D-N）。整个年度是1月到12月（J-D）。[Brian Bartling](https://rpubs.com/BrianBartling/GISTEMP)

可以在Lenssen, N., G. Schmidt, J. Hansen, M. Menne, A. Persin, R. Ruedy, 和 D. Zyss, 2019: Improvements in the GISTEMP uncertainty model. J. Geophys. Res. Atmos., 124, no. 12, 6307-6326, doi:10.1029/2018JD029522 中找到对数据的分析和更多信息。

在[常见问题解答](https://data.giss.nasa.gov/gistemp/faq/)中也有更多详细信息和常见问题的答案。

引用：GISS表面温度分析团队，2023年：GISS表面温度分析（GISTEMP），版本4。NASA Goddard Institute for Space Studies。数据集于2023年07月09日从https://data.giss.nasa.gov/gistemp/获取。

### 在这里获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取数据
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载本周感兴趣的readme和所有数据集

# ISO-8601日期或年/周都可以使用！

tuesdata <- tidytuesdayR::tt_load('2023-07-11')
tuesdata <- tidytuesdayR::tt_load(2023, week = 28)

global_temps <- tuesdata$global_temps
nh_temps <- tuesdata$nh_temps
sh_temps <- tuesdata$sh_temps
zonann_temps <- tuesdata$zonann_temps

# 或者手动读取数据

global_temps <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-07-11/global_temps.csv')
nh_temps <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-07-11/nh_temps.csv')
sh_temps <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-07-11/sh_temps.csv')
zonann_temps <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-07-11/zonann_temps.csv')

```

### 数据字典

# `global_temps.csv`

|variable |class  |description |
|:--------|:------|:-----------|
|Year     |double |Year        |
|Jan      |double |January         |
|Feb      |double |February         |
|Mar      |double |March         |
|Apr      |double |April         |
|May      |double |May         |
|Jun      |double |June         |
|Jul      |double |July         |
|Aug      |double |August         |
|Sep      |double |September         |
|Oct      |double |October         |
|Nov      |double |November         |
|Dec      |double |December         |
|J-D      |double |January-December         |
|D-N      |double |Decemeber-November         |
|DJF      |double |December-January-February         |
|MAM      |double |March-April-May        |
|JJA      |double |June-July-August         |
|SON      |double |September-October-November         |

# `nh_temps.csv`

|variable |class  |description |
|:--------|:------|:-----------|
|Year     |double |Year        |
|Jan      |double |January         |
|Feb      |double |February         |
|Mar      |double |March         |
|Apr      |double |April         |
|May      |double |May         |
|Jun      |double |June         |
|Jul      |double |July         |
|Aug      |double |August         |
|Sep      |double |September         |
|Oct      |double |October         |
|Nov      |double |November         |
|Dec      |double |December         |
|J-D      |double |January-December         |
|D-N      |double |Decemeber-November         |
|DJF      |double |December-January-February         |
|MAM      |double |March-April-May        |
|JJA      |double |June-July-August         |
|SON      |double |September-October-November         |

# `sh_temps.csv`

|variable |class  |description |
|:--------|:------|:-----------|
|Year     |double |Year        |
|Jan      |double |January         |
|Feb      |double |February         |
|Mar      |double |March         |
|Apr      |double |April         |
|May      |double |May         |
|Jun      |double |June         |
|Jul      |double |July         |
|Aug      |double |August         |
|Sep      |double |September         |
|Oct      |double |October         |
|Nov      |double |November         |
|Dec      |double |December         |
|J-D      |double |January-December         |
|D-N      |double |Decemeber-November         |
|DJF      |double |December-January-February         |
|MAM      |double |March-April-May        |
|JJA      |double |June-July-August         |
|SON      |double |September-October-November         |

# `zonann_temps.csv`

|variable |class  |description |
|:--------|:------|:-----------|
|Year     |double |Year        |
|Glob     |double |Global       |
|NHem     |double |Northern Hemisphere        |
|SHem     |double |Southern Hemisphere        |
|24N-90N  |double |24N-90N lattitude     |
|24S-24N  |double |24S-24N lattitude     |
|90S-24S  |double |90S-24S lattitude    |
|64N-90N  |double |64N-90N lattitude    |
|44N-64N  |double |44N-64N lattitude    |
|24N-44N  |double |24N-44N lattitude    |
|EQU-24N  |double |EQU-24N lattitude    |
|24S-EQU  |double |24S-EQU lattitude    |
|44S-24S  |double |44S-24S lattitude    |
|64S-44S  |double |64S-44S lattitude    |
|90S-64S  |double |90S-64S lattitude    |

### Cleaning Script

Missing data was indicated by `***`. Replaced `***` with an empty cell, so these would be NAs. 
