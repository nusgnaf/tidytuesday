# 坏血病

本周的数据来自[medicaldata R 包](https://higgi13425.github.io/medicaldata/)。这是Peter Higgins提供的一个数据包，包含19个医学数据集，用于使用R进行可重现医学研究的教学。

我们使用的是[scurvy 数据集](https://htmlpreview.github.io/?https://github.com/higgi13425/medicaldata/blob/master/man/description_docs/scurvy_desc.html)。

> 来源：这个数据集来自于 James Lind 在 1757 年发表的研究，收录在《A Treatise on the Scurvy in Three Parts》一书中。

> 这个数据集包含了12位患坏血病的参与者。在1757年，人们还不知道坏血病是维生素C缺乏的表现。各种疗法曾被零星报道，但林德是第一个通过随机对照试验测试不同的酸性物质（包括柑橘类水果）对抗坏血病的人。对12名患有症状性坏血病的水手进行了六种不同疗法的测试，这些水手被选中因患病程度相似。治疗持续六天，并在六天结束时报告了终点。这些终点包括牙龈腐烂、皮肤溃疡、膝盖无力和倦怠，以严重程度描述。这些已被转化为从0（无）到3（严重）的Likert量表。还报告了一个二分终点，即适合工作状态。

> 坏血病是长途航行中水手的常见疾病，导致口腔溃疡、皮肤病变、膝关节无力和倦怠。在长途航行中，坏血病可能是致命的。詹姆斯·林德于1757年报告了他对12名患有坏血病的水手进行的治疗，收录在《坏血病论文三部曲》中。这本476页的废话可以在谷歌图书网站上找到《坏血病论文三部曲》。其中149-153页在可慷慨地描述为400多页无证据胡言乱语中是一颗珍珠，这4页可能代表了对受控临床试验的第一次报告。

> 林德是HMS Salisbury号的船医，手头有一些受到坏血病影响的水手。有许多疗法被描述并提倡，但只有零星的证据。 1747年5月20日，林德决定在Salisbury号上对12名受影响的水手进行6种疗法的比较研究。他选择了12名患病程度相似的水手，患有明显的皮肤和口腔溃疡、膝关节无力和明显的倦怠，使他们无法胜任工作。他们每个人都接受了标准的船上饮食，包括粥和羊肉汤，偶尔补充饼干和布丁。每种治疗方法都是一种饮食补充剂（包括柑橘类水果）或一种药物。

> 这个数据框架是根据林德在这4页中记录的情况重建的，他估计的严重程度被转化为他在第6天选择的每种症状的4点Likert量表（0-3）。还添加了一个略显奇特的study_id变量，以及对每种治疗方法的详细描述。值得注意的是，关于这是否真的是第一个临床试验，或者它是否真的发生了，存在一些争议，因为没有当时的证据互相印证。请参阅有关历史辩论的链接。

> 林德报告说，每天用2个柠檬和一个橙治疗的水手效果最好，其次是用苹果酒治疗的水手。那些只用硫酸酯治疗的水手只有口腔溃疡有所改善。人们可以想象，这些酸性物质（如稀硫酸、醋、苹果酒和柑橘类水果）可能对这些口腔溃疡相当疼痛。不幸的是，这4页宝贵数据被埋在476页的噪音中，出版延迟了10年，而林德的半心半意的结论（他专注于酸度），意味着直到1795年英国海军才规定水手每天都要吃柠檬。

## 数据

```{r}
# 选项 1: tidytuesdayR 包 
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-07-25')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 30)

scurvy <- tuesdata$scurvy

# 选项 2: 直接从 GitHub 读取

scurvy <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-07-25/scurvy.csv')
```

### 数据字典

# `scurvy.csv`

|variable                  |class     |description               |
|:-------------------------|:---------|:-------------------------|
|study_id                  |double    |Participant ID                 |
|treatment                 |character |Treatment; cider, dilute_sulfuric_acid, vinegar, sea_water, citrus, purgative_mixture                |
|dosing_regimen_for_scurvy |character |Dosing Regimen; 1 quart per day; 25 drops of elixir of vitriol, three times a day; two spoonfuls, three times daily; half pint daily; two lemons and an orange daily; a nutmeg-sized paste of garlic, mustard seed, horseradish, balsam of Peru, and gum myrrh three times a day |
|gum_rot_d6                |character |Gum Rot on Day 6; 0_none, 1_mild, 2_moderate, 3_severe             |
|skin_sores_d6             |character |Skin Sores on Day 6; 0_none, 1_mild, 2_moderate, 3_severe            |
|weakness_of_the_knees_d6  |character |Weakness of the Knees on Day 6; 0_none, 1_mild, 2_moderate, 3_severe  |
|lassitude_d6              |character |Lassitude on Day 6; 0_none, 1_mild, 2_moderate, 3_severe            |
|fit_for_duty_d6           |character |Fit for Duty on Day 6; 0_no, 1_yes          |


### Cleaning Script

The first column was removed from the scurvy.csv file available at https://github.com/higgi13425/medicaldata/tree/master/data-raw.

## How to Participate

- [Explore the data](https://r4ds.hadley.nz/), watching out for interesting relationships. We would like to emphasize that you should not draw conclusions about **causation** in the data. There are various moderating variables that affect all data, many of which might not have been captured in these datasets. As such, our suggestion is to use the data provided to practice your data tidying and plotting techniques, and to consider for yourself what nuances might underlie these relationships.
- Create a visualization, a model, a [shiny app](https://shiny.posit.co/), or some other piece of data-science-related output, using R or another programming language.
- [Share your output and the code used to generate it](../../../sharing.md) on social media with the #TidyTuesday hashtag.
