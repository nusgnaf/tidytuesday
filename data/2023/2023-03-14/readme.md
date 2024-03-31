# 欧洲药物开发

本周的数据来自[欧洲药品管理局](https://www.ema.europa.eu/en/medicines/download-medicine-data)，通过[Github上的Miquel Anglada Girotto](https://github.com/MiqG/EMA-Data-Scratching-with-RSelenium)获取。我们使用了[所有人类和兽药的EMA公共评估报告的源表](https://www.ema.europa.eu/sites/default/files/Medicines_output_european_public_assessment_reports.xlsx)，而不是Miquel的抓取数据。

Miquel在[这篇文章](https://towardsdatascience.com/dissecting-28-years-of-european-pharmaceutical-development-3affd8f87dc0)中讨论了他对数据的探索。

### 在此处获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# 可以使用 ISO-8601 日期或年/周

tuesdata <- tidytuesdayR::tt_load('2023-03-14')
tuesdata <- tidytuesdayR::tt_load(2023, week = 11)

drugs <- tuesdata$drugs

# 或者手动读取数据

drugs <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-03-14/drugs.csv')
```

### 数据字典

# `drugs.csv`

|variable                                    |class     |description                                 |
|:-------------------------------------------|:---------|:-------------------------------------------|
|category                                    |character |human or veterinary |
|medicine_name                               |character |brand name of the medicine |
|therapeutic_area                            |character |semicolon-separated list of therapeutic areas; might benefit from further cleaning |
|common_name                                 |character |international non-proprietary name (INN) or common name |
|active_substance                            |character |common name of the active chemical in the drug |
|product_number                              |character |EMEA/H/C/ (human) or EMEA/V/C/ (veterinary) number for the drug |
|patient_safety                              |logical   |has patient safety notices |
|authorisation_status                        |character |whether the drug was authorised, withdrawn, or refused (or does not have a reported status) |
|atc_code                                    |character |anatomical therapeutic chemical code |
|additional_monitoring                       |logical   |when true, the medicine is under additional monitoring, meaning that it is monitored even more intensively than other medicines|
|generic                                     |logical   |whether the drug is a generic medicine, which is developed to be the same as a medicine that has already been authorised, called the reference medicine. A generic medicine contains the same active substance(s) as the reference medicine, and is used at the same dose(s) to treat the same disease(s) |
|biosimilar                                  |logical   |whether the drug is a biosimilar medicine, which is a biological medicine highly similar to another already approved biological medicine called the reference medicine.|
|conditional_approval                        |logical   |whether the medicine received a conditional marketing authorisation. This was granted in the interest of public health because the medicine addresses an unmet medical need and the benefit of immediate availability outweighs the risk from less comprehensive data than normally required. |
|exceptional_circumstances                   |logical   |whether the medicine was authorised under exceptional circumstances, because the applicant was unable to provide comprehensive data on the efficacy and safety of the medicine under normal conditions of use. This can happen because the condition to be treated is rare or because collection of full information is not possible or is unethical. |
|accelerated_assessment                      |logical   |whether the medicine had an accelerated assessment. This means that it is a medicine of major interest for public health, so its timeframe for review was 150 evaluation days rather than 210. |
|orphan_medicine                             |logical   |whether the medicine was designated an orphan medicine. This means that it was developed for use against a rare, life-threatening or chronically debilitating condition or, for economic reasons, it would be unlikely to have been developed without incentives. |
|marketing_authorisation_date                |date      |date on which the drug was authorised |
|date_of_refusal_of_marketing_authorisation  |date      |date on which the drug was refused |
|marketing_authorisation_holder_company_name |character |name of the company that is authorised to market the drug |
|pharmacotherapeutic_group                   |character |the target of the drug; might benefit from further cleaning |
|date_of_opinion                             |date      |date on which the opinion was made |
|decision_date                               |date      |date on which the latest decision was made |
|revision_number                             |integer   |integer revision number |
|condition_indication                        |character |language describing the specific uses of the drug |
|species                                     |character |for veterinary medicines, the target species; might benefit from further cleaning |
|first_published                             |datetime  |datetime when the information was first published |
|revision_date                               |datetime  |datetime of the most recent revision |
|url                                         |character |url for details about the drug and submission |


### Cleaning Script

```r
library(readxl)
library(tidyverse)
library(janitor)
library(here)

# xlsx file downloaded from https://www.ema.europa.eu/en/medicines/download-medicine-data

drugs <- readxl::read_excel(
  here::here(
    "data",
    "2023",
    "2023-03-14",
    "Medicines_output_european_public_assessment_reports.xlsx"
  ),
  skip = 8
) |> 
  clean_names() |> 
  mutate(
    category = factor(tolower(category)),
    # product_number is based on whether it's human or veterinary, so get rid of
    # the extras.
    product_number = str_extract(product_number, "\\d+$"),
    
    # These two are often only different in capitalization.
    international_non_proprietary_name_inn_common_name = tolower(
      international_non_proprietary_name_inn_common_name
    ),
    active_substance = tolower(active_substance),
    
    # These are really just dates that got meaningless hours attached.
    marketing_authorisation_date = lubridate::date(
      marketing_authorisation_date
    ),
    date_of_refusal_of_marketing_authorisation = lubridate::date(
      date_of_refusal_of_marketing_authorisation
    ),
    date_of_opinion = lubridate::date(date_of_opinion),
    decision_date = lubridate::date(decision_date),
    
    # Coalesce this in place then later rename and drop the extra column.
    human_pharmacotherapeutic_group = coalesce(
      human_pharmacotherapeutic_group, vet_pharmacotherapeutic_group
    ),
    
    atc_code = coalesce(atc_code, at_cvet_code),
    
    # Logicals
    patient_safety = patient_safety == "yes",
    additional_monitoring = additional_monitoring == "yes",
    generic = generic == "yes",
    biosimilar = biosimilar == "yes",
    conditional_approval = conditional_approval == "yes",
    exceptional_circumstances = exceptional_circumstances == "yes",
    accelerated_assessment = accelerated_assessment == "yes",
    orphan_medicine = orphan_medicine == "yes",
    
    # Other data types
    revision_number = as.integer(revision_number),
    authorisation_status = factor(tolower(authorisation_status))
  ) |>
  rename(
    "common_name" = "international_non_proprietary_name_inn_common_name",
    "pharmacotherapeutic_group" = "human_pharmacotherapeutic_group"
  ) |> 
  select(
    -"vet_pharmacotherapeutic_group",
    -"at_cvet_code"
  )

write_csv(
  drugs, 
  file = here::here(
    "data",
    "2023",
    "2023-03-14",
    "drugs.csv"
  )
)
```
