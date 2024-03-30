# 美国工会会员情况

祝劳动节快乐*！

本周的数据来自由巴里·赫希（乔治亚州立大学）、大卫·麦克弗森（三一大学）和威廉·伊文（迈阿密大学）提供的[美国当前人口调查中的工会会员、覆盖范围和收入](https://www.unionstats.com/)。他们对数据拥有版权，并表示“使用数据需要引用”。

> Unionstats.com 提供了自 1973 年以来的年度工会、非工会和总工资水平，这些数据来自美国当前人口调查。数据库提供了基于回归的整体工会工资差距估计，适用于经济、人口统计组群和行业（私营/公共、产业）。私营部门的工会工资差距高于公共部门，男性的工会工资差距高于女性，黑人男性和白人男性的工会工资差距大致相似，而西班牙裔男性的工会工资差距要远远高于西班牙裔女性。该数据库每年更新一次。

请参阅他们的开放获取文章[《五十年来的 CPS 工资、方法和工会-非工会工资差距》](https://onlinelibrary.wiley.com/doi/10.1111/irel.12330)以了解他们的方法详情和额外的可视化。

* 九月的第一个星期一被[劳动节](https://en.wikipedia.org/wiki/Labor_Day)正式承认，最早于1887年由俄勒冈州承认，并在1894年成为美国官方联邦假日，早于国际社会主义者大会采用的五一国际劳动节[International Workers' Day](https://en.wikipedia.org/wiki/International_Workers%27_Day) 十年。五月一日的选择部分原因是为了纪念1886年芝加哥发生的[黑色星期五事件](https://en.wikipedia.org/wiki/Haymarket_affair)——一场罢工和警察暴力事件。没有理由每个人都不能认可这两天！

## 数据

```{r}
# 选项1：tidytuesdayR 包
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-09-05')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 36)

demographics <- tuesdata$demographics
wages <- tuesdata$wages
states <- tuesdata$states

# 选项2：直接从 GitHub 读取

demographics <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-09-05/demographics.csv')
wages <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-09-05/wages.csv')
states <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-09-05/states.csv')
```

## 参与方式

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应在数据中得出关于**因果关系**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并考虑自己可能潜在的这些关系背后的细微差别。
- 使用 R 或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `demographics.csv`

Data sources:

- 1973-1981: May Current Population Survey (CPS)
- 1982: No union questions available
- 1983-2022: CPS Outgoing Rotation Group (ORG) Earnings Files

The definition of union membership was expanded in 1977 to include "employee associations similar to a union". 

|variable    |class     |description |
|:-----------|:---------|:-----------|
|year        |double    |When the data was collected. |
|sample_size |double    |The number of wage and salary workers ages 16 and over who were surveyed. |
|employment  |double    |Wage and salary employment in thousands. |
|members     |double    |Employed workers who are union members in thousands. |
|covered     |double    |Workers covered by a collective bargaining agreement in thousands. |
|p_members   |double    |Percent of employed workers who are union members. |
|p_covered   |double    |Percent of employed workers who are covered by a collective bargaining agreement. |
|facet       |character |The sector or demographic group contained in this row of data. |

# `wages.csv`

Data sources:

- 1973-1981: May Current Population Survey (CPS)
- 1982: No union questions available
- 1983-2022: CPS Outgoing Rotation Group (ORG) Earnings Files

The definition of union membership was expanded in 1977 to include "employee associations similar to a union". 

|variable                    |class     |description                 |
|:---------------------------|:---------|:---------------------------|
|year                        |double    |When the data was collected. |
|sample_size                 |double    |The number of wage and salary workers ages 16 and over who were surveyed and provided earnings and hours worked information. |
|wage                        |double    |Mean hourly earnings in nominal dollars. |
|at_cap                      |double    |Percent of workers with weekly earnings at the top code of \$999 through 1988, \$1923 in 1989-97, and \$2885 beginning in 1998, with individuals assigned mean earnings above the cap based on annual estimates of the gender-specific Pareto distribution. |
|union_wage                  |double    |Mean wage among union members. |
|nonunion_wage               |double    |Mean wage among nonunion workers. |
|union_wage_premium_raw      |double    |The percentage difference between the union and nonunion wage. |
|union_wage_premium_adjusted |double    |Estimated as exp(b)-1 where b is the regression coefficient on a union membership variable (equal to 1 if union and 0 otherwise) from a semi-logarithmic wage equation, with controls included for worker/job characteristics. Included in the all-worker wage equation are the control variables: years of schooling, potential years of experience [proxied by age minus years of schooling minus 6] and its square [both interacted with gender], and categorical variables for marital status, race and ethnicity, gender, part-time, large metropolitan area, state, public sector, broad industry, and broad occupation. Controls are omitted, as appropriate, for estimates within sectors or by demographic group [i.e., by class, gender, race, or industry sector]. Workers who do not report earnings but instead have them imputed [i.e., assigned] by the Census are removed from the estimation samples in all years, except 1994 and 1995 when imputed earners cannot be identified. Inclusion of imputed earners causes union wages to be understated, nonunion wages overstated, and union-nonunion wage differences understated. For 1994-95, the sample includes imputed earners and estimates in those years have been adjusted to remove the bias from imputation. |
|facet                       |character |The sector or demographic group contained in this row of data. |

# `states.csv`

Data source: Current Population Survey (CPS) Outgoing Rotation Group (ORG) Earnings Files

|variable           |class     |description        |
|:------------------|:---------|:------------------|
|state_census_code  |double    |Census state code used in CPS |
|state              |character |State name. |
|sector             |character |Employment sector. |
|observations       |double    |CPS sample size. |
|employment  |double    |Wage and salary employment in thousands. |
|members     |double    |Employed workers who are union members in thousands. |
|covered     |double    |Workers covered by a collective bargaining agreement in thousands. |
|p_members   |double    |Percent of employed workers who are union members. |
|p_covered   |double    |Percent of employed workers who are covered by a collective bargaining agreement. |
|state_abbreviation |character |State abbreviation. |
|year               |double    |Year of the survey. |

### Cleaning Script

``` r
library(haven)
library(rvest)
library(tidyverse)
library(here)

working_dir <- here::here("data", "2023", "2023-09-05")

base_url <- "https://www.unionstats.com/"

members_url <- "https://www.unionstats.com/members/members_index.html"
members_dta_urls <- rvest::read_html(members_url) |> 
  rvest::html_nodes("td:nth-child(4) a") |> 
  rvest::html_attr("href") |> 
  rvest::url_absolute(members_url)
  
# Fix typos
members_dta_urls <- stringr::str_replace_all(members_dta_urls, "wages", "members")
double_public <- which(
  members_dta_urls == "https://www.unionstats.com/members/dta/members_public.dta"
)
members_dta_urls[[double_public[[1]]]] <- "https://www.unionstats.com/members/dta/members_priv.dta"

members_data <- purrr::map(
  members_dta_urls,
  \(url) {
    haven::read_dta(url) |> 
      dplyr::rename(
        "sample_size" = "nobs",
        "employment" = "empl",
        "members" = "member",
        "p_members" = "pctmem",
        "p_covered" = "pctcov"
      ) |> 
      dplyr::mutate(
        dplyr::across(
          c(year, sample_size),
          as.integer
        )
      ) |> 
      dplyr::mutate(
        facet = stringr::str_extract(url, "members_([^.]+)\\.dta", group = 1)
      )
  }
) |> 
  purrr::list_rbind() |>
  dplyr::mutate(
    facet = dplyr::case_match(
      facet,
      "all" ~ "all wage and salary workers",
      "constr" ~ "construction",
      "manuf" ~ "manufacturing",
      "whole_ret" ~ "wholesale/retail",
      "trans_comm_util" ~ "transportation, communication, and utilities",
      "fire" ~ "finance, insurance, and real estate",           
      "serv" ~ "services",           
      "public_admin" ~ "public administration",
      "priv" ~ "private sector: all",         
      "priv_nonag" ~ "private sector: nonagricultural",     
      "priv_const" ~ "private sector: construction",     
      "priv_manuf" ~ "private sector: manufacturing",     
      "public" ~ "public sector: all",         
      "fed" ~ "public sector: federal",            
      "stateg" ~ "public sector: state government",         
      "localg" ~ "public sector: local government",         
      "postal" ~ "public sector: postal service",
      "police" ~ "public sector: police",        
      "less_th_coll" ~ "demographics: less than college",   
      "coll_plus" ~ "demographics: college or more",
      "male" ~ "demographics: male",
      "fem" ~ "demographics: female",
      "wh_male" ~ "demographics: white male",
      "wh_fem" ~ "demographics: white female",
      "bl_male" ~ "demographics: black male",
      "bl_fem" ~ "demographics: black female",
      "hisp_male" ~ "demographics: hispanic male",
      "hisp_fem" ~ "demographics: hispanic female",  
    )
  )

state_url <- "https://www.unionstats.com/state/dta/state.dta"
state_data <- haven::read_dta(state_url) |> 
  dplyr::select(
    "state_census_code" = "state_cens",
    "state",
    "sector",
    "observations" = "nobs",
    "employment" = "empl",
    "members" = "member",
    "covered",
    "p_members" = "pctmem",
    "p_covered" = "pctcov",
    "state_abbreviation" = "state2",
    "year"
  ) |> 
  dplyr::mutate(
    dplyr::across(
      c(state_census_code, observations, year),
      as.integer
    )
  )

wages_url <- "https://www.unionstats.com/wages/wages_index.html"
wages_dta_urls <- rvest::read_html(wages_url) |> 
  rvest::html_nodes("td:nth-child(4) a") |> 
  rvest::html_attr("href") |> 
  rvest::url_absolute(wages_url)

# One of the URLs has a typo on the site.
wages_dta_urls <- stringr::str_replace_all(wages_dta_urls, "members", "wages")

wages_data <- purrr::map(
  wages_dta_urls,
  \(url) {
    haven::read_dta(url) |> 
      dplyr::select(
        "year",
        "sample_size" = "nobs_wage",
        "wage",
        "at_cap" = "atcap",
        "union_wage" = "memwage",
        "nonunion_wage" = "nonwage",
        "union_wage_premium_raw" = "un_wage_prem_raw",
        "union_wage_premium_adjusted" = "un_wage_prem_adj"
      ) |> 
      dplyr::mutate(
        dplyr::across(
          c(year, sample_size),
          as.integer
        )
      ) |> 
      dplyr::mutate(
        facet = stringr::str_extract(url, "wages_([^.]+)\\.dta", group = 1)
      )
  }
) |> 
  purrr::list_rbind() |>
  dplyr::mutate(
    facet = dplyr::case_match(
      facet,
      "all" ~ "all wage and salary workers",
      "constr" ~ "construction",
      "manuf" ~ "manufacturing",
      "whole_ret" ~ "wholesale/retail",
      "trans_comm_util" ~ "transportation, communication, and utilities",
      "fire" ~ "finance, insurance, and real estate",           
      "serv" ~ "services",           
      "public_admin" ~ "public administration",
      "priv" ~ "private sector: all",         
      "priv_nonag" ~ "private sector: nonagricultural",     
      "priv_const" ~ "private sector: construction",     
      "priv_manuf" ~ "private sector: manufacturing",     
      "public" ~ "public sector: all",         
      "fed" ~ "public sector: federal",            
      "stateg" ~ "public sector: state government",         
      "localg" ~ "public sector: local government",         
      "postal" ~ "public sector: postal service",
      "police" ~ "public sector: police",        
      "less_th_coll" ~ "demographics: less than college",   
      "coll_plus" ~ "demographics: college or more",
      "male" ~ "demographics: male",
      "fem" ~ "demographics: female",
      "wh_male" ~ "demographics: white male",
      "wh_fem" ~ "demographics: white female",
      "bl_male" ~ "demographics: black male",
      "bl_fem" ~ "demographics: black female",
      "hisp_male" ~ "demographics: hispanic male",
      "hisp_fem" ~ "demographics: hispanic female",  
    )
  )

readr::write_csv(
  members_data,
  here::here(working_dir, "demographics.csv")
)
readr::write_csv(
  wages_data,
  here::here(working_dir, "wages.csv")
)
readr::write_csv(
  state_data,
  here::here(working_dir, "states.csv")
)
```
