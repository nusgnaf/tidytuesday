# 泰勒·斯威夫特

由于[《The Eras Tour Film》](https://www.tstheerastourfilm.com/)刚刚发布，本周我们将探索泰勒·斯威夫特的歌曲数据！

你准备好了吗？

W. Jake Thompson的[taylor](https://taylor.wjakethompson.com/) R包是一个由精选数据组成的泰勒·斯威夫特歌曲数据集，包括歌词和音频特征。数据来自Genius和Spotify API。

主要有三个数据集。

> 第一个是taylor_album_songs，其中包括泰勒正式录音室专辑中所有歌曲的歌词和来自Spotify API的音频特征。值得注意的是，这不包括单独发布的单曲（例如，Only the Young，Christmas Tree Farm等），以及非泰勒所有的专辑，其中存在泰勒所有的替代版本（例如，为了支持艺术家拥有自己的歌曲，Fearless被排除在外，取而代之的是Fearless（泰勒的版本））。

> 您可以使用taylor_all_songs访问泰勒的整个唱片目录。这包括taylor_album_songs中的所有歌曲，以及EP、个别单曲和作为泰勒版本重新发行的专辑的原始版本。

> 最后，还有一个小数据集，taylor_albums，总结了泰勒的专辑发行历史。

数据集中来自Spotify的音频特征的信息在他们的[API文档](https://developer.spotify.com/documentation/web-api/reference/get-audio-features)中有包含。

对于您的可视化，{taylor}包提供了自己的一类颜色调色板，受到Josiah Parry在[{cpcinema}](https://github.com/JosiahParry/cpcinema)包中的工作的启发。

您可能还对Alex Stephenson的[tayoRswift包](https://asteves.github.io/tayloRswift/)感兴趣，这是一个基于泰勒·斯威夫特专辑封面的ggplot2调色板。"当您的颜色绝对不应该被从叙事中排除时。"

## 数据

```{r}
# 选项1：tidytuesdayR 包
## install.packages("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2023-10-17')
## 或者
tuesdata <- tidytuesdayR::tt_load(2023, week = 42)

taylor_album_songs <- tuesdata$taylor_album_songs
taylor_all_songs <- tuesdata$taylor_all_songs
taylor_albums <- tuesdata$taylor_albums

# 选项2：直接从 GitHub 读取

taylor_album_songs <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-10-17/taylor_album_songs.csv')
taylor_all_songs <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-10-17/taylor_all_songs.csv')
taylor_albums <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-10-17/taylor_albums.csv')

```

## 参与方式

- [探索数据](https://r4ds.hadley.nz/)，留意有趣的关系。我们想强调的是，您不应在数据中得出关于**因果关系**的结论。有许多影响所有数据的调节变量，其中许多可能没有在这些数据集中捕获到。因此，我们建议您使用提供的数据来练习数据整理和绘图技术，并考虑自己可能潜在的这些关系背后的细微差别。
- 使用R或其他编程语言创建可视化、模型、[闪亮应用](https://shiny.posit.co/)或其他与数据科学相关的输出。
- 使用 #TidyTuesday 标签在社交媒体上[分享您的输出和生成它的代码](../../../sharing.md)。

### 数据字典

# `taylor_album_songs.csv`

|variable            |class     |description         |
|:-------------------|:---------|:-------------------|
|album_name          |character |Album name         |
|ep                  |logical   |Is it an EP                  |
|album_release       |double    |Album release date       |
|track_number        |integer   |Track number        |
|track_name          |character |Track name          |
|artist              |character |Artists             |
|featuring           |character |Artists featured           |
|bonus_track         |logical   |Is it a bonus track         |
|promotional_release |double    |Date of promotional release |
|single_release      |double    |Date of single release      |
|track_release       |double    |Date of track release       |
|danceability        |double    |Spotify danceability score. A value of 0.0 is least danceable and 1.0 is most danceable.        |
|energy              |double    |Spotify energy score. Energy is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity.        |
|key                 |integer   |The key the track is in.                 |
|loudness            |double    |Spotify loudness score. The overall loudness of a track in decibels (dB). Loudness values are averaged across the entire track.        |
|mode                |integer   |Mode indicates the modality (major or minor) of a track, the type of scale from which its melodic content is derived. Major is represented by 1 and minor is 0.               |
|speechiness         |double    |Spotify speechiness score. Speechiness detects the presence of spoken words in a track. The more exclusively speech-like the recording (e.g. talk show, audio book, poetry), the closer to 1.0 the attribute value.          |
|acousticness        |double    |Spotify acousticness score. A confidence measure from 0.0 to 1.0 of whether the track is acoustic. 1.0 represents high confidence the track is acoustic.        |
|instrumentalness    |double    |Spotify instrumentalness score. Predicts whether a track contains no vocals. The closer the instrumentalness value is to 1.0, the greater likelihood the track contains no vocal content. Values above 0.5 are intended to represent instrumental tracks, but confidence is higher as the value approaches 1.0.   |
|liveness            |double    |Spotify liveness score. Detects the presence of an audience in the recording. Higher liveness values represent an increased probability that the track was performed live. A value above 0.8 provides strong likelihood that the track is live.            |
|valence             |double    |Spotify valence score. A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track. Tracks with high valence sound more positive (e.g. happy, cheerful, euphoric), while tracks with low valence sound more negative (e.g. sad, depressed, angry).             |
|tempo               |double    |The overall estimated tempo of a track in beats per minute (BPM). In musical terminology, tempo is the speed or pace of a given piece and derives directly from the average beat duration.          |
|time_signature      |integer   |An estimated time signature. The time signature (meter) is a notational convention to specify how many beats are in each bar (or measure). The time signature ranges from 3 to 7 indicating time signatures of "3/4", to "7/4".     |
|duration_ms         |integer   |The duration of the track in milliseconds.       |
|explicit            |logical   |Does the track have explicit lyrics.           |
|key_name            |character |The key the track is in. Integers map to pitches using standard Pitch Class notation. E.g. 0 = C, 1 = C♯/D♭, 2 = D, and so on. If no key was detected, the value is -1.         |
|mode_name           |character |Modality of the track.           |
|key_mode            |character |The key of the track.       |
|lyrics              |list      |Track lyrics. These values are all NA. To get the lyrics in nested tibbles, `install.packages("taylor")` and use the source data.|

# `taylor_all_songs.csv`

|variable            |class     |description         |
|:-------------------|:---------|:-------------------|
|album_name          |character |Album name         |
|ep                  |logical   |Is it an EP                  |
|album_release       |double    |Album release date       |
|track_number        |integer   |Track number        |
|track_name          |character |Track name          |
|artist              |character |Artists             |
|featuring           |character |Artists featured           |
|bonus_track         |logical   |Is it a bonus track         |
|promotional_release |double    |Date of promotional release |
|single_release      |double    |Date of single release      |
|track_release       |double    |Date of track release       |
|danceability        |double    |Spotify danceability score. A value of 0.0 is least danceable and 1.0 is most danceable.        |
|energy              |double    |Spotify energy score. Energy is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity.        |
|key                 |integer   |The key the track is in.                 |
|loudness            |double    |Spotify loudness score. The overall loudness of a track in decibels (dB). Loudness values are averaged across the entire track.        |
|mode                |integer   |Mode indicates the modality (major or minor) of a track, the type of scale from which its melodic content is derived. Major is represented by 1 and minor is 0.               |
|speechiness         |double    |Spotify speechiness score. Speechiness detects the presence of spoken words in a track. The more exclusively speech-like the recording (e.g. talk show, audio book, poetry), the closer to 1.0 the attribute value.          |
|acousticness        |double    |Spotify acousticness score. A confidence measure from 0.0 to 1.0 of whether the track is acoustic. 1.0 represents high confidence the track is acoustic.        |
|instrumentalness    |double    |Spotify instrumentalness score. Predicts whether a track contains no vocals. The closer the instrumentalness value is to 1.0, the greater likelihood the track contains no vocal content. Values above 0.5 are intended to represent instrumental tracks, but confidence is higher as the value approaches 1.0.   |
|liveness            |double    |Spotify liveness score. Detects the presence of an audience in the recording. Higher liveness values represent an increased probability that the track was performed live. A value above 0.8 provides strong likelihood that the track is live.            |
|valence             |double    |Spotify valence score. A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track. Tracks with high valence sound more positive (e.g. happy, cheerful, euphoric), while tracks with low valence sound more negative (e.g. sad, depressed, angry).             |
|tempo               |double    |The overall estimated tempo of a track in beats per minute (BPM). In musical terminology, tempo is the speed or pace of a given piece and derives directly from the average beat duration.          |
|time_signature      |integer   |An estimated time signature. The time signature (meter) is a notational convention to specify how many beats are in each bar (or measure). The time signature ranges from 3 to 7 indicating time signatures of "3/4", to "7/4".     |
|duration_ms         |integer   |The duration of the track in milliseconds.       |
|explicit            |logical   |Does the track have explicit lyrics.           |
|key_name            |character |The key the track is in. Integers map to pitches using standard Pitch Class notation. E.g. 0 = C, 1 = C♯/D♭, 2 = D, and so on. If no key was detected, the value is -1.         |
|mode_name           |character |Modality of the track.           |
|key_mode            |character |The key of the track.       |
|lyrics              |list      |Track lyrics. These values are all NA. To get the lyrics in nested tibbles, `install.packages("taylor")` and use the source data.|

# `taylor_albums.csv`

|variable         |class     |description      |
|:----------------|:---------|:----------------|
|album_name       |character |Album name      |
|ep               |logical   |Is it an EP       |
|album_release    |double    |Album release date    |
|metacritic_score |integer   |Metacritic score |
|user_score       |double    |User score       |

### Cleaning Script

``` r
library(tidyverse)
library(taylor)

write_csv(taylor_album_songs, "taylor_album_songs.csv")
write_csv(taylor_all_songs, "taylor_all_songs.csv")
write_csv(taylor_albums, "taylor_albums.csv")


```
