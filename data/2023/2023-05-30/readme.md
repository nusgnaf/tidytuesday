# 已验证的最长寿者

本周的数据来自[Wikipedia的已验证的最长寿者列表](https://en.wikipedia.org/wiki/List_of_the_verified_oldest_people)，由 [frankiethull 在 GitHub 上](https://github.com/frankiethull/centenarians) 提供。感谢Frank的提交！

> 这些是已知的已验证的最长寿者列表，按年龄以年和天的降序排序。迄今年龄已被独立验证的最长寿者是法国的让娜·卡尔芒（1875年-1997年），享年122岁164天。已知的最长寿男性是日本的木村次郎右卫门（1897年-2013年），享年116岁54天。已知的最长寿存活女性是西班牙的玛丽亚·布拉尼亚斯，年龄为116岁85天。已知的最长寿存活男性是委内瑞拉的胡安·维森特·佩雷斯，年龄为114岁1天。100名最长寿的女性的平均寿命比100名最长寿的男性长几年。

### 在这里获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取数据
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载本周感兴趣的readme和所有数据集

# ISO-8601日期或年/周都可以使用！

tuesdata <- tidytuesdayR::tt_load('2023-05-30')
tuesdata <- tidytuesdayR::tt_load(2023, week = 22)

centenarians <- tuesdata$centenarians

# 或者手动读取数据

centenarians <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-05-30/centenarians.csv')

```

### 数据字典

# `centenarians.csv`

|variable                    |class     |description                 |
|:---------------------------|:---------|:---------------------------|
|rank                        |integer   |This person's overall rank by age.|
|name                        |character |The full name of this person.|
|birth_date                  |date      |This person's birth date.|
|death_date                  |date      |This person's death date (or NA if still alive).|
|age                         |double   |The person's age, either on the day of their death or on the day when the dataset was extracted on 2023-05-25.|
|place_of_death_or_residence |character |Where the person lives now or where they were when they died.|
|gender                      |character |Most likely actually the sex assigned to the person at birth (the source article does not specify).|
|still_alive                 |character |Either "alive" if the person was still alive at the time when the article as referenced, or "deceased" if the person was no longer alive.|

### Cleaning Script

No data cleaning. See the [source GitHub repo](https://github.com/frankiethull/centenarians) for details.
