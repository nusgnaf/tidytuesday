# 土拨鼠预测

祝愉快的土拨鼠节！本周我们将探索[groundhog-day.com](https://groundhog-day.com)上的土拨鼠节预测！看看你能否找到比他们的[按年份列出的预测表格](https://groundhog-day.com/predictions)更好的展示方式！对于不熟悉土拨鼠节传统的人来说，如果土拨鼠看到自己的影子并返回洞穴，那就是预示着将有六个星期的冬天。否则，春天将提前到来。我们尝试提供天气数据来配合这个数据集，但到目前为止我们还没有成功。请注意在未来可能会有后续数据集！

注："Oil Springs Ollie"（土拨鼠 #55）已被"Heaven's Wildlife Harvey"（土拨鼠 #70）接替。

## 数据

```{r}
# 选项1：tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-01-30')
## 或者
tuesdata <- tidytuesdayR::tt_load(2024, week = 5)

groundhogs <- tuesdata$groundhogs
predictions <- tuesdata$predictions

# 选项2：直接从 GitHub 读取

groundhogs <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-01-30/groundhogs.csv')
predictions <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-01-30/predictions.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应在数据中得出**因果**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并考虑这些关系背后可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用程序](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `groundhogs.csv`

|variable           |class     |description        |
|:------------------|:---------|:------------------|
|id                 |integer   |A numeric id for this groundhog. |
|slug               |character |The name of the groundhog, in simplified kebab case. |
|shortname          |character |A short version of the name of the groundhog or groundhog substitute. |
|name               |character |The full name of the groundhog or groundhog substitute. |
|city               |character |The city in which the prediction takes place. |
|region             |character |The state or province of the prediction. |
|country            |character |The country of the prediction (USA or Canada). |
|latitude           |double    |The latitude of the city. |
|longitude          |double    |The longitude of the city. |
|source             |character |A url with information about this groundhog. |
|current_prediction |character |A url with information about the most recent prediction. |
|is_groundhog       |logical   |A logical value indicating whether this predictor is a living groundhog. |
|type               |character |A short description of the type of animal or other thing that is said to make the prediction. |
|active             |logical   |A logical value indicating whether this predictor is active (as of 2023). |
|description        |character |A free-text description of the predictor. |
|image              |character |A URL with an image of the predictor. |
|predictions_count  |integer   |The number of predictions available for this predictor. |

# `predictions.csv`

|variable |class     |description |
|:--------|:---------|:-----------|
|id       |integer   |A numeric id for this groundhog. |
|year     |integer   |The year of the prediction. |
|shadow   |logical   |Whether the groundhog saw its shadow, and thus predicts 6 more weeks of winter. |
|details  |character |Free text with more information about this prediction. |

### Cleaning Script

``` r
library(tidyverse)
library(here)
library(fs)
library(httr2)

working_dir <- here::here("data", "2024", "2024-01-30")

groundhogs_all <- httr2::request("https://groundhog-day.com/api/v1/groundhogs/") |> 
  httr2::req_perform() |> 
  httr2::resp_body_json() |> 
  _$groundhogs |> 
  tibble::tibble(data = _) |> 
  tidyr::unnest_wider(data)

groundhogs <- groundhogs_all |> 
  dplyr::select(
    -predictions,
    -successor, # Only 1 groundhog has a successor, we'll mention it in the post.
    -contact
  ) |> 
  janitor::clean_names() |> 
  dplyr::mutate(
    # At least one has a stray comma at the end
    coordinates = stringr::str_remove(coordinates, ",$")
  ) |> 
  tidyr::separate_wider_delim(
    coordinates,
    ",",
    names = c("latitude", "longitude")
  ) |> 
  dplyr::mutate(
    # Correct types
    latitude = as.double(latitude),
    longitude = as.double(longitude),
    is_groundhog = as.logical(is_groundhog),
    active = as.logical(active)
  )

predictions <- groundhogs_all |> 
  dplyr::select(id, predictions) |> 
  tidyr::unnest_longer(predictions) |> 
  tidyr::unnest_wider(predictions) |> 
  dplyr::mutate(shadow = as.logical(shadow))

readr::write_csv(
  groundhogs,
  fs::path(working_dir, "groundhogs.csv")
)
readr::write_csv(
  predictions,
  fs::path(working_dir, "predictions.csv")
)
```
