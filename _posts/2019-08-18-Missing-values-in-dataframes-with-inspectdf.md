
### Summarising `NA` by column in dataframes

Exploring the number of records containing missing values in a new set
of data is an important and well known exploratory check. However, `NA`s
can be introduced into your data for a multitude of other reasons, often
as a side effect of data manipulations like transforming columns or
performing joins. In most cases, the behaviour is expected, but
sometimes when things go wrong, tracing missing values back through a
sequence of steps can be a helpful diagnostic.

All of that is to say that it’s vital to have simple tools for
interrogating dataframes for missing values… enter `inspectdf`\!

#### Missingness by column: `inspectdf::inspect_na()`

The `inspect_na()` function from the `inspectdf` package is a simple
tool designed to quickly summarise the frequency of missingness by
columns in a dataframe. Firstly, install the `inspectdf` package by
running

``` r
install.packages("inspectdf")
```

Then load both the `inspectdf` and `dplyr` packages - the latter we’ll
just use for its built-in `starwars` dataset.

``` r
# load packages
library(inspectdf)
library(dplyr)

# quick peek at starwars data that comes with dplyr
head(starwars)
```

    ## # A tibble: 6 x 13
    ##   name  height  mass hair_color skin_color eye_color birth_year gender
    ##   <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> 
    ## 1 Luke…    172    77 blond      fair       blue            19   male  
    ## 2 C-3PO    167    75 <NA>       gold       yellow         112   <NA>  
    ## 3 R2-D2     96    32 <NA>       white, bl… red             33   <NA>  
    ## 4 Dart…    202   136 none       white      yellow          41.9 male  
    ## 5 Leia…    150    49 brown      light      brown           19   female
    ## 6 Owen…    178   120 brown, gr… light      blue            52   male  
    ## # … with 5 more variables: homeworld <chr>, species <chr>, films <list>,
    ## #   vehicles <list>, starships <list>

So how many missing values are there in `starwars`? Even looking at the
output of the `head()` function reveals that there are at least a few
`NA`s in there. The use of the `inspect_na()` function is very
straightforward:

``` r
starwars %>% inspect_na
```

    ## # A tibble: 13 x 3
    ##    col_name     cnt  pcnt
    ##    <chr>      <dbl> <dbl>
    ##  1 birth_year    44 50.6 
    ##  2 mass          28 32.2 
    ##  3 homeworld     10 11.5 
    ##  4 height         6  6.90
    ##  5 hair_color     5  5.75
    ##  6 species        5  5.75
    ##  7 gender         3  3.45
    ##  8 name           0  0   
    ##  9 skin_color     0  0   
    ## 10 eye_color      0  0   
    ## 11 films          0  0   
    ## 12 vehicles       0  0   
    ## 13 starships      0  0

The output is a simple `tibble` with columns showing the count (`cnt`)
and percentage (`pcnt`) of `NA`s corresponding to each column
(`col_name`) in the `starwars` data. For example, we can see that the
`birth_year` column has the highest number of `NA`s with over half
missing. Note that the tibble is sorted in descending order of the
frequency of `NA` occurrence.

By adding the `show_plot` command, the `tibble` can also be displayed
graphically:

``` r
starwars %>% inspect_na %>% show_plot
```

![](/assets/images/show_plot-1.png)<!-- -->

Although this is a simple summary, and you’ll find many other ways to do
this in R, I use this *all of the time* and find it very convenient to
have a one-liner to call on. Code efficiency matters\!

#### More on the `inspectdf` package and exploratory data analysis

`inspectdf` can be used to produce a number of common summaries with
minimal effort. See previous posts to learn how to [explore and
visualise categorical
data](https://alastairrushworth.github.io/Exploring-categorical-data-with-inspectdf/)
and to [calculate and display correlation
coefficients](https://alastairrushworth.github.io/Calculating-and-visualising-correlation-coefficients-with-inspectdf/).
For a more general overview, have a look at the [package
website](https://alastairrushworth.github.io/inspectdf/).

For a recent overview of R packages for exploratory analysis, you might
also be interested in the recent paper [The Landscape of R Packages for
Automated Exploratory Data Analysis by Mateusz Staniak and Przemysław
Biecek](https://arxiv.org/pdf/1904.02101.pdf).

#### Comments? Suggestions? Issues?

Any feedback is welcome\! Find me on twitter at
[rushworth\_a](https://twitter.com/rushworth_a) or [write a github
issue](https://github.com/alastairrushworth/inspectdf/issues).
