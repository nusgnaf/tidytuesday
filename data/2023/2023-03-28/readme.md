# 时区

本周的数据来自[IANA tz数据库](https://data.iana.org/time-zones/tz-link.html)，通过 {[clock](https://clock.r-lib.org/)} 和 {[tzdb](https://tzdb.r-lib.org/)} 包。特别感谢 [Davis Vaughan](https://twitter.com/dvaughan32/status/1639281179433074692) 协助准备这些数据！

许多网站使用IANA tz数据库中的数据。[timeanddate.com](https://www.timeanddate.com/) 的 ["什么是夏令时"](https://www.timeanddate.com/time/dst/) 是查找有关时区的有趣信息的好地方，比如 [澳大利亚的豪礁岛](https://www.timeanddate.com/time/time-zones-interesting.html#:~:text=Lord%20Howe%20Island%3A%20UTC%20%2B10%3A30%20/%20%2B11%3A00) 的奇特情况。

### 在此处获取数据

```{r}
# 获取数据

# 使用 tidytuesdayR 包读取
# 通过 CRAN 安装：install.packages("tidytuesdayR")
# 这将加载感兴趣周的自述文件和所有数据集

# 可以使用 ISO-8601 日期或年/周

tuesdata <- tidytuesdayR::tt_load('2023-03-28')
tuesdata <- tidytuesdayR::tt_load(2023, week = 13)

transitions <- tuesdata$transitions
timezones <- tuesdata$timezones
timezone_countries <- tuesdata$timezone_countries
countries <- tuesdata$countries

# 或者手动读取数据

transitions <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-03-28/transitions.csv')
timezones <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-03-28/timezones.csv')
timezone_countries <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-03-28/timezone_countries.csv')
countries <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-03-28/countries.csv')
```

### 数据字典

# `transitions.csv`

Changes in the conversion of a given time zone to UTC (for example for daylight savings or because the definition of the time zone changed).

|variable     |class     |description  |
|:------------|:---------|:------------|
|zone         |character |The name of the time zone.|
|begin        |character |When this definition went into effect, in UTC. Tip: convert to a datetime using lubridate::as_datetime().|
|end          |character |When this definition ended (and the next definition went into effect), in UTC. Tip: convert to a datetime using lubridate::as_datetime().|
|offset       |double    |The offset of this time zone from UTC, in seconds.|
|dst          |logical   |Whether daylight savings time is active within this definition.|
|abbreviation |character |The time zone abbreviation in use throughout this begin to end range.|

# `timezones.csv`

Descriptions of time zones from the [IANA time zone database](https://data.iana.org/time-zones/tz-link.html).

|variable  |class     |description |
|:---------|:---------|:-----------|
|zone      |character |The name of the time zone.|
|latitude  |double    |Latitude of the time zone's "principal location."|
|longitude |double    |Longitude of the time zone's "principal location."|
|comments  |character |Comments from the tzdb definition file.|

# `timezone_countries.csv`

Countries (or other place names) that overlap with each time zone.

|variable     |class     |description  |
|:------------|:---------|:------------|
|zone         |character |The name of the time zone.|
|country_code |character |The [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) 2-character country code.|

# `countries.csv`

Names of countries and other places.

|variable     |class     |description  |
|:------------|:---------|:------------|
|country_code |character |The [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) 2-character country code.|
|place_name   |character |The usual English name for the coded region, chosen so that alphabetic sorting of subsets produces helpful lists. This is not the same as the English name in the ISO 3166 tables.|

### Cleaning Script

```r
# All packages used in this script:
library(tidyverse)
library(clock)
library(vctrs)
library(tzdb)
library(fs)
library(here)

# Code for extracting transitions provided by David Vaughan at
# https://gist.github.com/DavisVaughan/95a3bdb6b11e8e8f00ac671384461c72
# Slightly adapted for use in TidyTuesday.

generate_transitions <- function(zone,
                                 from = date_build(1900, 1, 1),
                                 to = date_build(2030, 1, 1)) {
  days <- date_seq(
    from = from,
    to = to,
    by = 1
  )
  
  days <- as_sys_time(days)
  info <- sys_time_info(days, zone = zone)
  
  # Must choose a unified transition time zone to use for display
  info$begin <- as_date_time(info$begin, zone = "UTC")
  info$end <- as_date_time(info$end, zone = "UTC")
  info$offset <- as.double(info$offset)
  
  info <- vec_unique(info)
  
  info
}

zones <- tzdb_names()
zones <- vec_set_names(zones, zones)

transitions <- map(zones, generate_transitions)
transitions <- list_rbind(transitions, names_to = "zone")
transitions <- as_tibble(transitions)

# All transitions (as long as more than one didn't happen within the same day)
transitions


# Add some additional information from the tzdb package.

tzdata <- tzdb::tzdb_path("text")

# zone1970 lists time zones that have been agreed upon since at least 1970. Many
# lines don't have comments, so expect warnings.
zone1970 <- read_tsv(
  fs::path(tzdb::tzdb_path("text"), "zone1970.tab"),
  skip = 34,
  col_names = c(
    "country_code",
    "coordinates",
    "zone",
    "comments"
  ),
  col_types = "cccc"
) |>
  # There's a comment in the middle of the table.
  filter(!str_starts(country_code, "#")) |> 
  # Break coordinates into lat/long columns. They're in "ISO 6709
  # sign-degrees-minutes-seconds format, either ±DDMM±DDDMM or ±DDMMSS±DDDMMSS,
  # first latitude (+ is north), then longitude (+ is east)." This is a good
  # opportunity to practice the new tidyr::separate_wider_regex function!
  # There's probably a function in a spatial package somewhere to deal with
  # this, but I'll work it out by hand.
  separate_wider_regex(
    coordinates,
    patterns = c(
      latitude = "[+-]\\d+",
      longitude = "[+-]\\d+"
    )
  ) |> 
  separate_wider_regex(
    latitude,
    patterns = c(
      lat_sign = "[+-]",
      lat_deg = "\\d{2}",
      lat_min = "\\d{2}",
      lat_sec = "\\d*"
    ),
    cols_remove = FALSE
  ) |> 
  mutate(
    lat_sec = as.integer(lat_sec),
    lat_sec = replace_na(lat_sec, 0),
    latitude = as.integer(paste0(lat_sign, 1)) * (
      as.integer(lat_deg) + as.integer(lat_min)/60 + lat_sec/60
    )
  ) |> 
  separate_wider_regex(
    longitude,
    patterns = c(
      long_sign = "[+-]",
      long_deg = "\\d{3}",
      long_min = "\\d{2}",
      long_sec = "\\d*"
    ),
    cols_remove = FALSE
  ) |> 
  mutate(
    long_sec = as.integer(long_sec),
    long_sec = replace_na(long_sec, 0),
    longitude = as.integer(paste0(long_sign, 1)) * (
      as.integer(long_deg) + as.integer(long_min)/60 + long_sec/60
    )
  ) |> 
  select(
    -starts_with("lat_"),
    -starts_with("long_")
  )

# Let's normalize that dataset a little by splitting it.
timezones <- zone1970 |> 
  select(zone, latitude, longitude, comments) |> 
  arrange(zone)
timezone_countries <- zone1970 |> 
  select(zone, country_code) |> 
  tidyr::separate_longer_delim(country_code, ",") |> 
  arrange(zone)

countries <- read_tsv(
  fs::path(tzdb::tzdb_path("text"), "iso3166.tab"),
  skip = 34,
  col_names = c(
    "country_code",
    "place_name"
  ),
  col_types = "cc"
)

colnames(transitions)
colnames(timezones)
colnames(timezone_countries)
colnames(countries)

write_csv(
  transitions,
  here(
    "data",
    "2023",
    "2023-03-28",
    "transitions.csv"
  )
)
write_csv(
  timezones,
  here(
    "data",
    "2023",
    "2023-03-28",
    "timezones.csv"
  )
)
write_csv(
  timezone_countries,
  here(
    "data",
    "2023",
    "2023-03-28",
    "timezone_countries.csv"
  )
)
write_csv(
  countries,
  here(
    "data",
    "2023",
    "2023-03-28",
    "countries.csv"
  )
)
```
