# Diwali销售数据

本周是排灯节，即光明节！本周的数据来自印度排灯节期间一家零售商店的[销售数据](https://www.kaggle.com/datasets/saadharoon27/diwali-sales-dataset)。这些数据由 Saad Haroon 在 Kaggle 上共享。

本周我们分享 Python 数据分析示例！有一些示例可供参考，但这些来自 [Brushan Shelke](https://www.kaggle.com/code/bhushanshelke69/diwali-data-exploration) 或 [Vikas Vachheta](https://github.com/vikasvachheta08/Diwali_Sales_Analysis_Using_Python) 的分析是一些数据探索的例子（查看 Diwali_Sales_Analysis.ipynb 文件获取代码）。

## 数据

```{r}
# 选项1：tidytuesdayR包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-11-14')
## OR
tuesdata <- tidytuesdayR::tt_load(2023, week = 46)

house <- tuesdata$diwali_sales_data

# 选项2：直接从GitHub读取

house <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-11-14/diwali_sales_data.csv')
```

## 如何参与

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们要强调的是，您不应该从数据中得出**因果关系**的结论。影响所有数据的各种调节变量很多，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并自行考虑这些关系背后可能存在的细微差别。
- 使用R或其他编程语言创建可视化、模型、[闪亮的应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `diwali_sales_data.csv`

|variable         |class     |description      |
|:----------------|:---------|:----------------|
|User_ID          |double    |User identification number        |
|Cust_name        |character |Customer name        |
|Product_ID       |character |Product identification number    |
|Gender           |character |Gender of the customer (e.g. Male, Female)           |
|Age Group        |character |Age group of the customer        |
|Age              |double    |Age of the customer             |
|Marital_Status   |double    |Marital status of the customer (e.g. Married, Single)   |
|State            |character |State of the customer           |
|Zone             |character |Geographic zone of the customer             |
|Occupation       |character |Occupation of the customer       |
|Product_Category |character |Category of the product |
|Orders           |double    |Number of orders made by the customer           |
|Amount           |double    |Amount in Indian rupees spent by the customer           |

### Cleaning Script

Data was downloaded from [Kaggle](https://www.kaggle.com/datasets/saadharoon27/diwali-sales-dataset), and the `Status` and `unnamed1` columns removed. 

``` r
library(tidyverse)

diwali_sales_data <- read_csv("DiwaliSalesData.csv")

diwali_sales_data <- diwali_sales_data %>% select(!(c(Status, unnamed1)))

write_csv(diwali_sales_data, "diwali_sales_data.csv")
```

