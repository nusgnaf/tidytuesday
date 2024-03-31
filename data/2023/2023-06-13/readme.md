# SAFI调查数据

本周的数据来自[SAFI（研究非洲农民领导的灌溉）调查](https://datacarpentry.org/socialsci-workshop/data/)，这是[Data Carpentry社会科学研讨会](https://datacarpentry.org/socialsci-workshop/)中使用的数据的子集。因此，如果您想学习如何使用这些数据，课程已经提供了！数据可通过[Figshare](https://figshare.com/articles/dataset/SAFI_Survey_Results/6262019)获得。

引用：Woodhouse, Philip; Veldwisch, Gert Jan; Brockington, Daniel; Komakech, Hans C.; Manjichi, Angela; Venot, Jean-Philippe (2018)：SAFI调查结果。doi:10.6084/m9.figshare.6262019.v1

> SAFI（研究非洲农民领导的灌溉）是一个正在进行的项目，旨在研究农业和灌溉方法。这是有关坦桑尼亚和莫桑比克的家庭和农业的调查数据。调查数据是通过在2016年11月至2017年6月之间进行的访谈收集的，使用了下载到Android智能手机的表格。调查表格是使用ODK（Open Data Kit）软件通过Excel电子表格创建的。然后将收集的数据发送回中央服务器。服务器可用于以JSON和CSV格式下载收集的数据。这是我们将使用的收集数据的教学版本。这不是完整的数据集。

> 调查涵盖了家庭特征（例如使用的建筑材料，家庭成员数量），农业实践（例如用水情况），资产（例如家畜数量和类型）以及家庭成员的详细信息。

> 在这些课程中使用的基本教学数据集是JSON数据集的子集，已转换为CSV格式。

### 在这里获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取数据
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载本周感兴趣的readme和所有数据集

# ISO-8601日期或年/周都可以使用！

tuesdata <- tidytuesdayR::tt_load('2023-06-13')
tuesdata <- tidytuesdayR::tt_load(2023, week = 24)

safi_data.csv <- tuesdata$`safi_data`

# 或者手动读取数据

safi_data <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-06-13/safi_data.csv')
```

### 数据字典

# `safi_data.csv`

|variable             |class     |description          |
|:--------------------|:---------|:--------------------|
|key_ID               |integer   | Added to provide a unique Id for each observation. (The InstanceID field does this as well but it is not as convenient to use)  |
|village              |character | Village name             |
|interview_date       |character | Date of interview    |
|no_membrs            |integer   | Number of members in the household |
|years_liv            |integer   | Number of years living in this village or a neighboring village    |
|respondent_wall_type |character | Type of walls the house has |
|rooms                |integer   | Number of rooms in the main house used for sleeping          |
|memb_assoc           |character | Are you a member of an irrigation association? |
|affect_conflicts     |character | Have you been affected by conflicts with other irrigators in the area? |
|liv_count            |integer   | Livestock count          |
|items_owned          |character | Items owned by the household        |
|no_meals             |integer   | How many meals do people in your household normally eat in a day?            |
|months_lack_food     |character | 	Indicate which months, In the last 12 months have you faced a situation when you did not have enough food to feed the household?   |
|instanceID           |character | 	Unique identifier for the form data submission |


### Cleaning Script

Data was cleaned for the Data Carpentry Social Science lessons. Information available on their [SAFI Teaching Dataset page](https://datacarpentry.org/socialsci-workshop/data/). 
