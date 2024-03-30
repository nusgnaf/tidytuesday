# R-Ladies 分会活动

R-Ladies Global 是数据科学领域一个鼓舞人心的社区、赋权和多样性的故事。由 Gabriela de Queiroz 创立，R-Ladies 开始于一场基层运动，其简单使命是：促进 R 编程社区的性别多样性，并为女性和性别少数群体提供一个友好的学习、合作和在数据科学中取得成功的空间。[R-Ladies Global 网站](https://rladies.org/)

本周的数据来自 [Federica Gazzelloni](https://github.com/Fgazzelloni) 的 [演示](https://youtu.be/EstytFNjrWc) [R-Ladies 分会：使演讲适合各种观众](https://github.com/Fgazzelloni/RLadies-Chapters-Making-Talks-Work-for-Diverse-Audiences/tree/main)，数据来自 [rladies meetup-archive](https://github.com/rladies/meetup_archive)。

## 数据

```{r}
# 选项1：tidytuesdayR包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-11-21')
## OR
tuesdata <- tidytuesdayR::tt_load(2023, week = 47)

rladies_chapters <- tuesdata$rladies_chapters

# 选项2：直接从GitHub读取

rladies_chapters <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-11-21/rladies_chapters.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该从数据中得出**因果关系**的结论。影响所有数据的各种调节变量很多，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用R或其他编程语言创建可视化、模型、[Shiny应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `rladies_chapters.csv`

|variable |class     |description |
|:--------|:---------|:-----------|
|id       |double    |event id          |
|chapter  |character |r-ladies chapter name     |
|title    |character |event title       |
|date     |double    |event date        |
|location |character |event location if online or in person    |
|year     |double    |event year        |

### Cleaning Script

Cleaning script from @Fgazzelloni in [her tidytuesday github issue](https://github.com/rfordatascience/tidytuesday/issues/632).

``` r
library(tidyverse)
library(jsonlite)
data <- jsonlite::fromJSON('https://raw.githubusercontent.com/rladies/meetup_archive/main/data/events.json')

chapters <- data %>%
  select(1,2,3,7,8)%>% 
  rename(chapter=group_urlname)%>%
  mutate(location=ifelse(location=="Online event","online","inperson"),
         title=sub(".*-- ","",title),
         title=gsub("\\s*\\([^\\)]+\\)","",title)) %>%
  filter(!str_detect(title,regex("canceled|cancelled",ignore_case=T)),
         !chapter%in%c("RLadiesJeddah","muhq_deleted@4633@rladies-ushuaia",
                      "muhq_deleted@9919@notopic@508502","notopic@544550"))%>%
  arrange(desc(date))%>%
  filter(year(date)<2024)%>%
  mutate(year=year(date))

# to download the n. of attendees
library(meetupr)
# meetupr::get_event_attendees("event-id")

id <- rladies_chapters$id
id1 <- id[1:25]

attendees <- function(id) {
  dat<- meetupr::get_event_attendees(id)%>%
   dim()
    dat[1]
}

mylist <- lapply(id1,attendees)

first25 <- mylist%>%unlist()
first25_events <- rladies_chapters[1:25,]%>%
  cbind(first25)
```

