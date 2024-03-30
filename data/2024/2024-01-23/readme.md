# 英格兰小镇青少年的教育程度

本周的[数据集](https://www.ons.gov.uk/file?uri=/peoplepopulationandcommunity/educationandchildcare/datasets/educationalattainmentofyoungpeopleinenglishtownsdata/200708201819/youngpeoplesattainmentintownsreferencetable1.xlsx)来自[英国国家统计局](https://www.ons.gov.uk/)。
该数据集在2023年7月的文章["为什么小镇的儿童和青少年在学业上比大城镇的好？"](https://www.ons.gov.uk/peoplepopulationandcommunity/educationandchildcare/articles/whydochildrenandyoungpeopleinsmallertownsdobetteracademicallythanthoseinlargertowns/2023-07-25)中进行了探讨。
感谢[Andrea Carpignani](https://github.com/acarpignani)提供数据集建议。

本周的文章包含了几个图表，其中一个是交互式的。你能重现它们吗？你能在数据中找到任何没有在文章中探讨的内容吗？

## 数据

```{r}
# 选项1：tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2024-01-23')
## 或者
tuesdata <- tidytuesdayR::tt_load(2024, week = 4)

english_education <- tuesdata$english_education

# 选项2：直接从 GitHub 读取

english_education <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2024/2024-01-23/english_education.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应在数据中得出**因果**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用所提供的数据来练习数据整理和绘图技术，并考虑这些关系下可能存在的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用程序](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `english_education.csv`

|variable |class |description |
|:--------|:-----|:-----------|
|town11cd |character |Town/city geography code (2011) |
|town11nm |character |Town/city geography name (2011) |
|population_2011 |numeric |Measure of the usual resident population in the town/city |
|size_flag |character |Size category of the built-up area or built-up area subdivision based on resident population (from Census 2011) |
|rgn11nm |character |English region name |
|coastal |character |Variable used to describe towns as coastal or non-coastal |
|coastal_detailed |character |Coastal towns split by size and by seaside towns and other coastal (non-seaside) towns |
|ttwa11cd |character |Travel-to-work area code (Census 2011 version) |
|ttwa11nm |character |Travel-to-work area name (Census 2011 version) |
|ttwa_classification |character |Travel to work area classification |
|job_density_flag |character |Variable used to describe towns as working, residential or mixed. |
|income_flag |character |Variable used to describe towns as lower income deprivation, mid income deprivation or higher income deprivatio |
|university_flag |character |Variable used to describe whether the town/city has a university |
|level4qual_residents35_64_2011 |character |Proportion of the town/city residents aged 35-64 with a Level 4 qualification or above. |
|ks4_2012_2013_counts |numeric |Count of pupils in the town/city in the 2012/13 Key stage 4 cohort |
|key_stage_2_attainment_school_year_2007_to_2008 |numeric |Proportion of pupils that achieved level 4 or above (expected level) in key stage 2 in English and Maths in the 2007 to 2008 school year |
|key_stage_4_attainment_school_year_2012_to_2013 |numeric |Proportion of pupils that achieved 5 GCSE or more, including English and Maths, with grades A*-C in the 2012 to 2013 school year |
|level_2_at_age_18 |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort that achieved level 2 qualifications at the age 18. |
|level_3_at_age_18 |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort that achieved level 3 qualifications at the age 18. |
|activity_at_age_19_full_time_higher_education |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort in full time higher education at the age 19. |
|activity_at_age_19_sustained_further_education |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort in sustained further education at the age 19. |
|activity_at_age_19_appprenticeships |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort in an apprenticeship at the age 19. |
|activity_at_age_19_employment_with_earnings_above_0 |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort in sustained employment at the age 19. |
|activity_at_age_19_employment_with_earnings_above_10_000 |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort in sustained employment earning £10,000 or above at the age 19. |
|activity_at_age_19_out_of_work |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort claiming out-of-work benefits at the age 19. |
|highest_level_qualification_achieved_by_age_22_less_than_level_1 |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort with less than a Level 1 qualification at age 22. |
|highest_level_qualification_achieved_by_age_22_level_1_to_level_2 |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort with a level 1 or level 2 qualification at age 22. |
|highest_level_qualification_achieved_by_age_22_level_3_to_level_5 |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort with level 3, level 4 or level 5 qualification at age 22. |
|highest_level_qualification_achieved_by_age_22_level_6_or_above |numeric |Proportion of the town/city's 2012/13 key stage 4 cohort with level 6 or above qualification at age 22. |
|highest_level_qualification_achieved_b_age_22_average_score |numeric |Town/city highest qualification average score based on highest levels of qualifications achieved of the 2012/13 KS4 cohort. |
|education_score |numeric |Town/city education score based on attainment levels of the 2012/13 Key stage 4 cohort. |

### Cleaning Script

``` r
library(tidyverse)
library(here)
library(fs)
library(withr)

working_dir <- here::here("data", "2024", "2024-01-23")

xls_path <- withr::local_tempfile(fileext = ".xlsx")
download.file(
  "https://www.ons.gov.uk/file?uri=/peoplepopulationandcommunity/educationandchildcare/datasets/educationalattainmentofyoungpeopleinenglishtownsdata/200708201819/youngpeoplesattainmentintownsreferencetable1.xlsx",
  xls_path,
  mode = "wb"
)

english_education <- readxl::read_xlsx(xls_path, sheet = "Data", na = "*") |> 
  janitor::clean_names()

readr::write_csv(
  english_education,
  fs::path(working_dir, "english_education.csv")
)
```
