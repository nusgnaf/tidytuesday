# 龙卷风

本周的数据来自美国国家气象局的国家气象服务暴风中心 [Severe Weather Maps, Graphics, and Data Page](https://www.spc.noaa.gov/wcm/#data)。感谢 [Evan Gower](https://github.com/rfordatascience/tidytuesday/issues/549) 的建议！

Evan [在Kaggle上调查过](https://www.kaggle.com/code/evangower/diving-into-us-tornado-data)这个数据集的一个版本。

### 在这里获取数据

```{r}
# 获取数据

# 使用tidytuesdayR包读取数据
# 通过CRAN安装：install.packages("tidytuesdayR")
# 这将加载本周感兴趣的readme和所有数据集

# ISO-8601日期或年/周都可以使用！

tuesdata <- tidytuesdayR::tt_load('2023-05-16')
tuesdata <- tidytuesdayR::tt_load(2023, week = 20)

tornados <- tornados

# 或者手动读取数据

tornados <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-05-16/tornados.csv')
```

### 数据字典

# `tornados.csv`

|variable     |class     |description  |
|:------------|:---------|:------------|
|om           |integer   |Tornado number. Effectively an ID for this tornado in this year.|
|yr           |integer   |Year, 1950-2022. |
|mo           |integer   |Month, 1-12.|
|dy           |integer   |Day of the month, 1-31. |
|date         |date      |Date. |
|time         |time      |Time. |
|tz           |character |[Canonical tz database timezone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).|
|datetime_utc |datetime  |Date and time normalized to UTC. |
|st           |character |Two-letter postal abbreviation for the state (DC = Washington, DC; PR = Puerto Rico; VI = Virgin Islands). |
|stf          |integer   |State FIPS (Federal Information Processing Standards) number. |
|mag          |integer   |Magnitude on the F scale (EF beginning in 2007). Some of these values are estimated (see fc). |
|inj          |integer   |Number of injuries. When summing for state totals, use sn == 1 (see below). |
|fat          |integer   |Number of fatalities. When summing for state totals, use sn == 1 (see below). |
|loss         |double    |Estimated property loss information in dollars. Prior to 1996, values were grouped into ranges. The reported number for such years is the maximum of its range. |
|slat         |double    |Starting latitude in decimal degrees. |
|slon         |double    |Starting longitude in decimal degrees. |
|elat         |double    |Ending latitude in decimal degrees. |
|elon         |double    |Ending longitude in decimal degrees. |
|len          |double    |Length in miles. |
|wid          |double    |Width in yards. |
|ns           |integer   |Number of states affected by this tornado. 1, 2, or 3. |
|sn           |integer   |State number for this row. 1 means the row contains the entire track information for this state, 0 means there is at least one more entry for this state for this tornado (om + yr). |
|f1           |integer   |FIPS code for the 1st county. |
|f2           |integer   |FIPS code for the 2nd county. |
|f3           |integer   |FIPS code for the 3rd county. |
|f4           |integer   |FIPS code for the 4th county. |
|fc           |logical   |Was the mag column estimated? |

### Cleaning Script

``` r
# All packages used in this script:
library(tidyverse)
library(here)

url <- "https://www.spc.noaa.gov/wcm/data/1950-2022_actual_tornadoes.csv"

# Some of the automatic column types are imperfect Get that spec and then
# update it.
tornados <- read_csv(url)
spec(tornados) # Copy/pasted into col_types below then edited.
tornados <- read_csv(
  url,
  col_types = cols(
    om = col_integer(),
    yr = col_integer(),
    mo = col_integer(),
    dy = col_integer(),
    date = col_date(format = ""),
    time = col_time(format = ""),
    tz = col_integer(),
    st = col_factor(),
    stf = col_integer(),
    stn = col_integer(),
    mag = col_integer(),
    inj = col_integer(),
    fat = col_integer(),
    loss = col_double(),
    closs = col_double(),
    slat = col_double(),
    slon = col_double(),
    elat = col_double(),
    elon = col_double(),
    len = col_double(),
    wid = col_integer(),
    ns = col_integer(),
    sn = col_integer(),
    sg = col_integer(),
    f1 = col_integer(),
    f2 = col_integer(),
    f3 = col_integer(),
    f4 = col_integer(),
    fc = col_integer()
  )
)

glimpse(tornados)

# This table only contains one segment per tornado, so we can drop the sg
# column.
tornados$sg <- NULL

# The tz column is confusing in the provided dictionary
# (https://www.spc.noaa.gov/wcm/data/SPC_severe_database_description.pdf).
# Investigate it to make sense of the various values.
tornados |> 
  count(tz)

# The doc says 3 == CST, and 9 == GMT. 0 appears to be NA. What is 6? 
tornados |> 
  filter(tz == 6) |>
  count(st)

# All tornados with tz == 6 are in Mountain Time states, so we'll make that
# assumption. Update time encoding.

tornados <- tornados |> 
  # We can't really judge even what day the recording was on for unknown tz, so
  # drop those values.
  filter(tz != 0) |> 
  mutate(
    # Make the remaining tz's more meaningful. We'll assume they meant Central
    # (daylight or standard) for "CST", and likewise that they meant what we now
    # call UTC for "GMT". "GMT" sometimes includes BST so we'll avoid using that
    # name.
    tz = case_match(
      tz,
      3 ~ "America/Chicago",
      6 ~ "America/Denver",
      9 ~ "UTC"
    ),
    # Add a datetime_utc column to normalize the times. ymd_hms only wants a
    # single timezone (not a vector of them), so break it up with a case_match.
    datetime_utc = case_match(
      tz,
      "America/Chicago" ~ lubridate::ymd_hms(
        paste(date, time),
        tz = "America/Chicago"
      ),
      "America/Denver" ~ lubridate::ymd_hms(
        paste(date, time),
        tz = "America/Denver"
      ),
      "UTC" ~ lubridate::ymd_hms(
        paste(date, time),
        tz = "UTC"
      ) 
    ) |> 
      lubridate::with_tz("UTC"),
    .after = tz
  ) |> 
  # Drop stn because it was discontinued and was inconsistent before being
  # discontinued. closs (crop loss) has an unexplained discontinuity in 2016 and
  # it isn't entirely clear what changed.
  select(-"stn", -"closs") |> 
  # Recode some more weird columns.
  mutate(
    # The mag column uses -9 for NA.
    mag = na_if(mag, -9),
    # The loss column is confusingly coded. Let's attempt to make it make sense.
    # The documentation (last updated in 2010) explains that the coding changed in
    # 1996. Observationally, it's clear that it changed again in 2016.
    loss = case_when(
      loss == 0 ~ NA,
      yr < 1996 & loss == 1 ~ 50,
      yr < 1996 & loss == 2 ~ 500,
      yr < 1996 & loss == 3 ~ 5000,
      yr < 1996 & loss == 4 ~ 50000,
      yr < 1996 & loss == 5 ~ 500000,
      yr < 1996 & loss == 6 ~ 5000000,
      yr < 1996 & loss == 7 ~ 50000000,
      yr < 1996 & loss == 8 ~ 500000000,
      yr < 1996 & loss == 9 ~ 5000000000,
      yr >= 1996 & yr < 2016 ~ loss * 1e6,
      TRUE ~ loss
    ),
    # The fc column is really a "was mag estimated" column
    fc = as.logical(fc)
  )

# Some of the remaining columns are confusing, but we'll explain them in the
# dictionary and see what people find!

write_csv(
  tornados,
  here(
    "data",
    "2023",
    "2023-05-16",
    "tornados.csv"
  )
)
```
