# 好莱坞年龄差距

本周的数据来自[好莱坞年龄差距](https://hollywoodagegap.com/)，由[Data Is Plural](https://www.data-is-plural.com/archive/2018-02-07-edition/)提供。

> 这是一个展示电影爱情利益者年龄差距的信息网站。

数据遵循一定的规则：

> 这两个（或更多）演员扮演的是真实的爱情利益者（不仅仅是朋友、同事或其他非浪漫类型的关系）

> 两位演员中最年轻的至少为17岁

> 不包括动画角色

我们之前提供过有关[贝克德尔测试](https://tidytues.day/2021/2021-03-09)的数据集。了解这些数据集之间是否存在任何相关性可能会很有趣！贝克德尔测试数据集还包括有关用于该数据集的电影的其他信息。

注意：年龄差距数据集包含“性别”列，其中始终包含“man”或“woman”的值。这些值似乎表示每部电影中的*角色*的身份认同。其中一些值与*演员*的身份认同不匹配。如果数据中有任何角色被错误地性别化，我们深感抱歉！

### 在此处获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# 可以使用ISO-8601日期或年/周

tuesdata <- tidytuesdayR::tt_load('2023-02-14')
tuesdata <- tidytuesdayR::tt_load(2023, week = 7)

age_gaps <- tuesdata$age_gaps

# 或者手动读取数据

age_gaps <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-14/age_gaps.csv')
```

### 数据字典

# `age_gaps.csv`

|variable           |class     |description        |
|:------------------|:---------|:------------------|
|movie_name         |character |Name of the film   |
|release_year       |integer   |Release year       |
|director           |character |Director of the film|
|age_difference     |integer   |Age difference between the characters in whole years |
|couple_number      |integer   |An identifier for the couple in case multiple couples are listed for this film |
|actor_1_name       |character |The name of the older actor in this couple|
|actor_2_name       |character |The name of the younger actor in this couple|
|character_1_gender |character |The gender of the older character, as identified by the person who submitted the data for this couple|
|character_2_gender |character |The gender of the younger character, as identified by the person who submitted the data for this couple|
|actor_1_birthdate  |date      |The birthdate of the older member of the couple|
|actor_2_birthdate  |date      |The birthdate of the younger member of the couple|
|actor_1_age        |integer   |The age of the older actor when the film was released|
|actor_2_age        |integer   |The age of the younger actor when the film was released|


### Cleaning Script

```r
library(tidyverse)
library(here)
library(janitor)

age_gaps <- read_csv(
  "http://hollywoodagegap.com/movies.csv",
) |> 
  clean_names()

glimpse(age_gaps)

# Quickly check that the columns make sense.
length(vctrs::vec_cast(age_gaps$release_year, integer())) == nrow(age_gaps)
length(vctrs::vec_cast(age_gaps$age_difference, integer())) == nrow(age_gaps)
unique(age_gaps$actor_1_gender)
!any(is.na(as.Date(age_gaps$actor_1_birthdate)))
length(vctrs::vec_cast(age_gaps$actor_1_age, integer())) == nrow(age_gaps)
unique(age_gaps$actor_2_gender)
!any(is.na(as.Date(age_gaps$actor_2_birthdate)))
length(vctrs::vec_cast(age_gaps$actor_2_age, integer())) == nrow(age_gaps)

# Formally set the dates to dates.
age_gaps <- age_gaps |> 
  mutate(
    across(
      ends_with("birthdate"),
      as.Date
    )
  )

# Try to get a better understanding of the "gender" columns.
count(age_gaps, actor_1_gender)
count(age_gaps, actor_2_gender)

# The order of the characters doesn't seem to be consistent
age_gaps |> 
  summarize(
    p_1_older = mean(actor_1_age > actor_2_age),
    p_1_male = mean(actor_1_gender == "man"),
    p_1_female_2_male = mean(actor_1_gender == "woman" & actor_2_gender == "man"),
    p_1_first_alpha = mean(actor_1_name < actor_2_name)
  )

# For the most part, they put the man first if there's a man in the couple. It
# doesn't look like there's a strict rule, though. But beware: Some movies have
# more than 1 couple! Let's use all that to rebuild the data, always putting the
# older character first.
age_gaps <- age_gaps |> 
  mutate(
    couple_number = row_number(),
    .by = "movie_name"
  ) |> 
  pivot_longer(
    cols = starts_with(c("actor_1_", "actor_2_")),
    names_to = c(NA, NA, ".value"),
    names_sep = "_"
  ) |> 
  # Put the older actor first.
  arrange(desc(age_difference), movie_name, birthdate) |> 
  # While we have it pivoted, correct Elliot Page's name. I don't know if other
  # actors are similarly deadnamed, but at least we can fix this one. Note that
  # the *characters* played by Elliot in these particular films were women, so
  # I'll leave the gender as-is.
  mutate(
    name = case_match(
      name,
      "Ellen Page" ~ "Elliot Page",
      .default = name
    )
  ) |>
  mutate(
    position = row_number(),
    .by = c("movie_name", "couple_number")
  ) |> 
  pivot_wider(
    names_from = "position",
    names_glue = "actor_{position}_{.value}",
    values_from = c("name", "gender", "birthdate", "age")
  )

# The gender isn't really the actor so much as it is the character. Let's
# correct that.
age_gaps <- age_gaps |> 
  rename(
    "character_1_gender" = "actor_1_gender",
    "character_2_gender" = "actor_2_gender"
  )

glimpse(age_gaps)

# Save the data.
write_csv(
  age_gaps,
  here::here(
    "data", "2023", "2023-02-14",
    "age_gaps.csv"
  )
)
```
