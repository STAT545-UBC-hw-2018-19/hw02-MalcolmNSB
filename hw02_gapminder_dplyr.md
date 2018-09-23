Exploring the gapminder using dplyr and ggplot2
================

In this document, we will be exploring the `gapminder` dataset using (among other tools) the `dplyr` and `ggplot2` functions. First we load the gapminder dataset and dplyr using the tidyverse package.

``` r
library(gapminder)
library(tidyverse)
```

    ## -- Attaching packages -------------------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.0.0     v purrr   0.2.5
    ## v tibble  1.4.2     v dplyr   0.7.6
    ## v tidyr   0.8.1     v stringr 1.3.1
    ## v readr   1.1.1     v forcats 0.3.0

    ## -- Conflicts ----------------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

Smell test the data
-------------------

In this section we determine some basic attributes of `gapminder`.

-   Is it a data.frame, a matrix, a vector, a list?

``` r
typeof(gapminder)
```

    ## [1] "list"

``` r
head(gapminder)
```

    ## # A tibble: 6 x 6
    ##   country     continent  year lifeExp      pop gdpPercap
    ##   <fct>       <fct>     <int>   <dbl>    <int>     <dbl>
    ## 1 Afghanistan Asia       1952    28.8  8425333      779.
    ## 2 Afghanistan Asia       1957    30.3  9240934      821.
    ## 3 Afghanistan Asia       1962    32.0 10267083      853.
    ## 4 Afghanistan Asia       1967    34.0 11537966      836.
    ## 5 Afghanistan Asia       1972    36.1 13079460      740.
    ## 6 Afghanistan Asia       1977    38.4 14880372      786.

-   What is its class?

``` r
class(gapminder)
```

    ## [1] "tbl_df"     "tbl"        "data.frame"

We see that `gapminder` is a data.frame, a tibble (`tbl`), and a tibble dataframe (`tbl_df`).

-   How many variables/columns?

``` r
ncol(gapminder)
```

    ## [1] 6

-   How many rows/observations?

``` r
nrow(gapminder)
```

    ## [1] 1704

-   Can you get these facts about “extent” or “size” in more than one way? Can you imagine different functions being useful in different contexts?

We can determine both the class and the number of rows & columns (among other information) of `gapminder` by using the `str` function,

``` r
str(gapminder)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    1704 obs. of  6 variables:
    ##  $ country  : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
    ##  $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
    ##  $ pop      : int  8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
    ##  $ gdpPercap: num  779 821 853 836 740 ...

which also displays the class(es) of gapminder and the number of rows & columns.

-   What data type is each variable?

1.  Continent and country: `character`
2.  Year and population: `integer`
3.  Life expectancy and GDP per capita: `numerical` (double)

Exploring a variable
--------------------

We shall pick,

Categorical variable: `continent`. Quantitative variable: `pop`(population).

-   What are possible values (or range, whichever is appropriate) of each variable?

``` r
distinct(select(gapminder, continent))
```

    ## # A tibble: 5 x 1
    ##   continent
    ##   <fct>    
    ## 1 Asia     
    ## 2 Europe   
    ## 3 Africa   
    ## 4 Americas 
    ## 5 Oceania

``` r
range(select(gapminder, pop))
```

    ## [1]      60011 1318683096

-   What values are typical? What’s the spread? What’s the distribution? Etc., tailored to the variable at hand.

``` r
gapminder %>% 
  select(pop) %>% 
  summary()
```

    ##       pop           
    ##  Min.   :6.001e+04  
    ##  1st Qu.:2.794e+06  
    ##  Median :7.024e+06  
    ##  Mean   :2.960e+07  
    ##  3rd Qu.:1.959e+07  
    ##  Max.   :1.319e+09

Plots
-----

Let's explore population visually, using log-scale.

``` r
ggplot(gapminder, aes(pop)) +
  scale_x_log10() +
  geom_histogram(aes(y=..density..), fill = "green") +
  geom_density()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](hw02_gapminder_dplyr_files/figure-markdown_github/unnamed-chunk-9-1.png)

Scatter plot of population with life expectancy:

``` r
ggplot(gapminder, aes(lifeExp,pop))+
  geom_point() +
  scale_y_log10()
```

![](hw02_gapminder_dplyr_files/figure-markdown_github/unnamed-chunk-10-1.png)

Here is a violin plot overlayed with a jitter plot for life expectancy across continents:

``` r
gapminder %>% 
  select(continent, lifeExp) %>% 
  ggplot(aes(continent, lifeExp)) +
  geom_violin() +
  geom_jitter(alpha = 0.2)
```

![](hw02_gapminder_dplyr_files/figure-markdown_github/unnamed-chunk-11-1.png)

In the following, we create a (log) plot of population across multiple years in Oceania, and Canada.

``` r
gapminder %>% 
  filter(continent == "Oceania" | country == "Canada") %>% 
  select(country, year,  pop) %>% 
  ggplot(aes(year, pop, shape = country)) +
  scale_y_log10() +
  geom_point()
```

![](hw02_gapminder_dplyr_files/figure-markdown_github/unnamed-chunk-12-1.png)
