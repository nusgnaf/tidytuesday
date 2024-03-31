# 门户项目

本周的数据来自[门户项目](https://portal.weecology.org/)。这是一个长期生态研究项目，研究亚利桑那州沙漠啮齿动物、植物、蚂蚁和天气的动态。

> 门户项目是在亚利桑那州门户附近进行的一项长期生态研究。自1977年以来，该地点一直被用于研究啮齿动物、蚂蚁和植物之间的相互作用，以及它们对气候的响应。为了研究生物之间的相互作用，他们通过实验性地操纵对24个研究区的访问。该研究已产生了100多篇科学论文，并且是美国历时最长的生态研究之一。

[Weecology研究小组](https://www.weecology.org/)监测啮齿动物、植物、蚂蚁和天气。门户项目的所有数据都以近实时的方式公开提供，以便为科学研究和外展提供最大的效益。核心数据集使用GitHub和持续分析运行自动化的实时数据工作流程进行管理。

该数据集侧重于啮齿动物数据。完整数据可通过以下资源获得：

- [GitHub数据存储库](https://github.com/weecology/PortalData)
- [实时更新的Zenodo存档](https://doi.org/10.5281/zenodo.1215988)
- [数据论文](https://www.biorxiv.org/content/early/2018/05/28/332783)
- [方法文档](https://github.com/weecology/PortalData/blob/master/SiteandMethods/Methods.md)

门户项目数据也可以通过数据检索器(Data Retriever)访问，这是一个用于数据的包管理器。

[数据检索器](https://www.data-retriever.org/)

还维护有一个面向教学的数据集版本，其中去除了一些数据的复杂性，以便于计算训练目的的使用。该数据集是[数据修习生态学](https://datacarpentry.org/ecology-workshop/)材料的核心数据集，并已被下载了近50,000次。

### 在这里获取数据

```{r}
# Get the Data

# Read in with tidytuesdayR package 
# Install from CRAN via: install.packages("tidytuesdayR")
# This loads the readme and all the datasets for the week of interest

# Either ISO-8601 date or year/week works!

tuesdata <- tidytuesdayR::tt_load('2023-05-02')
tuesdata <- tidytuesdayR::tt_load(2023, week = 18)

plots <- tuesdata$plots
species <- tuesdata$species
surveys <- tuesdata$surveys


# Or read in the data manually

plots <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-05-02/plots.csv')
species <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-05-02/species.csv')
surveys <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-05-02/surveys.csv')


```

### 数据字典

# `plots.csv`

| variable  | class     | description    |
|:----------|:----------|:---------------|
| plot      | double    | Plot number    |
| treatment | character | Treatment type |

# `species.csv`

| variable       | class     | description             |
|:---------------|:----------|:------------------------|
| species        | character | Species                 |
| scientificname | character | Scientific Name         |
| taxa           | character | Taxa                    |
| commonname     | character | Common Name             |
| censustarget   | double    | Target species (0 or 1) |
| unidentified   | double    | Unidentified (0 or 1)   |
| rodent         | double    | Rodent (0 or 1)         |
| granivore      | double    | Granivore (0 or 1)      |
| minhfl         | double    | Minimum hindfoot length |
| meanhfl        | double    | Mean hindfoot length    |
| maxhfl         | double    | Maximum hindfoot length |
| minwgt         | double    | Minimum weight          |
| meanwgt        | double    | Mean weight             |
| maxwgt         | double    | Maximum weight          |
| juvwgt         | double    | Juvenile weight         |

# `surveys.csv`

| variable   | class     | description                                                    |
|:-----------------------|:---------------------|:-------------------------|
| censusdate | double    | Census date                                                    |
| month      | double    | Month                                                          |
| day        | double    | Day                                                            |
| year       | double    | Year                                                           |
| treatment  | character | Treatment type                                                 |
| plot       | double    | Plot number                                                    |
| stake      | double    | Stake number                                                   |
| species    | character | Species code                                                   |
| sex        | character | Sex                                                            |
| reprod     | character | Reproductive condition                                         |
| age        | character | Age                                                            |
| testes     | character | Testes (Scrotal, Recent, or Minor)                             |
| vagina     | character | Vagina (Swollen, Plugged, or Both)                             |
| pregnant   | character | Pregnant                                                       |
| nipples    | character | Nipples (Enlarged, Swollen, or Both)                           |
| lactation  | character | Lactating                                                      |
| hfl        | double    | Hindfoot length                                                |
| wgt        | double    | Weight                                                         |
| tag        | character | Primary individual identifier                                  |
| note2      | character | Newly tagged individual for 'tag'                              |
| ltag       | character | Secondary tag information when ear tags were used in both ears |
| note3      | character | Newly tagged individual for 'ltag'                             |

### Cleaning Script

Thanks to @ethanwhite for the data cleaning script. This script
downloads the data using the [{portalr}](https://weecology.github.io/portalr/) package. It filters for the
species and plot data, and years greater than 1977.

``` r
# All packages used in this script:
library(portalr)
library(dplyr)

download_observations(".")
data_tables <- load_rodent_data()

species_data <- data_tables[["species_table"]]
plots_data <- data_tables[["plots_table"]]

plot_treatments <- plots_data %>%
  filter(year > 1977) |>
  mutate(iso_date = as.Date(paste0(year, "-", month, "-", "01")), 
         plot = as.factor(plot)) %>%
  select(iso_date, plot, treatment)

plots_data_longterm <- plot_treatments |>
  group_by(plot) |>
  summarize(treatment = case_when(
              all(treatment == "control") ~ "control",
              all(treatment == "exclosure") ~ "exclosure")) |>
  filter(!is.na(treatment))

species_data <- species_data |>
  filter(censustarget == 1, unidentified == 0)

survey_data <- summarize_individual_rodents(
  time = "date",
  length = "Longterm") |>
  filter(year > 1977) |>
  filter(species %in% unique(species_data$species))

write.csv(survey_data, "surveys.csv", row.names = FALSE, na = "")
write.csv(plots_data_longterm, "plots.csv", row.names = FALSE, na = "")
write.csv(species_data, "species.csv", row.names = FALSE, na = "")
```
