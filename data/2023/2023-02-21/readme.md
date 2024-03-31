# 鲍勃·罗斯的绘画作品

本周的数据来自Jared Wilber的[Bob Ross Paintings](https://github.com/jwilber/Bob_Ross_Paintings/blob/master/data/bob_ross_paintings.csv)，通过 @frankiethull的[Bob Ross Colors 数据包](https://github.com/frankiethull/BobRossColors)。

> 这是来自电视节目《喜悦的绘画》中的[鲍勃·罗斯的绘画作品](https://www.twoinchbrush.com/all-paintings)的数据。

@frankiethull 创建了一个名为 [{BobRossColors}](https://github.com/frankiethull/BobRossColors) 的 R 数据包，其中包含有关调色板的信息，利用 imgpalr 从每幅绘画图像中提取出差异和定性颜色。此外，数据包中还包含了独特的鲍勃·罗斯命名的颜色。

在数据集的 GitHub 存储库中，还有[绘画作品的 png 图像](https://github.com/jwilber/Bob_Ross_Paintings/tree/master/data/paintings)！

您可能还想查看我们之前的[2019-08-06的鲍勃·罗斯数据集](https://tidytues.day/2019/2019-08-06)，看看命名对象和命名颜色之间是否存在相关性！

### 在此处获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# 可以使用 ISO-8601 日期或年/周

tuesdata <- tidytuesdayR::tt_load('2023-02-21')
tuesdata <- tidytuesdayR::tt_load(2023, week = 8)

bob_ross <- tuesdata$bob_ross

# 或者手动读取数据

bob_ross <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-21/bob_ross.csv')
```

### 数据字典

# `bob_ross.csv`

|variable         |class     |description      |
|:----------------|:---------|:----------------|
|painting_index   |double    |	Painting number as enumerated in collection.   |
|img_src          |character |	Url path to image.      |
|painting_title   |character |	Title of the painting.  |
|season           |double    |  Season of 'The Joy of Painting' in which the painting was featured.           |
|episode          |double    |  Episode of 'The Joy of Painting' in which the painting was featured.	        |
|num_colors       |double    |  Number of unique colors used in the painting.       |
|youtube_src      |character |	Youtube video of episode featuring the painting.      |
|colors           |character |	List of colors used in the painting.          |
|color_hex        |character |	List of colors (hexadecimal code) used in the painting.     |
|Black_Gesso      |logical |Black_Gesso used |
|Bright_Red       |logical |Bright_Red used |
|Burnt_Umber      |logical |Burnt_Umber used |
|Cadmium_Yellow   |logical |Cadmium_Yellow used |
|Dark_Sienna      |logical |Dark_Sienna used |
|Indian_Red       |logical |Indian_Red used |
|Indian_Yellow    |logical |Indian_Yellow used |
|Liquid_Black     |logical |Liquid_Black used |
|Liquid_Clear     |logical |Liquid_Clear used |
|Midnight_Black   |logical |Midnight_Black used |
|Phthalo_Blue     |logical |Phthalo_Blue used |
|Phthalo_Green    |logical |Phthalo_Green used |
|Prussian_Blue    |logical |Prussian_Blue used |
|Sap_Green        |logical |Sap_Green used |
|Titanium_White   |logical |Titanium_White used |
|Van_Dyke_Brown   |logical |Van_Dyke_Brown used |
|Yellow_Ochre     |logical |Yellow_Ochre used |
|Alizarin_Crimson |logical |Alizarin_Crimson used |

### Cleaning Script

```r
library(tidyverse)

# Read in the data
bob_ross <- read_csv(
  "https://raw.githubusercontent.com/jwilber/Bob_Ross_Paintings/master/data/bob_ross_paintings.csv",
) 

glimpse(bob_ross)

# The first column doesn't contain data that we need, so we can remove it

bob_ross <- select(bob_ross, -1)

# Several columns refer to presence/absence of named colors.

bob_ross <- bob_ross |> 
  mutate(
    across(Black_Gesso:Alizarin_Crimson, as.logical)
  )

# Save the data.
write_csv(
  bob_ross,
  here::here(
    "data", "2023", "2023-02-21",
    "bob_ross.csv"
  )
)
```
