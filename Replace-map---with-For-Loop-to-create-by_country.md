Replace map() with For Loop to create by\_country
================

## Introduce

In textbook <R for Data Science>(Authors:Garrett Grolemund and Hadley
Wickham ,<https://r4ds.had.co.nz/>), there is a data frame named
by\_country(refer to 25.2.2 List-columns). Authors used function map()
to create the data frame which includes column model presenting
lifeExp\~year models for each country.

Let’s find a way to replace map with for loop to create by\_country.

## code

``` r
by_country <- gapminder %>% 
  group_by(country, continent) %>% 
  nest()
by_country$data[[1]]
```

    ## # A tibble: 12 x 4
    ##     year lifeExp      pop gdpPercap
    ##    <int>   <dbl>    <int>     <dbl>
    ##  1  1952    28.8  8425333      779.
    ##  2  1957    30.3  9240934      821.
    ##  3  1962    32.0 10267083      853.
    ##  4  1967    34.0 11537966      836.
    ##  5  1972    36.1 13079460      740.
    ##  6  1977    38.4 14880372      786.
    ##  7  1982    39.9 12881816      978.
    ##  8  1987    40.8 13867957      852.
    ##  9  1992    41.7 16317921      649.
    ## 10  1997    41.8 22227415      635.
    ## 11  2002    42.1 25268405      727.
    ## 12  2007    43.8 31889923      975.

``` r
country_model <- function(df) {
  lm(lifeExp ~ year, data = df)
}
```

``` r
# this is original code in textbook:models <- map(by_country$data, country_model)
models <- function(df) {
  out <- vector("list",length(df))
  for (i in seq_along(df)) {
    out[[i]] <- country_model(df[[i]])
  }
  out
}
```

``` r
by_country <- by_country %>% 
  mutate(model = models(data))
by_country
```

    ## # A tibble: 142 x 4
    ##    country     continent data              model 
    ##    <fct>       <fct>     <list>            <list>
    ##  1 Afghanistan Asia      <tibble [12 x 4]> <lm>  
    ##  2 Albania     Europe    <tibble [12 x 4]> <lm>  
    ##  3 Algeria     Africa    <tibble [12 x 4]> <lm>  
    ##  4 Angola      Africa    <tibble [12 x 4]> <lm>  
    ##  5 Argentina   Americas  <tibble [12 x 4]> <lm>  
    ##  6 Australia   Oceania   <tibble [12 x 4]> <lm>  
    ##  7 Austria     Europe    <tibble [12 x 4]> <lm>  
    ##  8 Bahrain     Asia      <tibble [12 x 4]> <lm>  
    ##  9 Bangladesh  Asia      <tibble [12 x 4]> <lm>  
    ## 10 Belgium     Europe    <tibble [12 x 4]> <lm>  
    ## # ... with 132 more rows
