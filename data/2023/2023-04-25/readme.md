# 伦敦马拉松

本周的数据来自 Nicola Rennie 的[LondonMarathon R 包](https://github.com/nrennie/LondonMarathon)。这是一个包含两个数据集的 R 包，这些数据集是从维基百科（2022年11月1日）上关于伦敦马拉松获奖者和一些一般数据的信息中爬取的。关于数据集是如何创建的，以及一些分析，都在 Nicola 的博文["Scraping London Marathon data with {rvest}"](https://nrennie.rbind.io/blog/web-scraping-rvest-london-marathon/)中有描述。感谢 @nrennie 汇总这些数据集！

### 在这里获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取数据
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣的周的自述文件和所有数据集

# 使用 ISO-8601 日期格式或者年份/周数格式！

tuesdata <- tidytuesdayR::tt_load('2023-04-25')
tuesdata <- tidytuesdayR::tt_load(2023, week = 17)

winners <- tuesdata$winners
london_marathon <- tuesdata$london_marathon


# 或者手动读取数据

winners <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-04-25/winners.csv')
london_marathon <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-04-25/london_marathon.csv')


```

### 数据字典

# `winners.csv`

|variable    |class     |description |
|:-----------|:---------|:-----------|
|Category    |character |Category of race           |
|Year        |double    |Year                       |
|Athlete     |character |Name of the winner         |
|Nationality |character |Nationality of the winner  |
|Time        |double    |Winning time               |


# `london_marathon.csv`

|variable         |class     |description      |
|:----------------|:---------|:----------------|
|Date             |double    |Date of the race                        |
|Year             |double    |Year                                    |
|Applicants       |double    |Number of people who applied            |
|Accepted         |double    |Number of people accepted               |
|Starters         |double    |Number of people who started            |
|Finishers        |double    |Number of people who finished           |
|Raised           |double    |Amount raised for charity (£ millions)  |
|Official charity |character |Official charity                        |



### Cleaning Script

No data cleaning
