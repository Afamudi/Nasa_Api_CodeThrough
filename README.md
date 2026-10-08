# Nasa_Api_CodeThrough
CodeThrough 



## Introduction

I had come across the NASA API while completing one of the discussions on Yellowdig, I went ahead and played with them and found them very fun to use.

The `nasapower` package allows R users to get weather and climate data from NASA's POWER API. In this CodeThrough, I will show how to download daily temperature data for Phoenix, Arizona, look at the data, find the average temperature, and create a simple graph.

If you do not already have the `nasapower` package installed, run this command one time in the R console:

```{r install, eval=FALSE}
install.packages("nasapower")
```

## Step 1: Load the Packages

First, we need to load the packages that we will use.

```{r setup, message=FALSE, warning=FALSE}
library(nasapower)
library(ggplot2)
library(dplyr)
```

The `nasapower` package connects R to NASA POWER data. `ggplot2` will be used to make our graph, and `dplyr` will help us work with the data.

## Step 2: Get Data from NASA

We will request daily temperature data for Phoenix, Arizona.

```{r get-data}
phoenix_weather <- get_power(
  community = "ag",
  lonlat = c(-112.0740, 33.4484),
  pars = "T2M",
  dates = c("2025-01-01", "2025-12-31"),
  temporal_api = "daily"
)
```

The `get_power()` function sends a request to NASA's POWER API.

Here is what the main arguments mean:

- `community = "ag"` requests data from the agriculture community.
- `lonlat` gives the longitude and latitude for Phoenix.
- `pars = "T2M"` asks for average air temperature 2 meters above the ground.
- `dates` gives the starting and ending dates.
- `temporal_api = "daily"` asks NASA for daily data.

## Step 3: Look at the Data

Before changing the data, it is useful to see what NASA returned.

```{r look-data}
head(phoenix_weather)
```

We can also use `glimpse()` to see the column names and data types.

```{r glimpse-data}
glimpse(phoenix_weather)
```

Each row shows one day. The `T2M` column contains the average temperature for that day in degrees Celsius.

## Step 4: Create a Date Column

The NASA data includes separate columns for the year, month, and day. We can combine them into one date column. This will make the data easier to graph.

```{r create-date}
phoenix_weather <- phoenix_weather %>%
  mutate(
    date = as.Date(as.character(YYYYMMDD), format = "%Y%m%d")
  )

head(phoenix_weather)
```

Now the dataset has a new column called `date`.

## Step 5: Find the Average Temperature

We can use `summarise()` to calculate the average temperature for the entire year.

```{r average}
phoenix_weather %>%
  summarise(
    average_temperature = mean(T2M, na.rm = TRUE)
  )
```

The `mean()` function calculates the average. The `na.rm = TRUE` part tells R to ignore missing values if there are any.

## Step 6: Create a Graph

Now we can use `ggplot2` to see how the temperature changed throughout the year.

```{r graph, fig.width=8, fig.height=4.5}
ggplot(phoenix_weather, aes(x = YYYYMMDD, y = T2M)) +
  geom_line() +
  labs(
    title = "Daily Temperature in Phoenix",
    subtitle = "NASA POWER Data - 2025",
    x = "Date",
    y = "Temperature (°C)"
  ) +
  theme_minimal()
```

The graph makes the temperature pattern easier to see. Phoenix should have lower temperatures near the beginning and end of the year and much higher temperatures during the summer.

## Conclusion

The `nasapower` package makes it easy to access NASA weather data directly from R. we can use `get_power()` to request real NASA data, look at the dataset, creat a date column, calculate the average temperature, and make a graph, which we did in this code through.

The same idea can also be used to study different cities and weather.
