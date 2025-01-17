# Data Wrangling and Analyses with Tidyverse

!!! info 
    
    === "Keypoints"
    
        - Use the `dplyr` package to manipulate data frames.
        - Use `glimpse()` to quickly look at your data frame.
        - Use `select()` to choose variables from a data frame.
        - Use `filter()` to choose data based on values.
        - Use `mutate()` to create new variables.
        - Use `group_by()` and `summarize()` to work with subsets of data.

    === "Objectives"
        - Describe what the `dplyr` package in R is used for.
        - Apply common `dplyr` functions to manipulate data in R.
        - Employ the 'pipe' operator to link together a sequence of functions.
        - Employ the 'mutate' function to apply other chosen functions to existing columns and create new columns of data.
        - Employ the 'split-apply-combine' concept to split the data into groups, apply analysis to each group, and combine the results.

    === "Questions" 
    
        - How can I manipulate data frames without repeating myself?


Bracket subsetting is handy, but it can be cumbersome and difficult to
read, especially for complicated operations.

Luckily, the [`dplyr`](https://cran.r-project.org/package=dplyr) package
provides a number of very useful functions for manipulating data frames
in a way that will reduce repetition, reduce the probability of making
errors, and probably even save you some typing. As an added bonus, you
might even find the `dplyr` grammar easier to read.

Here we're going to cover some of the most commonly used functions as
well as using pipes (`%>%`) to combine them:

1.  `glimpse()`
2.  `select()`
3.  `filter()`
4.  `group_by()`
5.  `summarize()`
6.  `mutate()`
7.  `pivot_longer` and `pivot_wider`

Packages in R are sets of additional functions that let you do more
stuff in R. The functions we've been using, like `str()`, come built
into R; packages give you access to more functions. You need to install
a package and then load it to be able to use it.

!!! r-project "r"

    ```r
    # Installs dplyr package 
    install.packages("dplyr") 
    # Installs tidyr package 
    install.packages("tidyr") 
    # Installs ggplot2 package 
    install.packages("ggplot2") 
    # Install readr package`
    install.packages("readr") 
    ```

You might get asked to choose a CRAN mirror — this is asking you to
choose a site to download the package from. The choice doesn't matter
too much; I'd recommend choosing the RStudio mirror.

!!! r-project "r"

    ```r
    # Loads in dplyr package to use 
    library("dplyr")          
    # Loads in tidyr package to use 
    library("tidyr")          
    # Loads in ggplot2 package to use 
    library("ggplot2")          
    # Load in readr package to use`
    library("readr")          
    ```

You only need to install a package once per computer, but you need to
load it every time you open a new R session and want to use that
package.

!!! tip "Installing packages"

    It may be temping to install the `tidyverse` package, as it contains
    many useful collection of packages for this lesson and beyond.
    However, when teaching or following this lesson, we advise that
    participants install `dplyr`, `readr`, `ggplot2`, and `tidyr`
    individually as shown above. Otherwise, a substaial amount of the
    lesson will be spend waiting for the installation to complete.


## What is dplyr?

The package `dplyr` is a fairly new (2014) package that tries to provide
easy tools for the most common data manipulation tasks. This package is
also included in the [`tidyverse` package](https://www.tidyverse.org/),
which is a collection of eight different packages (`dplyr`, `ggplot2`,
`tibble`, `tidyr`, `readr`, `purrr`, `stringr`, and `forcats`). It is
built to work directly with data frames. The thinking behind it was
largely inspired by the package `plyr` which has been in use for some
time but suffered from being slow in some cases.`dplyr` addresses this
by porting much of the computation to C++. An additional feature is the
ability to work with data stored directly in an external database. The
benefits of doing this are that the data can be managed natively in a
relational database, queries can be conducted on that database, and only
the results of the query returned.

This addresses a common problem with R in that all operations are
conducted in memory and thus the amount of data you can work with is
limited by available memory. The database connections essentially remove
that limitation in that you can have a database that is over 100s of GB,
conduct queries on it directly and pull back just what you need for
analysis in R.

## Loading .csv files in tidy style

The Tidyverse's `readr` package provides its own unique way of loading
.csv files in to R using `read_csv()`, which is similar to `read.csv()`.
`read_csv()` allows users to load in their data faster, doesn't create
row names, and allows you to access non-standard variable names (ie.
variables that start with numbers of contain spaces), and outputs your
data on the R console in a tidier way. In short, it's a much friendlier
way of loading in potentially messy data.

Now let's load our vcf .csv file using `read_csv()`:

!!! r-project "r"

    ```r
    variants <- read_csv("combined_tidy_vcf.csv")
    ```

??? success "Output"

    ```
    Rows: 801 Columns: 29
    
    ── Column specification ────────────────────────────────────────────────────────────────────────
    Delimiter: ","
    chr  (7): sample_id, CHROM, REF, ALT, DP4, Indiv, gt_GT_alleles
    dbl (16): POS, QUAL, IDV, IMF, DP, VDB, RPB, MQB, BQB, MQSB, SGB, MQ0F, AC, AN, MQ, gt_GT
    num  (1): gt_PL
    lgl  (5): ID, FILTER, INDEL, ICB, HOB

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ```

## Taking a quick look at data frames

Similar to `str()`, which comes built into R, `glimpse()` is a `dplyr`
function that (as the name suggests) gives a glimpse of the data frame.

!!! r-project "r"

    ```r
    glimpse(variants)
    ```

??? success "Output"

    ```
    Rows: 801
    Columns: 29
    $ sample_id     <chr> "SRR2584863", "SRR2584863", "SRR2584863", "SRR2584863", "SRR2584863", "S…
    $ CHROM         <chr> "CP000819.1", "CP000819.1", "CP000819.1", "CP000819.1", "CP000819.1", "C…
    $ POS           <dbl> 9972, 263235, 281923, 433359, 473901, 648692, 1331794, 1733343, 2103887,…
    $ ID            <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ REF           <chr> "T", "G", "G", "CTTTTTTT", "CCGC", "C", "C", "G", "ACAGCCAGCCAGCCAGCCAGC…
    $ ALT           <chr> "G", "T", "T", "CTTTTTTTT", "CCGCGC", "T", "A", "A", "ACAGCCAGCCAGCCAGCC…
    $ QUAL          <dbl> 91.0000, 85.0000, 217.0000, 64.0000, 228.0000, 210.0000, 178.0000, 225.0…
    $ FILTER        <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ INDEL         <lgl> FALSE, FALSE, FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, TRUE, TRUE, FALSE,…
    $ IDV           <dbl> NA, NA, NA, 12, 9, NA, NA, NA, 2, 7, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
    $ IMF           <dbl> NA, NA, NA, 1.000000, 0.900000, NA, NA, NA, 0.666667, 1.000000, NA, NA, …
    $ DP            <dbl> 4, 6, 10, 12, 10, 10, 8, 11, 3, 7, 9, 20, 12, 19, 15, 10, 14, 9, 13, 2, …
    $ VDB           <dbl> 0.0257451, 0.0961330, 0.7740830, 0.4777040, 0.6595050, 0.2680140, 0.6240…
    $ RPB           <dbl> NA, 1.000000, NA, NA, NA, NA, NA, NA, NA, NA, 0.900802, NA, 0.954207, NA…
    $ MQB           <dbl> NA, 1.0000000, NA, NA, NA, NA, NA, NA, NA, NA, 0.1501340, NA, 0.0497871,…
    $ BQB           <dbl> NA, 1.000000, NA, NA, NA, NA, NA, NA, NA, NA, 0.750668, NA, 0.774755, NA…
    $ MQSB          <dbl> NA, NA, 0.974597, 1.000000, 0.916482, 0.916482, 0.900802, 1.007750, 1.00…
    $ SGB           <dbl> -0.556411, -0.590765, -0.662043, -0.676189, -0.662043, -0.670168, -0.651…
    $ MQ0F          <dbl> 0.000000, 0.166667, 0.000000, 0.000000, 0.000000, 0.000000, 0.000000, 0.…
    $ ICB           <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ HOB           <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, …
    $ AC            <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
    $ AN            <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
    $ DP4           <chr> "0,0,0,4", "0,1,0,5", "0,0,4,5", "0,1,3,8", "1,0,2,7", "0,0,7,3", "0,0,3…
    $ MQ            <dbl> 60, 33, 60, 60, 60, 60, 60, 60, 60, 60, 25, 60, 10, 60, 60, 60, 60, 60, …
    $ Indiv         <chr> "/home/dcuser/dc_workshop/results/bam/SRR2584863.aligned.sorted.bam", "/…
    $ gt_PL         <dbl> 1210, 1120, 2470, 910, 2550, 2400, 2080, 2550, 11128, 1940, 1310, 2550, …
    $ gt_GT         <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
    $ gt_GT_alleles <chr> "G", "T", "T", "CTTTTTTTT", "CCGCGC", "T", "A", "A", "ACAGCCAGCCAGCCAGCC…
    ```

In the above output, we can already gather some information about
`variants`, such as the number of rows and columns, column names, type
of vector in the columns, and the first few entries of each column.
Although what we see is similar to outputs of `str()`, this method gives
a cleaner visual output.

## Selecting columns and filtering rows

To select columns of a data frame, use `select()`. The first argument to
this function is the data frame (`variants`), and the subsequent
arguments are the columns to keep.

!!! r-project "r"

    ```r
    select(variants, sample_id, REF, ALT, DP)
    ```

??? success "Output"

    ```
    # A tibble: 801 × 4
       sample_id  REF                              ALT                                            DP
       <chr>      <chr>                            <chr>                                       <dbl>
     1 SRR2584863 T                                G                                               4
     2 SRR2584863 G                                T                                               6
     3 SRR2584863 G                                T                                              10
     4 SRR2584863 CTTTTTTT                         CTTTTTTTT                                      12
     5 SRR2584863 CCGC                             CCGCGC                                         10
     6 SRR2584863 C                                T                                              10
     7 SRR2584863 C                                A                                               8
     8 SRR2584863 G                                A                                              11
     9 SRR2584863 ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAG ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCC…     3
    10 SRR2584863 AT                               ATT                                             7
    # … with 791 more rows
    # ℹ Use `print(n = ...)` to see more rows
    ```

To select all columns *except* certain ones, put a "-" in front of the
variable to exclude it.

!!! r-project "r"

    ```r
    select(variants, -CHROM)
    ```

??? success "Output"

    ```
    # A tibble: 801 × 28
       sample_id      POS ID    REF   ALT    QUAL FILTER INDEL   IDV    IMF    DP    VDB   RPB   MQB
       <chr>        <dbl> <lgl> <chr> <chr> <dbl> <lgl>  <lgl> <dbl>  <dbl> <dbl>  <dbl> <dbl> <dbl>
     1 SRR2584863    9972 NA    T     G        91 NA     FALSE    NA NA         4 0.0257    NA    NA
     2 SRR2584863  263235 NA    G     T        85 NA     FALSE    NA NA         6 0.0961     1     1
     3 SRR2584863  281923 NA    G     T       217 NA     FALSE    NA NA        10 0.774     NA    NA
     4 SRR2584863  433359 NA    CTTT… CTTT…    64 NA     TRUE     12  1        12 0.478     NA    NA
     5 SRR2584863  473901 NA    CCGC  CCGC…   228 NA     TRUE      9  0.9      10 0.660     NA    NA
     6 SRR2584863  648692 NA    C     T       210 NA     FALSE    NA NA        10 0.268     NA    NA
     7 SRR2584863 1331794 NA    C     A       178 NA     FALSE    NA NA         8 0.624     NA    NA
     8 SRR2584863 1733343 NA    G     A       225 NA     FALSE    NA NA        11 0.992     NA    NA
     9 SRR2584863 2103887 NA    ACAG… ACAG…    56 NA     TRUE      2  0.667     3 0.902     NA    NA
    10 SRR2584863 2333538 NA    AT    ATT     167 NA     TRUE      7  1         7 0.568     NA    NA
    # … with 791 more rows, and 14 more variables: BQB <dbl>, MQSB <dbl>, SGB <dbl>, MQ0F <dbl>,
    #   ICB <lgl>, HOB <lgl>, AC <dbl>, AN <dbl>, DP4 <chr>, MQ <dbl>, Indiv <chr>, gt_PL <dbl>,
    #   gt_GT <dbl>, gt_GT_alleles <chr>
    # ℹ Use `print(n = ...)` to see more rows, and `colnames()` to see all variable names
    ```

`dplyr` also provides useful functions to select columns based on their
names. For instance, `ends_with()` allows you to select columns that
ends with specific letters. For instance, if you wanted to select
columns that end with the letter "B":

!!! r-project "r"

    ```r
    select(variants, ends_with("B"))
    ```

??? success "Output"

    ```
    # A tibble: 801 × 8
          VDB   RPB   MQB   BQB   MQSB    SGB ICB   HOB  
        <dbl> <dbl> <dbl> <dbl>  <dbl>  <dbl> <lgl> <lgl>
     1 0.0257    NA    NA    NA NA     -0.556 NA    NA   
     2 0.0961     1     1     1 NA     -0.591 NA    NA   
     3 0.774     NA    NA    NA  0.975 -0.662 NA    NA   
     4 0.478     NA    NA    NA  1     -0.676 NA    NA   
     5 0.660     NA    NA    NA  0.916 -0.662 NA    NA   
     6 0.268     NA    NA    NA  0.916 -0.670 NA    NA   
     7 0.624     NA    NA    NA  0.901 -0.651 NA    NA   
     8 0.992     NA    NA    NA  1.01  -0.670 NA    NA   
     9 0.902     NA    NA    NA  1     -0.454 NA    NA   
    10 0.568     NA    NA    NA  1.01  -0.617 NA    NA   
    # … with 791 more rows
    # ℹ Use `print(n = ...)` to see more rows
    ```

!!! question "Challenge"

    Create a table that contains all the columns with the letter "i" and
    column "POS", without columns "Indiv" and "FILTER". Hint: look at for
    a function called `contains()`, which can be found in the help
    documentation for ends with we just covered (`?ends_with`). Note that
    contains() is not case sensistive.

    ??? success "Solution"

        !!! r-project "r"

            ```r
            # First, we select "POS" and all columns with letter "i". This will contain columns     Indiv and FILTER. 
            variants_subset <- select(variants, POS, contains("i"))

            # Next, we remove columns Indiv and FILTER
            variants_result <- select(variants_subset, -Indiv, -FILTER)
            variants_result
            ```

        !!! success "Output"

            ```
            # A tibble: 801 × 7
                   POS sample_id  ID    INDEL   IDV    IMF ICB  
                 <dbl> <chr>      <lgl> <lgl> <dbl>  <dbl> <lgl>
             1    9972 SRR2584863 NA    FALSE    NA NA     NA   
             2  263235 SRR2584863 NA    FALSE    NA NA     NA   
             3  281923 SRR2584863 NA    FALSE    NA NA     NA   
             4  433359 SRR2584863 NA    TRUE     12  1     NA   
             5  473901 SRR2584863 NA    TRUE      9  0.9   NA   
             6  648692 SRR2584863 NA    FALSE    NA NA     NA   
             7 1331794 SRR2584863 NA    FALSE    NA NA     NA   
             8 1733343 SRR2584863 NA    FALSE    NA NA     NA   
             9 2103887 SRR2584863 NA    TRUE      2  0.667 NA   
            10 2333538 SRR2584863 NA    TRUE      7  1     NA   
            # … with 791 more rows
            # ℹ Use `print(n = ...)` to see more rows
            ```
        
    ??? success "Alternative solution"


        !!! r-project "r"

            ```r
            variants_result <- select(variants, POS, contains("i"), -Indiv, -FILTER)
            ```
        
To choose rows, use `filter()`:

!!! r-project "r"

    ```r
    filter(variants, sample_id == "SRR2584863")
    ```

??? success "Output"

    ```
    # A tibble: 25 × 29
       sample_id  CHROM     POS ID    REF   ALT    QUAL FILTER INDEL   IDV    IMF    DP    VDB   RPB
       <chr>      <chr>   <dbl> <lgl> <chr> <chr> <dbl> <lgl>  <lgl> <dbl>  <dbl> <dbl>  <dbl> <dbl>
     1 SRR2584863 CP000… 9.97e3 NA    T     G        91 NA     FALSE    NA NA         4 0.0257    NA
     2 SRR2584863 CP000… 2.63e5 NA    G     T        85 NA     FALSE    NA NA         6 0.0961     1
     3 SRR2584863 CP000… 2.82e5 NA    G     T       217 NA     FALSE    NA NA        10 0.774     NA
     4 SRR2584863 CP000… 4.33e5 NA    CTTT… CTTT…    64 NA     TRUE     12  1        12 0.478     NA
     5 SRR2584863 CP000… 4.74e5 NA    CCGC  CCGC…   228 NA     TRUE      9  0.9      10 0.660     NA
     6 SRR2584863 CP000… 6.49e5 NA    C     T       210 NA     FALSE    NA NA        10 0.268     NA
     7 SRR2584863 CP000… 1.33e6 NA    C     A       178 NA     FALSE    NA NA         8 0.624     NA
     8 SRR2584863 CP000… 1.73e6 NA    G     A       225 NA     FALSE    NA NA        11 0.992     NA
     9 SRR2584863 CP000… 2.10e6 NA    ACAG… ACAG…    56 NA     TRUE      2  0.667     3 0.902     NA
    10 SRR2584863 CP000… 2.33e6 NA    AT    ATT     167 NA     TRUE      7  1         7 0.568     NA
    # … with 15 more rows, and 15 more variables: MQB <dbl>, BQB <dbl>, MQSB <dbl>, SGB <dbl>,
    #   MQ0F <dbl>, ICB <lgl>, HOB <lgl>, AC <dbl>, AN <dbl>, DP4 <chr>, MQ <dbl>, Indiv <chr>,
    #   gt_PL <dbl>, gt_GT <dbl>, gt_GT_alleles <chr>
    # ℹ Use `print(n = ...)` to see more rows, and `colnames()` to see all variable names
    ```

`filter()` will keep all the rows that match the conditions that are
provided. Here are a few examples:

!!! r-project "r"

    ```r
    # Rows for which the reference genome has T or G
    filter(variants, REF %in% c("T", "G"))

    # Rows that have TRUE in the column INDEL
    filter(variants, INDEL)

    # Rows that don't have missing data in the IDV column
    filter(variants, !is.na(IDV))
    ```

We have a column titled "QUAL". This is a Phred-scaled confidence score
that a polymorphism exists at this position given the sequencing data.
Lower QUAL scores indicate low probability of a polymorphism existing at
that site. `filter()` can be useful for selecting mutations that have a
QUAL score above a certain threshold:

!!! r-project "r"

    ```r
    # Rows with QUAL values greater than or equal to 100
    filter(variants, QUAL >= 100)
    ```

`filter()` allows you to combine multiple conditions. You can separate
them using a `,` as arguments to the function, they will be combined
using the `&` (AND) logical operator. If you need to use the `|` (OR)
logical operator, you can specify it explicitly:

!!! r-project "r"

    ```r
    # Using `,` as AND
    filter(variants, sample_id == "SRR2584863", QUAL >= 100)

    # Using `&` as AND
    filter(variants, sample_id == "SRR2584863" & QUAL >= 100)

    # Using `|` logical operator
    filter(variants, sample_id == "SRR2584863", (MQ >= 50 | QUAL >= 100))
    ```

!!! question "Challenge"

    Select all the mutations that occurred between the positions 1e6 (one
    million) and 2e6 (inclusive) that have a QUAL greater than 200, and
    exclude INDEL mutations. Hint: to flip logical values such as TRUE to
    a FALSE, we can use to negation symbol "!". (eg. !TRUE == FALSE).

    ??? success "Solution"

        !!! r-project "r"

            ```r
            filter(variants, POS >= 1e6 & POS <= 2e6, QUAL > 200, !INDEL)
            ```

        !!! success "Output"

            ```
            # A tibble: 77 × 29
               sampl…¹ CHROM    POS ID    REF   ALT    QUAL FILTER INDEL   IDV   IMF    DP   VDB   RPB   MQB
               <chr>   <chr>  <dbl> <lgl> <chr> <chr> <dbl> <lgl>  <lgl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
             1 SRR258… CP00… 1.73e6 NA    G     A       225 NA     FALSE    NA    NA    11 0.992    NA    NA
             2 SRR258… CP00… 1.00e6 NA    A     G       225 NA     FALSE    NA    NA    15 0.481    NA    NA
             3 SRR258… CP00… 1.02e6 NA    A     G       225 NA     FALSE    NA    NA    12 0.242    NA    NA
             4 SRR258… CP00… 1.06e6 NA    C     T       225 NA     FALSE    NA    NA    17 0.346    NA    NA
             5 SRR258… CP00… 1.06e6 NA    A     G       206 NA     FALSE    NA    NA     9 0.630    NA    NA
             6 SRR258… CP00… 1.07e6 NA    G     T       225 NA     FALSE    NA    NA    11 0.349    NA    NA
             7 SRR258… CP00… 1.07e6 NA    T     C       225 NA     FALSE    NA    NA    12 0.196    NA    NA
             8 SRR258… CP00… 1.10e6 NA    C     T       225 NA     FALSE    NA    NA    15 0.454    NA    NAincluding
             9 SRR258… CP00… 1.11e6 NA    C     T       212 NA     FALSE    NA    NA     9 0.179    NA    NA
            10 SRR258… CP00… 1.11e6 NA    A     G       225 NA     FALSE    NA    NA    14 0.909    NA    NA
            # … with 67 more rows, 14 more variables: BQB <dbl>, MQSB <dbl>, SGB <dbl>, MQ0F <dbl>,
            #   ICB <lgl>, HOB <lgl>, AC <dbl>, AN <dbl>, DP4 <chr>, MQ <dbl>, Indiv <chr>, gt_PL <dbl>,
            #   gt_GT <dbl>, gt_GT_alleles <chr>, and abbreviated variable name ¹​sample_id
            # ℹ Use `print(n = ...)` to see more rows, and `colnames()` to see all variable names
            ```


## Pipes

But what if you wanted to select and filter? We can do this with pipes.
Pipes, are a fairly recent addition to R. Pipes let you take the output
of one function and send it directly to the next, which is useful when
you need to many things to the same data set. It was possible to do this
before pipes were added to R, but it was much messier and more
difficult. Pipes in R look like `%>%` and are made available via the
`magrittr` package, which is installed as part of `dplyr`. If you use
RStudio, you can type the pipe with <kbd>Ctrl</kbd> +
<kbd>Shift</kbd> + <kbd>M</kbd> if
you're using a PC, or <kbd>Cmd</kbd> +
<kbd>Shift</kbd> + <kbd>M</kbd> if
you're using a Mac.

!!! r-project "r"

    ```r
    variants %>%   
      filter(sample_id == "SRR2584863") %>%   
      select(REF, ALT, DP)
    ```

??? success "Output"

    ```
    # A tibble: 25 × 3
       REF                              ALT                                                       DP
       <chr>                            <chr>                                                  <dbl>
     1 T                                G                                                          4
     2 G                                T                                                          6
     3 G                                T                                                         10
     4 CTTTTTTT                         CTTTTTTTT                                                 12
     5 CCGC                             CCGCGC                                                    10
     6 C                                T                                                         10
     7 C                                A                                                          8
     8 G                                A                                                         11
     9 ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAG ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGC…     3
    10 AT                               ATT                                                        7
    # … with 15 more rows
    # ℹ Use `print(n = ...)` to see more rows
    ```

In the above code, we use the pipe to send the `variants` data set first
through `filter()`, to keep rows where `sample_id` matches a particular
sample, and then through `select()` to keep only the `REF`, `ALT`, and
`DP` columns. Since `%>%` takes the object on its left and passes it as
the first argument to the function on its right, we don't need to
explicitly include the data frame as an argument to the `filter()` and
`select()` functions any more.

Some may find it helpful to read the pipe like the word "then". For
instance, in the above example, we took the data frame `variants`,
*then* we `filter`ed for rows where `sample_id` was SRR2584863, *then*
we `select`ed the `REF`, `ALT`, and `DP` columns, *then* we showed only
the first six rows. The **`dplyr`** functions by themselves are somewhat
simple, but by combining them into linear workflows with the pipe, we
can accomplish more complex manipulations of data frames.

If we want to create a new object with this smaller version of the data
we can do so by assigning it a new name:

!!! r-project "r"

    ```r
    SRR2584863_variants <- variants %>%   
      filter(sample_id == "SRR2584863") %>%   
      select(REF, ALT, DP)
    ```

This new object includes all of the data from this sample. Let's look at
just the first six rows to confirm it's what we want:

!!! r-project "r"

    ```r
    SRR2584863_variants
    ```

??? success "Output"

    ```
    # A tibble: 25 × 3
       REF                              ALT                                                       DP
       <chr>                            <chr>                                                  <dbl>
     1 T                                G                                                          4
     2 G                                T                                                          6
     3 G                                T                                                         10
     4 CTTTTTTT                         CTTTTTTTT                                                 12
     5 CCGC                             CCGCGC                                                    10
     6 C                                T                                                         10
     7 C                                A                                                          8
     8 G                                A                                                         11
     9 ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAG ACAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGCCAGC…     3
    10 AT                               ATT                                                        7
    # … with 15 more rows
    # ℹ Use `print(n = ...)` to see more rows
    ```

Similar to `head()` and `tail()` functions, we can also look at the
first or last six rows using tidyverse function `slice()`. Slice is a
more versatile function that allows users to specify a range to view:

!!! r-project "r"

    ```r
    SRR2584863_variants %>% slice(1:6)
    ```

??? success "Output"

    ```
    # A tibble: 6 × 3
      REF      ALT          DP
      <chr>    <chr>     <dbl>
    1 T        G             4
    2 G        T             6
    3 G        T            10
    4 CTTTTTTT CTTTTTTTT    12
    5 CCGC     CCGCGC       10
    6 C        T            10
    ```

!!! r-project "r"

    ```r
    SRR2584863_variants %>% slice(10:25)
    ```

??? success "Output"

    ```
    # A tibble: 16 × 3
       REF   ALT      DP
       <chr> <chr> <dbl>
     1 AT    ATT       7
     2 A     C         9
     3 A     C        20
     4 G     T        12
     5 A     T        19
     6 G     A        15
     7 A     C        10
     8 C     A        14
     9 A     G         9
    10 A     C        13
    11 A     AC        2
    12 G     T        10
    13 A     G        16
    14 A     C        11
    15 TGG   T        10
    16 A     C         9
    ```

!!! question "Exercise: Pipe and filter"

    Starting with the `variants` data frame, use pipes to subset the data
    to include only observations from SRR2584863 sample, where the
    filtered depth (DP) is at least 10. Showing only 5th through 11th rows
    of columns `REF`, `ALT`, and `POS`.

    ??? success "Solution"

        !!! r-project "r"

            ```r
            variants %>%
              filter(sample_id == "SRR2584863" & DP >= 10) %>%
              slice(5:11) %>%
              select(sample_id, DP, REF, ALT, POS)
            ```

        !!! success "Output"

            ```
            # A tibble: 7 × 5
              sample_id     DP REF   ALT       POS
              <chr>      <dbl> <chr> <chr>   <dbl>
            1 SRR2584863    11 G     A     1733343
            2 SRR2584863    20 A     C     2446984
            3 SRR2584863    12 G     T     2618472
            4 SRR2584863    19 A     T     2665639
            5 SRR2584863    15 G     A     2999330
            6 SRR2584863    10 A     C     3339313
            7 SRR2584863    14 C     A     3401754
            ```
        

## Mutate

Frequently you'll want to create new columns based on the values in
existing columns, for example to do unit conversions or find the ratio
of values in two columns. For this we'll use the `dplyr` function
`mutate()`.

For example, we can convert the polymorphism confidence value QUAL to a
probability value according to the formula:

$$\textrm {Probability} = 1- 10 ^ {-\frac{\textrm{QUAL}}{10}}$$

We can use `mutate` to add a column (`POLPROB`) to our `variants` data
frame that shows the probability of a polymorphism at that site given
the data.

!!! r-project "r"

    ```r
    variants %>%   
      mutate(POLPROB = 1 - (10 ^ -(QUAL/10)))
    ```

!!! question "Exercise"

    There are a lot of columns in our data set, so let's just look at the
    `sample_id`, `POS`, `QUAL`, and `POLPROB` columns for now. Add a line
    to the above code to only show those columns.

    ??? success "Solution"

        !!! r-project "r"

            ```r
            variants %>%   
              mutate(POLPROB = 1 - (10 ^ -(QUAL/10))) %>%
              select(sample_id, POS, QUAL, POLPROB)
            ```

        !!! success "Output"

            ```
            # A tibble: 801 × 4
               sample_id      POS  QUAL POLPROB
               <chr>        <dbl> <dbl>   <dbl>
             1 SRR2584863    9972    91    1.00
             2 SRR2584863  263235    85    1.00
             3 SRR2584863  281923   217    1   
             4 SRR2584863  433359    64    1.00
             5 SRR2584863  473901   228    1   
             6 SRR2584863  648692   210    1   
             7 SRR2584863 1331794   178    1   
             8 SRR2584863 1733343   225    1   
             9 SRR2584863 2103887    56    1.00
            10 SRR2584863 2333538   167    1   
            # … with 791 more rows
            # ℹ Use `print(n = ...)` to see more rows
            ```


## `group_by()` and `summarize()` functions

Many data analysis tasks can be approached using the
"split-apply-combine" paradigm: split the data into groups, apply some
analysis to each group, and then combine the results. `dplyr` makes this
very easy through the use of the `group_by()` function, which splits the
data into groups.

We can use `group_by()` to tally the number of mutations detected in
each sample using the function `tally()`:

!!! r-project "r"

    ```r
    variants %>%   
      group_by(sample_id) %>%   
      tally()
    ```

??? success "Output"

    ```
    # A tibble: 3 × 2
      sample_id      n
      <chr>      <int>
    1 SRR2584863    25
    2 SRR2584866   766
    3 SRR2589044    10
    ```

Since counting or tallying values is a common use case for `group_by()`,
an alternative function was created to bypasses `group_by()` using the
function `count()`:

!!! r-project "r"

    ```r
    variants %>% 
      count(sample_id)
    ```

!!! question "Challenge"

    How many mutations are INDELs?

    ??? success "Solution"

        !!! r-project "r"

            ```r
            variants %>%
              count(INDEL)
            ```

        !!! success "Output"

            ```
            # A tibble: 2 × 2
              INDEL     n
              <lgl> <int>
            1 FALSE   700
            2 TRUE    101
            ```


When the data is grouped, `summarize()` can be used to collapse each
group into a single-row summary. `summarize()` does this by applying an
aggregating or summary function to each group.

It can be a bit tricky at first, but we can imagine physically splitting
the data frame by groups and applying a certain function to summarize
the data.

![split_apply_combine](../figures/split_apply_combine.png){width="500"}[^1]

We can also apply many other functions to individual columns to get
other summary statistics. For example,we can use built-in functions like
`mean()`, `median()`, `min()`, and `max()`. These are called "built-in
functions" because they come with R and don't require that you install
any additional packages. By default, all **R functions operating on
vectors that contains missing data will return NA**. It's a way to make
sure that users know they have missing data, and make a conscious
decision on how to deal with it. When dealing with simple statistics
like the mean, the easiest way to ignore `NA` (the missing data) is to
use `na.rm = TRUE` (`rm` stands for remove).

So to view the mean, median, maximum, and minimum filtered depth (`DP`)
for each sample:

!!! r-project "r"

    ```r
    variants %>%   
      group_by(sample_id) %>%   
      summarize(     
        mean_DP = mean(DP),     
        median_DP = median(DP),     
        min_DP = min(DP),     
        max_DP = max(DP)
      )
    ```

??? success "Output"

    ```
    # A tibble: 3 × 5
      sample_id  mean_DP median_DP min_DP max_DP
      <chr>        <dbl>     <dbl>  <dbl>  <dbl>
    1 SRR2584863    10.4      10        2     20
    2 SRR2584866    10.6      10        2     79
    3 SRR2589044     9.3       9.5      3     16
    ```

## Reshaping data frames

It can sometimes be useful to transform the "long" tidy format, into the
wide format. This transformation can be done with the `pivot_wider()`
function provided by the `tidyr` package (also part of the `tidyverse`).

`pivot_wider()` takes a data frame as the first argument, and two
arguments: the column name that will become the columns and the column
name that will become the cells in the wide data.

!!! r-project "r"

    ```r
    variants_wide <- variants %>%
      group_by(sample_id, CHROM) %>%
      summarize(mean_DP = mean(DP)) %>%
      pivot_wider(names_from = sample_id, values_from = mean_DP)

    variants_wide
    ```

??? success "Output"

    ```
    # A tibble: 1 × 4
      CHROM      SRR2584863 SRR2584866 SRR2589044
      <chr>           <dbl>      <dbl>      <dbl>
    1 CP000819.1       10.4       10.6        9.3
    ```

The opposite operation of `pivot_wider()` is taken care by
`pivot_longer()`. We specify the names of the new columns, and here add
`-CHROM` as this column shouldn't be affected by the reshaping:


!!! r-project "r"

    ```r
    variants_wide %>%
      pivot_longer(-CHROM, names_to = "sample_id", values_to = "mean_DP")
    ```

??? success "Output"

    ```
    # A tibble: 3 × 3
      CHROM      sample_id  mean_DP
      <chr>      <chr>        <dbl>
    1 CP000819.1 SRR2584863    10.4
    2 CP000819.1 SRR2584866    10.6
    3 CP000819.1 SRR2589044     9.3
    ```
## Resources

-   [Handy dplyr
    cheatsheet](https://github.com/rstudio/cheatsheets/raw/master/data-transformation.pdf)

-   [Much of this lesson was copied or adapted from Jeff Hollister's
    materials](http://usepa.github.io/introR/2015/01/14/03-Clean/)

[^1]: The figure was adapted from the Software Carpentry lesson, [R for
    Reproducible Scientific
    Analysis](https://swcarpentry.github.io/r-novice-gapminder/13-dplyr/)
