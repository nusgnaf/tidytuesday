# 艺术史

本周的数据来自[arthistory数据包](https://saralemus7.github.io/arthistory/)

> 此数据集包含了 Holland Stam 的论文工作使用的数据，题为[Quantifying art historical narratives](https://research.repository.duke.edu/concern/datasets/q811kk70n?locale=en)。数据收集的目的是评估通过 Janson's History of Art 和 Gardner's Art Through the Ages 两本最受欢迎的艺术史教科书在美国教育系统中使用的艺术家的人口统计代表性。在这个包中，收集了艺术家级别和作品级别的数据，以及有关艺术家人口统计信息的变量和用于描述他们或他们的作品在每个教科书版本中占据了多少空间的数值指标。

> 此数据包包含三个数据集：

> * worksjanson：包含了从 1963 年到 2011 年的 Gardner's 艺术史教科书版本的单个作品级别数据。对于每个作品，都有关于作品大小和文本在教科书中的显示方式的信息，以及关于作品的媒介和创作年份的细节。还包括有关艺术家的人口统计数据。

> * worksgardner：包含了从 1926 年到 2020 年的 Gardner's 艺术史教科书版本的单个作品级别数据。对于每个作品，都有关于作品在教科书中显示方式的大小以及相应描述文本的大小的信息。还包括有关艺术家的人口统计数据。

> * artists：包含了从 1926 年到 2020 年的 Gardner 或 Janson's 艺术史教科书版本的艺术家的各种信息。数据包括人口统计信息、在教科书中占据的空间，以及在 MoMA 和 Whitney 博物馆中的存在。

致谢 arthistory

> 引用

> Lemus S, Stam H (2022). arthistory: Art History Textbook Data. https://github.com/saralemus7/arthistory, https://saralemus7.github.io/arthistory/.

[Holland Stam's thesis](https://github.com/hollandstam1/thesis) 中包含了分析示例，以 Quarto 文件形式呈现。


### 在此处获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# ISO-8601日期或年/周均可使用！

tuesdata <- tidytuesdayR::tt_load('2023-01-17')
tuesdata <- tidytuesdayR::tt_load(2023, week = 03)

arthistory <- tuesdata$arthistory

# 或者手动读取数据

artists <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-01-17/artists.csv')

```
### 数据字典

# `artists.csv`

|variable                   |class     |description                |
|:--------------------------|:---------|:--------------------------|
|artist_name                |character |The name of each artist                |
|edition_number             |double    |The edition number of the textbook from either Janson's History or Art or Gardner's Art Through the Ages.             |
|year                       |double    |The year of publication for a given edition of Janson or Gardner.                   |
|artist_nationality         |character |The nationality of a given artist.        |
|artist_nationality_other   |character |The nationality of the artist. Of the total count of artists through all editions of Janson's History of Art and Gardner's Art Through the Ages, 77.32% account for French, Spanish, British, American and German. Therefore, the categorical strings of this variable are French, Spanish, British, American, German and Other   |
|artist_gender              |character |The gender of the artist              |
|artist_race                |character |The race of the artist                |
|artist_ethnicity           |character |The ethnicity of the artist           |
|book                       |character |Which book, either Janson or Gardner the particular artist at that particular time was included.                    |
|space_ratio_per_page_total |double    |The area in centimeters squared of both the text and the figure of a particular artist in a given edition of Janson's History of Art divided by the area in centimeters squared of a single page of the respective edition. This variable is continuous. |
|artist_unique_id           |double    |	The unique identifying number assigned to artists across books is denoted in alphabetical order. This variable is discrete.        |
|moma_count_to_year         |double    |The total count of exhibitions ever held by the Museum of Modern Art (MoMA) of a particular artist at a given year of publication. This variable is discrete.         |
|whitney_count_to_year      |double    |The count of exhibitions held by The Whitney of a particular artist at a particular moment of time, as highlighted by year. This variable in discrete.   |
|artist_race_nwi            |character |The non-white indicator for artist race, meaning if an artist's race is denoted as either white or non-white.          |




### Cleaning Script

No data cleaning
