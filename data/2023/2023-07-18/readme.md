# GPT检测器

本周的数据来自Simon Couch的[detectors R包](https://github.com/simonpcouch/detectors/)，其中包含了各种GPT检测器的预测结果。

该数据基于以下预印本：

GPT Detectors Are Biased Against Non-Native English Writers. Weixin Liang, Mert Yuksekgonul, Yining Mao, Eric Wu, James Zou. arXiv: [2304.02819](https://arxiv.org/abs/2304.02819)

> 研究作者进行了一系列实验，将许多论文提交给不同的GPT检测模型。通过对比检测器对母语和非母语英语写手撰写的论文的预测，作者认为GPT检测器不成比例地将非母语英语写手的真实写作归类为人工智能生成的。

### 在这里获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取数据
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载本周感兴趣的readme和所有数据集

# ISO-8601日期或年/周都可以使用！

tuesdata <- tidytuesdayR::tt_load('2023-07-18')
tuesdata <- tidytuesdayR::tt_load(2023, week = 29)

detectors <- tuesdata$detectors

# 或者手动读取数据

detectors <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-07-18/detectors.csv')

```

### 数据字典

# `detectors.csv`

|variable    |class     |description |
|:-----------|:---------|:-----------|
|kind        |character |Whether the essay was written by a "Human" or "AI".      |
|.pred_AI    |double    |The class probability from the GPT detector that the inputted text was written by AI. |
|.pred_class |character |The uncalibrated class prediction, encoded as `if_else(.pred_AI > .5, "AI", "Human")` |
|detector    |character |The name of the detector used to generate the predictions. |
|native      |character |For essays written by humans, whether the essay was written by a native English writer or not. These categorizations are coarse; values of `"Yes"` may actually be written by people who do not write with English natively. `NA` indicates that the text was not written by a human.   |
|name        |character |A label for the experiment that the predictions were generated from. |
|model       |character |For essays that were written by AI, the name of the model that generated the essay.  |
|document_id |double    |A unique identifier for the supplied essay. Some essays were supplied to multiple detectors. Note that some essays are AI-revised derivatives of others. |
|prompt      |character |For essays that were written by AI, a descriptor for the form of "prompt engineering" passed to the model. |

### Cleaning Script

csv file was generated from the detectors tibble in the 'detectors' R package. 
