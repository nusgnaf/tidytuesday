# 儿童看护成本

母亲节快乐！本周的数据来自[国家儿童看护价格数据库](https://www.dol.gov/agencies/wb/topics/featured-childcare)。

> 国家儿童看护价格数据库(NDCP)是县级别上最全面的联邦儿童看护价格信息来源。该数据库提供按照儿童看护提供者类型、儿童年龄和县特征划分的儿童看护价格数据。数据可追溯至2008年至2018年。

本周感谢Thomas Mock提供了这份数据，同时也要向[Jon Schwabish在Twitter上](https://twitter.com/jschwabish/status/1626597782491639810?s=46&t=avwSC7an3lwX3FH35u0gMA)指出原始政府发布的地图缺少标签表示感谢。

注意：本数据集暗示“双亲”指的是一男一女。我们意识到这并不反映每个充满爱的家庭的现实情况。

### 在这里获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取数据
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载本周感兴趣的readme和所有数据集

# ISO-8601日期或年/周都可以使用！

tuesdata <- tidytuesdayR::tt_load('2023-05-09')
tuesdata <- tidytuesdayR::tt_load(2023, week = 19)

childcare_costs <- tuesdata$childcare_costs
counties <- tuesdata$counties

# 或者手动读取数据

childcare_costs <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-05-09/childcare_costs.csv')
counties <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-05-09/counties.csv')
```

### 数据字典

# `childcare_costs.csv`

|variable                  |class  |description               |
|:-------------------------|:------|:-------------------------|
|county_fips_code          |double |Four- or five-digit number that uniquely identifies the county in a state. The first two digits (for five-digit numbers) or 1 digit (for four-digit numbers) refer to the FIPS code of the state to which the county belongs. |
|study_year                |double |Year the data collection began for the market rate survey and in which ACS data is representative of, or the study publication date. |
|unr_16                    |double |Unemployment rate of the population aged 16 years old or older.|
|funr_16                   |double |Unemployment rate of the female population aged 16 years old or older. |
|munr_16                   |double |Unemployment rate of the male population aged 16 years old or older. |
|unr_20to64                |double |Unemployment rate of the population aged 20 to 64 years old. |
|funr_20to64               |double |Unemployment rate of the female population aged 20 to 64 years old. |
|munr_20to64               |double |Unemployment rate of the male population aged 20 to 64 years old. |
|flfpr_20to64              |double |Labor force participation rate of the female population aged 20 to 64 years old. |
|flfpr_20to64_under6       |double |Labor force participation rate of the female population aged 20 to 64 years old who have children under 6 years old. |
|flfpr_20to64_6to17        |double |Labor force participation rate of the female population aged 20 to 64 years old who have children between 6 and 17 years old. |
|flfpr_20to64_under6_6to17 |double |Labor force participation rate of the female population aged 20 to 64 years old who have children under 6 years old and between 6 and 17 years old. |
|mlfpr_20to64              |double |Labor force participation rate of the male population aged 20 to 64 years old. |
|pr_f                      |double |Poverty rate for families. |
|pr_p                      |double |Poverty rate for individuals. |
|mhi_2018                  |double |Median household income expressed in 2018 dollars. |
|me_2018                   |double |Median earnings expressed in 2018 dollars for the population aged 16 years old or older. |
|fme_2018                  |double |Median earnings for females expressed in 2018 dollars for the population aged 16 years old or older. |
|mme_2018                  |double |Median earnings for males expressed in 2018 dollars for the population aged 16 years old or older. |
|total_pop                 |double |Count of the total population. |
|one_race                  |double |Percent of population that identifies as being one race. |
|one_race_w                |double |Percent of population that identifies as being one race and being only White or Caucasian. |
|one_race_b                |double |Percent of population that identifies as being one race and being only Black or African American. |
|one_race_i                |double |Percent of population that identifies as being one race and being only American Indian or Alaska Native. |
|one_race_a                |double |Percent of population that identifies as being one race and being only Asian. |
|one_race_h                |double |Percent of population that identifies as being one race and being only Native Hawaiian or Pacific Islander. |
|one_race_other            |double |Percent of population that identifies as being one race and being a different race not previously mentioned. |
|two_races                 |double |Percent of population that identifies as being two or more races. |
|hispanic                  |double |Percent of population that identifies as being Hispanic or Latino regardless of race. |
|households                |double |Number of households. |
|h_under6_both_work        |double |Number of households with children under 6 years old with two parents that are both working. |
|h_under6_f_work           |double |Number of households with children under 6 years old with two parents with only the father working. |
|h_under6_m_work           |double |Number of households with children under 6 years old with two parents with only the mother working. |
|h_under6_single_m         |double |Number of households with children under 6 years old with a single mother. |
|h_6to17_both_work         |double |Number of households with children between 6 and 17 years old with two parents that are both working. |
|h_6to17_fwork             |double |Number of households with children between 6 and 17 years old with two parents with only the father working. |
|h_6to17_mwork             |double |Number of households with children between 6 and 17 years old with two parents with only the mother working. |
|h_6to17_single_m          |double |Number of households with children between 6 and 17 years old with a single mother. |
|emp_m                     |double |Percent of civilians employed in management, business, science, and arts occupations aged 16 years old or older in the county. |
|memp_m                    |double |Percent of male civilians employed in management, business, science, and arts occupations aged 16 years old or older in the county. |
|femp_m                    |double |Percent of female civilians employed in management, business, science, and arts occupations aged 16 years old or older in the county. |
|emp_service               |double |Percent of civilians employed in service occupations aged 16 years old and older in the county. |
|memp_service              |double |Percent of male civilians employed in service occupations aged 16 years old and older in the county. |
|femp_service              |double |Percent of female civilians employed in service occupations aged 16 years old and older in the county. |
|emp_sales                 |double |Percent of civilians employed in sales and office occupations aged 16 years old and older in the county. |
|memp_sales                |double |Percent of male civilians employed in sales and office occupations aged 16 years old and older in the county. |
|femp_sales                |double |Percent of female civilians employed in sales and office occupations aged 16 years old and older in the county. |
|emp_n                     |double |Percent of civilians employed in natural resources, construction, and maintenance occupations aged 16 years old and older in the county. |
|memp_n                    |double |Percent of male civilians employed in natural resources, construction, and maintenance occupations aged 16 years old and older in the county. |
|femp_n                    |double |Percent of female civilians employed in natural resources, construction, and maintenance occupations aged 16 years old and older in the county. |
|emp_p                     |double |Percent of civilians employed in production, transportation, and material moving occupations aged 16 years old and older in the county. |
|memp_p                    |double |Percent of male civilians employed in production, transportation, and material moving occupations aged 16 years old and older in the county. |
|femp_p                    |double |Percent of female civilians employed in production, transportation, and material moving occupations aged 16 years old and older in the county. |
|mcsa                      |double |Weekly, full-time median price charged for Center-Based Care for those who are school age based on the results reported in the market rate survey report for the county or the rate zone/cluster to which the county is assigned. |
|mfccsa                    |double |Weekly, full-time median price charged for Family Childcare for those who are school age based on the results reported in the market rate survey report for the county or the rate zone/cluster to which the county is assigned.|
|mc_infant                 |double |Aggregated weekly, full-time median price charged for Center-based Care for infants (i.e. aged 0 through 23 months). |
|mc_toddler                |double |Aggregated weekly, full-time median price charged for Center-based Care for toddlers (i.e. aged 24 through 35 months). |
|mc_preschool              |double |Aggregated weekly, full-time median price charged for Center-based Care for preschoolers (i.e. aged 36 through 54 months). |
|mfcc_infant               |double |Aggregated weekly, full-time median price charged for Family Childcare for infants (i.e. aged 0 through 23 months). |
|mfcc_toddler              |double |Aggregated weekly, full-time median price charged for Family Childcare for toddlers (i.e. aged 24 through 35 months). |
|mfcc_preschool            |double |Aggregated weekly, full-time median price charged for Family Childcare for preschoolers (i.e. aged 36 through 54 months). |

# `counties.csv`

|variable           |class     |description        |
|:------------------|:---------|:------------------|
|county_fips_code   |double    |Four- or five-digit number that uniquely identifies the county in a state. The first two digits (for five-digit numbers) or 1 digit (for four-digit numbers) refer to the FIPS code of the state to which the county belongs. |
|county_name        |character |The full name of the county. |
|state_name         |character |The full name of the state in which the county is found. |
|state_abbreviation |character |The two-letter state abbreviation. |

### Cleaning Script

``` r
# All packages used in this script:
library(tidyverse)
library(here)
library(withr)

url <- "https://www.dol.gov/sites/dolgov/files/WB/media/nationaldatabaseofchildcareprices.xlsx"
temp_xlsx <- withr::local_tempfile(fileext = ".xlsx")
download.file(url, temp_xlsx, mode = "wb")

childcare_costs_raw <- readxl::read_xlsx(temp_xlsx) |>
  janitor::clean_names() |> 
  # There are 15 constant columns. Get rid of those.
  janitor::remove_constant(quiet = FALSE)

# The file is very large, but it contains a lot of duplicate data. Extract
# duplications into their own tables.
counties <- childcare_costs_raw |> 
  dplyr::distinct(county_fips_code, county_name, state_name, state_abbreviation)
childcare_costs <- childcare_costs_raw |> 
  dplyr::select(
    -county_name,
    -state_name,
    -state_abbreviation,
    # Original data also contained unadjusted + adjusted dollars, let's just
    # keep the 2018 adjustments.
    -mhi, -me, -fme, -mme,
    # A number of columns have fine-grained breakdowns by age, and then also
    # broader categories. Let's only keep the categories ("infant" vs 0-5
    # months, 6-11 monts, etc)
    -ends_with("bto5"), -ends_with("6to11"), -ends_with("12to17"), 
    -ends_with("18to23"), -ends_with("24to29"), -ends_with("30to35"),
    -ends_with("36to41"), -ends_with("42to47"), -ends_with("48to53"),
    -ends_with("54to_sa"),
    # Since we aren't worrying about the unaggregated columns, we can ignore the
    # flags indicating how those columns were aggregated into the combined
    # columns.
    -ends_with("_flag"),
    # Original data has both median and 75th percentile for a number of columns.
    # We'll simplify.
    -starts_with("x75"),
    # While important for wider research, we don't need to keep the (many)
    # variables describing whether certain data was imputed.
    -starts_with("i_")
  )

readr::write_csv(
  childcare_costs,
  here::here(
    "data",
    "2023",
    "2023-05-09",
    "childcare_costs.csv"
  )
)

readr::write_csv(
  counties,
  here::here(
    "data",
    "2023",
    "2023-05-09",
    "counties.csv"
  )
)
```
