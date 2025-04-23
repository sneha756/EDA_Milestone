README
================

10.3.3

# 1. Explore the distribution of x, y, and z in diamonds

The `diamonds` dataset contains three variables `x`, `y`, and `z`, which represent the dimensions of the diamond.  
- `x` represents the length, - `y` represents the width, - `z` represents the depth (height).

By looking at the distributions of each, we can confirm if this makes sense based on their ranges and shapes.  
We expect `z` (depth) to generally be smaller than `x` and `y` (length and width), since diamonds are typically flatter than they are tall.

Below is the code to create histograms

``` r
# Load necessary library
library(ggplot2)
data(diamonds)

# Look at summaries
summary(diamonds[, c("x", "y", "z")])
```

    ##        x                y                z         
    ##  Min.   : 0.000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: 4.710   1st Qu.: 4.720   1st Qu.: 2.910  
    ##  Median : 5.700   Median : 5.710   Median : 3.530  
    ##  Mean   : 5.731   Mean   : 5.735   Mean   : 3.539  
    ##  3rd Qu.: 6.540   3rd Qu.: 6.540   3rd Qu.: 4.040  
    ##  Max.   :10.740   Max.   :58.900   Max.   :31.800

``` r
# Plot distributions
par(mfrow = c(1,3))  # plot 3 plots side-by-side

hist(diamonds$x, main = "Distribution of x (Length)", xlab = "x (mm)", col = "lightblue", border = "white")
hist(diamonds$y, main = "Distribution of y (Width)", xlab = "y (mm)", col = "lightgreen", border = "white")
hist(diamonds$z, main = "Distribution of z (Depth)", xlab = "z (mm)", col = "lightpink", border = "white")
```

![](README_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

``` r
par(mfrow = c(1,1))  # reset plotting area
```

From our diagram above we see that, z (depth) is less than both x (length) and y (width) which is in line with what we expected it to be as diamonds are flatter.

# 2. Explore the distribution of price

The price variable in the `diamonds` dataset represents the price of each diamond in US dollars.  
To explore its distribution properly, it’s important to try different **binwidths** in the histogram.  
This helps us see patterns that could be hidden if the bins are too wide or too narrow.

After plotting the distribution with several binwidths, I noticed: - The distribution is heavily **right-skewed**: most diamonds are lower priced, but a few are extremely expensive. - There are **some gaps** or **strange patterns** at very high prices, suggesting there might be clusters at certain high price points.

Let’s explore this visually. Below is the code to histograms with differing binwidths to see the differences.

``` r
# Base histogram
ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 500, fill = "skyblue", color = "black") +
  labs(title = "Distribution of Diamond Prices (Binwidth = 500)",
       x = "Price (USD)", y = "Count")
```

![](README_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
# Try smaller binwidth
ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 100, fill = "lightgreen", color = "black") +
  labs(title = "Distribution of Diamond Prices (Binwidth = 100)",
       x = "Price (USD)", y = "Count")
```

![](README_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
# Try very large binwidth
ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 1000, fill = "lightpink", color = "black") +
  labs(title = "Distribution of Diamond Prices (Binwidth = 1000)",
       x = "Price (USD)", y = "Count")
```

![](README_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

# 3. How many diamonds are 0.99 carat? How many are 1 carat?

We want to count how many diamonds have a carat weight of exactly **0.99** and exactly **1.00**.  
After checking the numbers, we find: - There are far fewer diamonds at 0.99 carat than at 1 carat. - This suggests that diamonds are often **cut** or **marketed** to hit “milestone weights” like 1.00 carat, because it is more desirable and valuable for buyers.

It’s likely that sellers aim to reach **1 carat** to command a higher price, and thus, fewer diamonds are sold at **0.99 carat** even if physically they could be very similar.

Let’s find the exact counts below which gives the code to do the same.

``` r
# How many diamonds at 0.99 carat
sum(diamonds$carat == 0.99)
```

    ## [1] 23

``` r
# How many diamonds at 1.00 carat
sum(diamonds$carat == 1.00)
```

    ## [1] 1558

# 4. Comparing coord_cartesian() vs. xlim()/ylim()

When zooming in on a plot:

- `coord_cartesian(xlim = ..., ylim = ...)` **zooms in without changing the underlying data**.  
  It just **adjusts the view window** — the histogram bins stay based on the full dataset.
- `xlim()` or `ylim()` **filters out the data before plotting**.  
  That means it **removes data points** outside the limits, and the histogram **recomputes the bins** using only the filtered data.

**Example difference:** - With `coord_cartesian()`, you keep the overall shape of the histogram — you’re just zooming in. - With `xlim()`, the histogram could look totally different because some data was **deleted** before plotting

**If you leave `binwidth` unset:** - `ggplot2` automatically chooses a binwidth for you. - Sometimes, this auto-chosen binwidth might be too large or too small for your zoomed-in view, making the histogram look strange (e.g., only a few bars, or super spiky).

**If you zoom so only half a bar shows (using coord_cartesian()):** - `coord_cartesian()` will **clip** the bar — you can see part of a bar cut off nicely. - With `xlim()`, the half-bar might **disappear completely** because the data for that bar might have been removed.

``` r
# Default histogram (no binwidth set)
ggplot(diamonds, aes(x = price)) +
  geom_histogram() +
  labs(title = "Default Histogram with Auto Binwidth")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](README_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
# Zoom using coord_cartesian()
ggplot(diamonds, aes(x = price)) +
  geom_histogram() +
  coord_cartesian(xlim = c(0, 5000)) +
  labs(title = "Zoom with coord_cartesian() (0 to 5000)")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](README_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
# Zoom using xlim() -- cuts data
ggplot(diamonds, aes(x = price)) +
  geom_histogram() +
  xlim(0, 5000) +
  labs(title = "Zoom with xlim() (0 to 5000)")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 14714 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

    ## Warning: Removed 2 rows containing missing values or values outside the scale range
    ## (`geom_bar()`).

![](README_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

10.4.1

# 1. How missing values are handled in histograms vs. bar charts

**Missing values (NAs)** are handled **differently** in histograms and bar charts:

- In a **histogram** (which is for **continuous** data like `price` or `carat`),  
  missing values are simply **ignored** — they are **dropped automatically**.  
  The histogram only plots the non-missing data points, and you usually don’t even see a warning unless you look for it.

- In a **bar chart** (which is for **categorical** data like `cut` or `color`),  
  missing values are **treated like a separate category** if you want.  
  If you don’t tell ggplot to remove them, it will show a bar labeled “(NA)” for the missing values.

|  |
|----|
| ✅ **Why is there a difference?** |
| \- A **histogram** is about **binning numeric ranges** — it doesn’t make sense to bin an “NA” value. |
| \- A **bar chart** is about **counting categories** — and “NA” can be counted just like any other category. |

Let’s see examples!

## 5.2 Code

``` r
# Artificially create some missing data
set.seed(123)
diamonds_with_na <- diamonds
diamonds_with_na$price[sample(1:nrow(diamonds), 500)] <- NA  # randomly set 500 prices to NA
diamonds_with_na$cut[sample(1:nrow(diamonds), 300)] <- NA    # randomly set 300 cuts to NA

# Histogram: missing values ignored
ggplot(diamonds_with_na, aes(x = price)) +
  geom_histogram(binwidth = 500, fill = "skyblue", color = "black") +
  labs(title = "Histogram of Price (missing values ignored)")
```

    ## Warning: Removed 500 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](README_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
# Bar chart: missing values appear as a category
ggplot(diamonds_with_na, aes(x = cut)) +
  geom_bar(fill = "lightgreen", color = "black") +
  labs(title = "Bar Chart of Cut (missing values shown)")
```

![](README_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->

# 2. What does na.rm = TRUE do in mean() and sum()?

The argument `na.rm = TRUE` in functions like `mean()` and `sum()` means:

- **“Remove missing values (NAs) before calculating.”**

If you don’t use `na.rm = TRUE` and your data contains any NAs: - `mean()` and `sum()` will return **NA** as the result. - That’s because they can’t compute a total or average if there are missing values.

By setting `na.rm = TRUE`, you tell R to **ignore** the NAs and compute using only the available (non-missing) data.

------------------------------------------------------------------------

``` r
# Create a small vector with missing values
values <- c(1, 2, 3, NA, 5)

# Without na.rm
mean(values)  # returns NA
```

    ## [1] NA

``` r
sum(values)   # returns NA
```

    ## [1] NA

``` r
# With na.rm = TRUE
mean(values, na.rm = TRUE)  # returns 2.75
```

    ## [1] 2.75

``` r
sum(values, na.rm = TRUE)   # returns 11
```

    ## [1] 11

# 3. Frequency plot of scheduled_dep_time colored by cancellation status

We are asked to: - Create a frequency plot of `scheduled_dep_time`. - Color the bars by whether the flight was **cancelled** or not. - **Facet** the plot by the cancelled status (separate panels). - Try different `scales` options to balance the plots (because there are many more non-cancelled flights).

``` r
# Load necessary libraries
#install.packages("nycflights13")

library(ggplot2)
library(nycflights13)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
# Create a new cancelled variable
flights <- flights %>%
  mutate(cancelled = is.na(dep_time))  # TRUE if flight was cancelled

# Plot
ggplot(flights, aes(x = sched_dep_time, fill = cancelled)) +
  geom_histogram(binwidth = 100) +
  facet_wrap(~cancelled) +
  labs(title = "Scheduled Departure Times by Cancellation Status",
       x = "Scheduled Departure Time", y = "Number of Flights",
       fill = "Cancelled")
```

![](README_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->
