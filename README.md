taxizedb
========



[![Build Status](https://travis-ci.org/ropensci/taxizedb.svg?branch=master)](https://travis-ci.org/ropensci/taxizedb)
[![codecov](https://codecov.io/gh/ropensci/taxizedb/branch/master/graph/badge.svg)](https://codecov.io/gh/ropensci/taxizedb)
[![rstudio mirror downloads](http://cranlogs.r-pkg.org/badges/taxizedb)](https://github.com/metacran/cranlogs.app)
[![cran version](https://www.r-pkg.org/badges/version/taxizedb)](https://cran.r-project.org/package=taxizedb)

`taxizedb` - Tools for Working with Taxonomic Databases on your Machine

[taxize](https://github.com/ropensci/taxize) is a heavily used taxonomic toolbelt
package in R - However, it makes web requests for nearly all methods. That is fine
for most cases, but when the user has many, many names it is much more efficient
to do requests to a local SQL database.

Not all taxonomic databases are publicly available, or possible to mash into a SQLized
version. Taxonomic DB's supported thus far:

* ITIS - they provide a SQL dump
* COL - they provide a SQL dump
* Theplantlist - we make a SQL database from CSV files they provide
* GBIF taxonomic backbone - we make a SQL database from darwin core archive

Get in touch [in the issues](https://github.com/ropensci/taxizedb/issues) with
any ideas on new data sources.

This package for each data sources performs the following tasks:

* Download database - `db_download_*`
* Load database into SQL - `db_load_*`
* Create `dplyr` SQL backend - `src_*`

## install

cran version


```r
install.packages("taxizedb")
```

dev version


```r
devtools::install_github("ropensci/taxizedb")
```


```r
library("taxizedb")
library("dplyr")
```

## start your SQL DBs

Remember to start your PostgreSQL database for ITIS and ThePlantList and your MySQL database for COL

## Download and load DBs

ITIS


```r
x <- db_download_itis()
db_load_itis(x)
```

The Plant List (TPL)


```r
x <- db_download_tpl()
db_load_tpl(x)
```

Cataloge of Life (COL)


```r
x <- db_download_itis()
db_load_itis(x)
```

## connect to the DBs

ITIS


```r
src <- src_itis()
```

TPL


```r
src <- src_tpl()
```

COL


```r
src <- src_col()
```

## query with SQL syntax


```r
sql_collect(src, "select * from hierarchy limit 5")
#> # A tibble: 5 × 5
#>                     hierarchy_string    tsn parent_tsn level childrencount
#> *                              <chr>  <int>      <int> <int>         <int>
#> 1                             202422 202422          0     0        145330
#> 2                      202422-846491 846491     202422     1          2666
#> 3               202422-846491-660046 660046     846491     2          2654
#> 4        202422-846491-660046-846497 846497     660046     3             7
#> 5 202422-846491-660046-846497-846508 846508     846497     4             6
```


```r
# or pipe the src to sql_collect
src %>% sql_collect("select * from hierarchy limit 5")
#> # A tibble: 5 × 5
#>                     hierarchy_string    tsn parent_tsn level childrencount
#> *                              <chr>  <int>      <int> <int>         <int>
#> 1                             202422 202422          0     0        145330
#> 2                      202422-846491 846491     202422     1          2666
#> 3               202422-846491-660046 660046     846491     2          2654
#> 4        202422-846491-660046-846497 846497     660046     3             7
#> 5 202422-846491-660046-846497-846508 846508     846497     4             6
```

## use dplyr verbs

get a `tbl`


```r
hiers <- src %>% tbl("hierarchy")
```

limit 10


```r
hiers %>% top_n(10)
#> Source:   query [?? x 5]
#> Database: postgres 9.6.0 [sacmac@localhost:5432/ITIS]
#>
#>                                                       hierarchy_string
#>                                                                  <chr>
#> 1                                                               202423
#> 2                                                        202423-914154
#> 3                                                 202423-914154-914155
#> 4                                          202423-914154-914155-914158
#> 5                                    202423-914154-914155-914158-82696
#> 6                             202423-914154-914155-914158-82696-563886
#> 7                       202423-914154-914155-914158-82696-563886-99208
#> 8                202423-914154-914155-914158-82696-563886-99208-100500
#> 9         202423-914154-914155-914158-82696-563886-99208-100500-563890
#> 10 202423-914154-914155-914158-82696-563886-99208-100500-563890-914213
#> # ... with more rows, and 4 more variables: tsn <int>, parent_tsn <int>,
#> #   level <int>, childrencount <int>
```

select certain fields


```r
hiers %>% select(tsn, level)
#> Source:   query [?? x 2]
#> Database: postgres 9.6.0 [sacmac@localhost:5432/ITIS]
#>
#>       tsn level
#>     <int> <int>
#> 1  202422     0
#> 2  846491     1
#> 3  660046     2
#> 4  846497     3
#> 5  846508     4
#> 6  846553     5
#> 7  954935     6
#> 8    5549     7
#> 9    5550     8
#> 10 954936     6
#> # ... with more rows
```

## Meta

* Please [report any issues or bugs](https://github.com/ropensci/taxizedb/issues).
* License: MIT
* Get citation information for `taxizedb` in R doing `citation(package = 'taxizedb')`
* Please note that this project is released with a [Contributor Code of Conduct](CONDUCT.md). By participating in this project you agree to abide by its terms.

[![ropensci_footer](https://ropensci.org/public_images/github_footer.png)](https://ropensci.org)
