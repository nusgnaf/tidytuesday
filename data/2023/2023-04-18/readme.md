# 新石器时代的创始作物

本周的数据来自于[西南亚的“新石器时代创始作物”研究汇编](https://github.com/joeroe/SWAsiaNeolithicFounderCrops/)。["重新审视西南亚的“新石器时代创始作物”概念"](https://link.springer.com/article/10.1007/s00334-023-00917-1)是一篇使用这些数据的开放获取研究论文。感谢您分享您的研究，[@joeroe](https://github.com/joeroe)！

根据[关于该数据集的社交媒体帖子](https://fosstodon.org/@joeroe@archaeo.social/110186477750041419)：

> 八种“创始作物”—— 小麦、粒小麦、大麦、扁豆、豌豆、鹰嘴豆、苦蕾和亚麻——长期以来一直被认为是#新石器时代经济的基石。
> ...
> 我们发现，新石器时代的经济比以前想象的要多样化得多，包括了数十种谷物、豆类、小种子草本植物、十字花科植物、假谷物、莎草、开花植物、树木和灌木。尤其是自由脱粒小麦、草豌豆、蚕豆和“新型”谷物在广泛种植中尤为重要。

阅读帖子以获取关于这些数据的背景信息！

### 在这里获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取数据
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣的周的自述文件和所有数据集

# 使用 ISO-8601 日期格式或者年份/周数格式！

tuesdata <- tidytuesdayR::tt_load('2023-04-18')
tuesdata <- tidytuesdayR::tt_load(2023, week = 16)

founder_crops <- tuesdata$founder_crops

# 或者手动读取数据

founder_crops <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-04-18/founder_crops.csv')
```

### 数据字典

# `founder_crops.csv`

|variable          |class     |description       |
|:-----------------|:---------|:-----------------|
|source            |character |the source database|
|source_id         |character |id of this record in the source database|
|source_site_name  |character |name of the site in the source database|
|site_name         |character |standardized site name|
|latitude          |double    |latitude          |
|longitude         |double    |longitude         |
|phase             |character |phase             |
|phase_description |character |phase_description |
|phase_code        |character |phase_code        |
|age_start         |double    |oldest date for the record, in years before 1950 CE (years BP)|
|age_end           |double    |most recent date for the record, in years before 1950 CE (years BP)|
|taxon_source      |character |taxonomy as stated in the course database|
|n                 |double    |number of individuals in the sample|
|prop              |double    |proportion of this sample that contains this crop|
|reference         |character |papers describing this data|
|taxon_detail      |character |canonical name for this taxonomic group|
|taxon             |character |taxonomic details for this sample; this and the previous column may have been swapped in the source|
|genus             |character |genus             |
|family            |character |family            |
|category          |character |broad category for this sample|
|founder_crop      |character |traditional founder crop to which this sample belongs|
|edibility         |character |parts of the plant that are edible, if any|
|grass_type        |character |for grasses, the category for this sample|
|legume_type       |character |for legumes, the category for this sample|

### Cleaning Script

```r
# All packages used in this script:
library(tidyverse)
library(here)

url <- "https://raw.githubusercontent.com/joeroe/SWAsiaNeolithicFounderCrops/main/analysis/data/derived_data/swasia_neolithic_flora.tsv"
founder_crops <- readr::read_tsv(url) |> 
  # de-duplicate the reference column
  dplyr::mutate(
    reference = purrr::map_chr(
      reference,
      \(ref) {
        if (is.na(ref)) {
          return(NA)
        }
        ref |> 
          stringr::str_split_1(";") |> 
          unique() |> 
          paste(collapse = ";")
      }
    )
  )

readr::write_csv(
  founder_crops,
  here::here(
    "data",
    "2023",
    "2023-04-18",
    "founder_crops.csv"
  )
)
```
