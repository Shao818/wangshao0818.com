---
title: R数据科学Ⅴ：管道操作、数据读写与数据连接
author: 王芍
date: '2020-12-27'
slug: []
categories:
  - R
  - 数据科学
tags:
  - R
  - 数据科学
  - 数据结构
  - 管道操作
  - 数据读写
lastmod: '2020-12-27T17:03:00+08:00'
keywords: []
description: ''
comment: no
toc: true
autoCollapseToc: no
postMetaInFooter: no
hiddenFromHomePage: no
contentCopyright: no
reward: no
mathjax: no
mathjaxEnableSingleDollar: no
mathjaxEnableAutoNumber: no
hideHeaderAndFooter: no
flowchartDiagrams:
  enable: no
  options: ''
sequenceDiagrams:
  enable: no
  options: ''
weight: 105
---



{{% center %}} 
![](https://gitee.com/shao818/Figure/raw/master/null/20201227173200.png)
{{% /center %}}

<!--more-->

<div align=center> 
<font size=5 color=#C46657 face="仿宋">目录</font>
</div>

-------------------------------------------------------------------------------

<font size=2  face="仿宋">

-   [tidyverse 包简介](#tidyverse-包简介)
    -   [Hadley Wickham](#hadley-wickham)
    -   [`tidyverse` 包](#tidyverse-包)
    -   [tidyverse 与 data.table](#tidyverse-与-data.table)
-   [管道操作](#管道操作)
    -   [什么是管道操作？](#什么是管道操作)
    -   [常用管道操作](#常用管道操作)
    -   [在管道中使用匿名函数](#在管道中使用匿名函数)
    -   [在管道中访问对象内容](#在管道中访问对象内容)
-   [数据读写](#数据读写)
    -   [数据读写的包与函数](#数据读写的包与函数)
    -   [保存数据](#保存数据)
-   [数据连接](#数据连接)
    -   [数据连接简介](#数据连接简介)
    -   [合并行与合并列](#合并行与合并列)
    -   [根据值匹配合并数据框](#根据值匹配合并数据框)
    -   [集合运算](#集合运算)

-------------------------------------------------------------------------------

<font size=1  face="仿宋">

## `tidyverse` 包简介

### `Hadley Wickham`

-   R语言超级大神，ggplot2, tidyverse 作者，RStudio 首席科学家
-   荣获统计学界的诺贝尔奖：「2019 COPSS 奖」

![Hadley
Wickham](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20202927042932.png)

### `tidyverse` 包

![Core Tidy
Workflow](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203227043236.png)

-   用“现代的”、“优雅的”方式，以管道式、泛函式编程技术实现了数据科学的整个
    流程：数据导入、数据清洗、数据操作、数据可视化、数据建模、可重现与交
    互报告。 - 在 tidyverse 包的引领下，近年来涌现出一系列具体研究领域的
    tidy\* 版本的包。

### `tidyverse` 与 `data.table`

-   `tidyverse`操作数据语法优雅、容易上手，但效率与主打高效的 data.table
    包不可同日而语，处理几G甚至几十G的数据，需要用`data.table`。
-   但`data.table`的语法高度抽象、不容易上手。
-   也有不少包尝试底层用data.table，上层用tidyverse语法包装（转化），如
    `tidyfst`, `dtplyr`, `tidyfast`等。

## 管道操作

### 什么是管道操作？

-   `magrittr`包引入了管道操作，能够通过管道将数据从一个函数传给另一个函数，
    从而用若干函数构成的管道依次变换你的数据。
    引例，对数据集`warpbreaks`，按分类变量`wool`和`tension`分组，对连续变量
    `breaks`做分组汇总，分别计算均值、中位数、标准差：

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --
    
    ## √ ggplot2 3.3.2     √ purrr   0.3.4
    ## √ tibble  3.0.4     √ dplyr   1.0.2
    ## √ tidyr   1.1.2     √ stringr 1.4.0
    ## √ readr   1.4.0     √ forcats 0.5.0
    
    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
mtcars %>%
 group_by(cyl) %>%
 summarise(mpg_avg = mean(mpg))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)
    
    ## # A tibble: 3 x 2
    ##     cyl mpg_avg
    ##   <dbl>   <dbl>
    ## 1     4    26.7
    ## 2     6    19.7
    ## 3     8    15.1

-   管道运算符`%>%`的意思是：将左边的运算结果，以输入的方式传给右边函数。若
    干个函数通过管道链接起来，叫做管道（pipeline）

<!-- -->

    x %>% f() %>% g() # 等同于 g(f(x))

管道，也支持 R base 函数：

``` r
month.abb %>% # 内置月份名缩写字符向量
 sample(6) %>% 
 tolower() %>% 
 paste0(collapse = "|")
```

    ## [1] "jun|oct|sep|may|nov|dec"

> 使用管道的好处是： - 避免使用过多的中间变量； - 程序可读性大大增强：

-   管道操作的过程，读起来就是对原数据集依次进行一系列操作的过程。而非管道
    操作，读起来与操作的过程是相反的，比如同样实现上例：

``` r
paste0(tolower(sample(month.abb, 6)), collapse="|")
```

    ## [1] "jan|oct|feb|mar|aug|apr"

### 常用管道操作

#### 管道默认将输出传给下一个函数的第1个参数

``` r
c(1, 3, 4, 5, NA) %>% mean(., na.rm=TRUE)
```

    ## [1] 3.25

``` r
c(1, 3, 4, 5, NA) %>% mean(na.rm = TRUE) # "."可以省略
```

    ## [1] 3.25

#### 输出可以在右边使用多次

-   在右边的函数调用中需要借助 “.”：

``` r
c(1, 3, 4, 5) %>% plot(., main=paste(., collapse=", "))
```

![](张敬信数据科学系列04_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
# 第1个"."可以省略, 其余的"."不能省略
c(1, 3, 4, 5) %>% plot(main=paste(., collapse=", "))
```

![](张敬信数据科学系列04_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->
\#\#\#\# 输出传给不是第1个参数

-   若函数的第1个参数不是接受数据，需要手动放置 “.”

``` r
iris %>% plot(Sepal.Width ~ Petal.Width, data = .) # 正确做法
```

![](张敬信数据科学系列04_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

### 在管道中使用匿名函数

``` r
month.abb %>% # 选择子集
 {.[1:3]}
```

    ## [1] "Jan" "Feb" "Mar"

``` r
# 等同于
month.abb %>%
 (function(.) {.[1:3]})
```

    ## [1] "Jan" "Feb" "Mar"

``` r
models <- mtcars %>%
 split(.$cyl) %>%
 map(~lm(mpg ~ wt, data = .))
```

> 注： 最后一行代码同`map(function(df) lm(mpg ~ wt, data = df))`

### 在管道中访问对象内容

你可能想要用`[`或`[[`或`$`访问
`"."`的内容，`magrittr`包提供了一些函数来实现这样
的操作，更多函数可用`?magrittr::extract`查阅：

![](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203327043335.png)

## 数据读写

### 数据读写的包与函数

#### readr包

读写带分隔符的文本文件，如`csv`和`tsv`;
也能读写序列化的R对象`rds`，若想保存数
据集后续再加载回来，`rds`将保存元数据和该对象的状态，如分组和数据类型。

-   读入数据到数据框：`read_csv()` 和`read_tsv()`

-   读入欧式格式数据：`read_csv2()`和`read_tsv2()`

-   读写`rds`数据：`read_rds()`和`write_rds()`

-   写出数据到文件：`write_csv()`,
    `write_tsv()`,`write_csv2()`,`write_tsv2()`

-   转化数据类型：`parse_number()`, `parse_logical()`,
    `parse_factor()`等

> 注：欧式格式数据以“;”为分隔符，“,” 为小数位。

#### readxl 包

专门读取`Excel`文件，包括同一个工作簿中的不同工作表。 - `read_excel()`:
自动检测`xls`或`xlsx`文件 - `read_xls()`: 读取`xls`文件 - `read_xlsx()`:
读取`xlsx`文件

#### haven 包

读写`SPSS`, `Stata`, `SAS`数据文件。 - 读：`read_spss()`,
`read_stata()`, `read_sas()` - 写：`write_spss()`, `write_stata()`,
`write_sas()`

#### readtext 包

读取全部文本文件的内容到数据框，每个文件变成一行，常用于文本挖掘或数据收集；`readtext`还支持读取`csv`,
`tab`, `json`,`xml`, `html`,`pdf`,`doc`, `docx`, `rtf`,`xls`, `xlsx`等。

-   `readtext()`

#### 数据读写实例

-   以读取`Excel`文件为例演示，其它类型的数据文件，换成其它读取函数即可。

#### 读入单个`Excel`文件

``` r
library(tidyverse)
library(readxl)
```

``` r
dat <- read_xlsx("C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2010.xlsx")
glimpse(dat)
```

    ## Rows: 7,648
    ## Columns: 32
    ## $ code                                        <chr> "000001.XSHE", "000002....
    ## $ statDate                                    <chr> "2010-03-31", "2010-03-...
    ## $ eps                                         <dbl> 0.5082, 0.1025, -0.0010...
    ## $ adjusted_profit                             <dbl> 1576599000.00, 10780907...
    ## $ operating_profit                            <dbl> 1867589000.00, 14751672...
    ## $ value_change_profit                         <dbl> 127562000.00, 66781674....
    ## $ roe                                         <dbl> 7.41, 2.95, -0.13, -0.9...
    ## $ inc_return                                  <dbl> 7.41, 2.82, NA, NA, 0.7...
    ## $ net_profit_margin                           <dbl> 38.64, 15.67, 5.86, -62...
    ## $ gross_profit_margin                         <dbl> NA, 36.72, 45.90, 46.35...
> 注： 函数`read_xlsx()`还有参数`sheet`,
> `range`设置读取的工作表和单元格范围；参数
> `col_name`设置是否将第1行设为列名；参数`col_type`为每列设置类型；参数`skip`设
> 置跳过的行数。

> 注意：`readr`包试着猜测每列的数据类型（数值型、字符型等），但属于保守猜测，
> 以避免丢失信息。本例中，`date`列为字符型，因为它没有认出日期格式，`individuals`
> 列为`double`型。通常更建议随后再用`dplyr`包修改数据类型。

#### 批量读取`Excel`文件

-   批量读取的数据文件往往具有相同的列结构，读入后紧接着需要按行合并为一个
    数据框，借助强大的`purrr`包，这可以一步完成。

-   首先要得到要导入的全部`Excel`文件的完整路径，可以任意嵌套，只需将参数
    `recursive`设为`TRUE`：

``` r
files<- list.files("C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载", pattern="xlsx", full.names=TRUE, recursive = TRUE)
files
```

    ##  [1] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2010.xlsx"  
    ##  [2] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2011.xlsx"  
    ##  [3] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2012.xlsx"  
    ##  [4] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2013.xlsx"  
    ##  [5] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2014.xlsx"  
    ##  [6] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2015.xlsx"  
    ##  [7] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2016.xlsx"  
    ##  [8] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2017.xlsx"  
    ##  [9] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2018.xlsx"  
    ## [10] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2019.xlsx"  
    ## [11] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2010.xlsx"
    ## [12] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2011.xlsx"
    ## [13] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2012.xlsx"
    ## [14] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2013.xlsx"
    ## [15] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2014.xlsx"
    ## [16] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2015.xlsx"
    ## [17] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2016.xlsx"
    ## [18] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2017.xlsx"
    ## [19] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2018.xlsx"
    ## [20] "C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽资产负债表2019.xlsx"

-   接着，在这些路径上做迭代，应用`read_xlsx()`到每个文件路径，再按行合并。
-   可以用`apply`函数族，但更建议用`purrr`包，其用户接口更容易记，并返回可预
    见的数据类型，还可用于管道操作。
-   这里用`map_dfr()`函数：

<!-- -->

    map_dfr(.x, .f, .id = "id_col")

它映射函数`.f`到列表或向量`.x`的每个元素，再将每个结果做 “行合并”
创建数据 框，并增加新列`id_col`描述该行来自哪个文件。

``` r
library(purrr)
datas <- map_dfr(set_names(files), read_xlsx, .id="source_file") 
# names 用于.id
glimpse(datas)
```

    ## Rows: 245,611
    ## Columns: 114
    ## $ source_file                                 <chr> "C:/Mirror/JutyperNoteb...
    ## $ code                                        <chr> "000001.XSHE", "000002....
    ## $ statDate                                    <chr> "2010-03-31", "2010-03-...
    ## $ eps                                         <dbl> 0.5082, 0.1025, -0.0010...
    ## $ adjusted_profit                             <dbl> 1576599000.00, 10780907...
    ## $ operating_profit                            <dbl> 1867589000.00, 14751672...
    ## $ value_change_profit                         <dbl> 127562000.00, 66781674....
  

-   也可以添加 read\_xlsx() 的其它控制读取的参数，例如

``` r
datas <- map_dfr(set_names(files), 
 read_xlsx,
 col_names = FALSE, 
 .id = "source_file")
```

    ## New names:
    ## * `` -> ...1
    ## * `` -> ...2
    ## * `` -> ...3
    ## * `` -> ...4
    ## * `` -> ...5
    ## * ...
    

> 注意：若数据表的列具有不同的数据类型。例如，某表 “出勤/缺席”
> 编码为1/0, 而另 一个表编码为TRUE/FALSE. 则这样的列不能做
> “行合并”，因为 R 不知道如何转化类
> 型。此时，可以通过设置`col_types`都以 “text”
> 类型导入所有列，后续再处理类型 转化。

``` r
datas <- map_dfr(set_names(files), 
 read_xlsx, 
 col_types = "text",
 .id = "source_file") 
glimpse(datas)
```

    ## Rows: 245,611
    ## Columns: 114
    ## $ source_file                                 <chr> "C:/Mirror/JutyperNoteb...
    ## $ code                                        <chr> "000001.XSHE", "000002....
    ## $ statDate                                    <chr> "2010-03-31", "2010-03-...
    ## $ eps                                         <chr> "0.5082", "0.1025", "-0...
    ## $ adjusted_profit                             <chr> "1576599000", "10780907...
    ## $ operating_profit                            <chr> "1867589000", "14751672...
    ## $ value_change_profit                         <chr> "127562000", "66781674....
    ## $ roe                                         <chr> "7.41", "2.95", "-0.13"...
    ## $ inc_return                                  <chr> "7.41", "2.82", NA, NA,...
    ## $ net_profit_margin                           <chr> "38.64", "15.67", "5.86...
    ## $ gross_profit_margin                         <chr> NA, "36.72", "45.9", "4...
    ## $ expense_to_total_revenue                    <chr> NA, "80.34", "90.28", "...
    ## $ operation_profit_to_total_revenue           <chr> "48.85", "20.55", "9.34...
   

### 保存数据

-   用`readr`包中的`write_csv()`和`write_rds()`，或`writexl`包中的`write_xlsx()`可以保存数据框到文件。
-   下面以导出数据到`.rds`文件为例，因为它能保存数据框及其元数据，如数据类型和分组（若有的话）。

``` r
iris %>% 
 write_rds("my_iris.rds")
dat <- read_rds("my_iris.rds") # 导入.rds数据
dim(dat)
```

    ## [1] 150   5

## 数据连接

### 数据连接简介

-   数据分析经常会涉及多个数据表，也称为关系数据库。关系是指两个数据表之间
    的关系，更多数据表之间的关系总可以表示为两两之间的关系。

-   一个项目的数据，通常都是用若干数据表分别存放，它们之间通过 “键”
    连接在一 起，根据数据分析的需要，通过键匹配进行数据连接。

-   例如，机场航班数据的连接结构：

![数据连接](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203427043437.png)

-   键列（可以不止1列），能够唯一识别自己或别人数据表的每一个观测。要判断
    某（些）列是否是键，可以先用·count()·计数，再看是否没有n &gt;
    1出现：

``` r
Finance2019 <- read_xlsx("C:/Mirror/JutyperNotebook/Python练习/聚宽数据下载/聚宽财务指标2010.xlsx")
Finance2019 %>%
  as_tibble()%>%
  count(code,statDate) %>%
  filter(n > 1)
```

    ## # A tibble: 0 x 3
    ## # ... with 3 variables: code <chr>, statDate <chr>, n <int>

### 合并行与合并列

-   合并数据框最基本的方法是：
    -   合并行：下方堆叠新行，根据列名匹配列，注意列名相同，否则作为新列（NA填充）；  
    -   合并列：右侧拼接新列，根据位置匹配行，行数必须相同。
-   分别用 dplyr 包中的 bind\_rows() 和 bind\_cols() 实现。

``` r
bind_rows(
 sample_n(iris, 2),
 sample_n(iris, 2),
 sample_n(iris, 2) )
```

    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
    ## 1          7.7         3.0          6.1         2.3 virginica
    ## 2          5.7         2.5          5.0         2.0 virginica
    ## 3          4.9         3.1          1.5         0.2    setosa
    ## 4          5.8         2.7          5.1         1.9 virginica
    ## 5          6.2         2.8          4.8         1.8 virginica
    ## 6          4.8         3.4          1.9         0.2    setosa

``` r
one <- mtcars[1:4, 1:3]
two <- mtcars[1:4, 4:5]
bind_cols(one, two)
```

    ##                 mpg cyl disp  hp drat
    ## Mazda RX4      21.0   6  160 110 3.90
    ## Mazda RX4 Wag  21.0   6  160 110 3.90
    ## Datsun 710     22.8   4  108  93 3.85
    ## Hornet 4 Drive 21.4   6  258 110 3.08

-   有时候，是在批量导入数据的同时做合并行/合并列，配合`purrr`包中`map_dfr()`和`map_dfc()`使用实现：应用函数`.f`到列表或向量`.x`的每个元，再将每个结果做“行合并/合并列”
    创建数据框，.id 可增加新列 id\_col 描述该行来自哪个文件。

<!-- -->

    map_dfr(.x, .f, .id = "id_col")
    map_dfc(.x, .f)

### 根据值匹配合并数据框

-   只介绍最常用的六种合并：左连接、右连接、全连接、内连接、半连接、反连接，前四种连接又称为修改连接，后两种连接又称为过滤连接。

``` r
band = tibble(name = c("Mick", "John", "Paul"),
 band = c("Stones", "Beatles", "Beatles"))
band
```

    ## # A tibble: 3 x 2
    ##   name  band   
    ##   <chr> <chr>  
    ## 1 Mick  Stones 
    ## 2 John  Beatles
    ## 3 Paul  Beatles

``` r
instrument = tibble(name = c("John", "Paul", "Keith"), 
 plays = c("guitar", "bass", "guitar"))
instrument
```

    ## # A tibble: 3 x 2
    ##   name  plays 
    ##   <chr> <chr> 
    ## 1 John  guitar
    ## 2 Paul  bass  
    ## 3 Keith guitar

#### 左连接：left\_join()

-   外连接至少保留一个数据表中的所有观测，分为左连接、右连接、全连接，其中最常用的是左连接：保留
    x 所有行，合并匹配的y中的列。

``` r
band %>% 
 left_join(instrument, by = "name")
```

    ## # A tibble: 3 x 3
    ##   name  band    plays 
    ##   <chr> <chr>   <chr> 
    ## 1 Mick  Stones  <NA>  
    ## 2 John  Beatles guitar
    ## 3 Paul  Beatles bass

![左连接](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203527043508.png)

> 注： 若两个表中的键列列名不同，用 by = c(“name1” = “name2”)。

#### 右连接：right\_join()

-   保留y所有行，合并匹配的x中的列。

``` r
band %>% 
 right_join(instrument, by = "name")
```

    ## # A tibble: 3 x 3
    ##   name  band    plays 
    ##   <chr> <chr>   <chr> 
    ## 1 John  Beatles guitar
    ## 2 Paul  Beatles bass  
    ## 3 Keith <NA>    guitar

![右连接](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203527043552.png)

#### 全连接：full\_join()

-   保留`x`和`y`中的所有行，合并匹配的列。

``` r
band %>% 
 full_join(instrument, by = "name")
```

    ## # A tibble: 4 x 3
    ##   name  band    plays 
    ##   <chr> <chr>   <chr> 
    ## 1 Mick  Stones  <NA>  
    ## 2 John  Beatles guitar
    ## 3 Paul  Beatles bass  
    ## 4 Keith <NA>    guitar

![全连接](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203627043617.png)
\#\#\#\# 内连接：inner\_join()

-   内连接是保留两个数据表中所共有的观测：只保留x中与y匹配的行，合并匹配
    的y中的列。

``` r
band %>% 
 inner_join(instrument, by = "name")
```

    ## # A tibble: 2 x 3
    ##   name  band    plays 
    ##   <chr> <chr>   <chr> 
    ## 1 John  Beatles guitar
    ## 2 Paul  Beatles bass

![内连接](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203627043639.png)

#### 半连接：semi\_join()

根据在y中，来筛选x中的行。

``` r
band %>% 
 semi_join(instrument, by = "name")
```

    ## # A tibble: 2 x 2
    ##   name  band   
    ##   <chr> <chr>  
    ## 1 John  Beatles
    ## 2 Paul  Beatles

![半连接](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203727043704.png)

#### 反连接：anti\_join()

-   根据不在 y 中，来筛选 x 中的行。

``` r
band %>% 
 anti_join(instrument, by = "name")
```

    ## # A tibble: 1 x 2
    ##   name  band  
    ##   <chr> <chr> 
    ## 1 Mick  Stones

![反连接](https://gitee.com/shao818/Figure/raw/master/null/%E6%88%AA%E5%9B%BE_20203727043724.png)

### 集合运算

-   集合运算有时候很有用，都是针对所有行，通过比较变量的值来实现。这就需要
    数据表x和y具有相同的变量，并将观测看成是集合中的元素：

``` r
x<-c("a1","a2","c1")
y<-c("a1","a2","b1")
intersect(x, y) # 返回 x 和 y 共同包含的观测；
```

    ## [1] "a1" "a2"

``` r
union(x, y) # 返回 x 和 y 中所有的（唯一）观测；
```

    ## [1] "a1" "a2" "c1" "b1"

``` r
setdiff(x, y) # 返回在 x 中但不在 y 中的观测。
```

    ## [1] "c1"
    
</font>