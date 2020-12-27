---
title: R数据科学Ⅲ：异质数据类型与因子型数据
author: 王芍
date: '2020-12-25'
slug: []
categories:
  - R
  - 数据科学
tags:
  - R
  - 数据科学
  - 数据类型
  - 异质数据
  - 因子型数据
lastmod: '2020-12-25T15:56:52+08:00'
keywords: []
description: ''
comment: no
toc: true
weight: 103
---
{{% center %}} 
![](https://gitee.com/shao818/Figure/raw/master/null/20201227173200.png)
{{% /center %}}


<!--more-->

<div align=center> 
<font size=5 color=#C46657 face="仿宋">目录</font>
</div>

-------------------------------------------------------------------------------

<font face="仿宋"  size=2>

-   [列表（list）](#列表list)
    -   [创建列表](#创建列表)
    -   [从列表中提取成分下的元素](#从列表中提取成分下的元素)
    -   [构建列表子集](#构建列表子集)
    -   [对列表的成分赋值](#对列表的成分赋值)
    -   [列表函数](#列表函数)
-   [数据框（数据表）](#数据框数据表)
    -   [创建数据框](#创建数据框)
    -   [提取数据框的元素、子集](#提取数据框的元素子集)
    -   [给数据框赋值](#给数据框赋值)
-   [因子（factor）](#因子factor)
    -   [创建因子](#创建因子)
    -   [使用因子](#使用因子)
    -   [有用函数](#有用函数)    
    
-------------------------------------------------------------------------------

<font size=1  face="仿宋">


## 列表（list）

列表，可以包含不同类型的对象，甚至可以包括其他列表。列表的灵活性使得它
非常有用。

例如，用 R 拟合一个线性回归模型，其返回结果就是一个列表，其中包含了线性
回归的详细结果，如线性回归系数（数值向量）、残差（数值向量）、QR 分解
（包含一个矩阵和其他对象的列表）等。因为这些结果全都被打包到一个列表
中，就可以很方便地提取所需信息，而无需每次调用不同的函数。

### 创建列表

可以用函数`list()`创建列表。不同类型的对象可以被装进同一个列表中。
例如，创建了一个列表，包含 3 个成分：一个单元素的数值向量、一个两元素的
逻辑向量和一个长度为 3 的字符向量：

``` r
l0 <- list(1, c(TRUE, FALSE), c("a", "b", "c"))
l0
```

    ## [[1]]
    ## [1] 1
    ## 
    ## [[2]]
    ## [1]  TRUE FALSE
    ## 
    ## [[3]]
    ## [1] "a" "b" "c"

可以在创建列表时，为列表的每个成分指定名称：

``` r
l1 <- list(x = 1, y = c(TRUE, FALSE), z = c("a", "b", "c"))
l1
```

    ## $x
    ## [1] 1
    ## 
    ## $y
    ## [1]  TRUE FALSE
    ## 
    ## $z
    ## [1] "a" "b" "c"

也可以创建列表后再对列表成分命名或修改命名：

``` r
names(l1) <- NULL # 移除列表成分的名字
names(l1) <- c("A","B","C")
l1
```

    ## $A
    ## [1] 1
    ## 
    ## $B
    ## [1]  TRUE FALSE
    ## 
    ## $C
    ## [1] "a" "b" "c"

### 从列表中提取成分下的元素

提取列表中成分下的元素，最常用的方法是用`$`，通过成分名称来提取该成分下
的元素（内容）：

``` r
l1$B
```

    ## [1]  TRUE FALSE

``` r
l1$m # 访问不存在的成分m, 将会返回NULL
```

    ## NULL

也可以用 \[\[n\]\] 来提取列表第 n 个成分的元素，n 也可以换成成分的名字：

``` r
l1[[2]] # 同l1[["B"]]
```

    ## [1]  TRUE FALSE

使用 \[\[\]\]
提取列表中某个成分的元素更加灵活，因为可以用变量传递想要提取其
元素的成分名字（用于函数调用)：

``` r
member <- "B" #想要取出的成分名字
l1[[member]]
```

    ## [1]  TRUE FALSE

### 构建列表子集

经常也需要从列表中提取多个成分的元素，由这些成分组成的列表构成了原列表
的一个子集。

就像构建向量和矩阵的子集一样，构建一个列表子集是用`[]`，可以取出列表中的
一些成分，然后放到一个新的列表中。  
`[]`中可以用字符向量表示成分名称，用数值向量表示成分位置，或用逻辑向量指
定是否选择，来取出列表成分。

``` r
l1["A"] # 同l1[1]
```

    ## $A
    ## [1] 1

``` r
l1[c("A", "B")] # 同l1[c(1, 2)]
```

    ## $A
    ## [1] 1
    ## 
    ## $B
    ## [1]  TRUE FALSE

``` r
l1[c(TRUE, FALSE, TRUE)]
```

    ## $A
    ## [1] 1
    ## 
    ## $C
    ## [1] "a" "b" "c"

注意： 用`[]`提取若干成分时，返回列表的子集，还是一个列表；用`[[ ]]`
提取单个成分的元素，返回的是对应成分的元素。总之，`[]`提取对象的子集，类型仍是该对象；\[\[\]\]
提取对象的内容（下一级元素）。

### 对列表的成分赋值

``` r
l1 <- list(x = 1, y = c(TRUE, FALSE), z = c("a", "b", "c"))
l1$x <- 0 ;l1# 将列表的成分x赋值为0;
```

    ## $x
    ## [1] 0
    ## 
    ## $y
    ## [1]  TRUE FALSE
    ## 
    ## $z
    ## [1] "a" "b" "c"

``` r
l1$m <- 4
l1
```

    ## $x
    ## [1] 0
    ## 
    ## $y
    ## [1]  TRUE FALSE
    ## 
    ## $z
    ## [1] "a" "b" "c"
    ## 
    ## $m
    ## [1] 4

也可以同时给多个列表成分赋值：

``` r
l1[c("y", "z")] <- list(y = "new value for y", z = c(1, 2))
l1
```

    ## $x
    ## [1] 0
    ## 
    ## $y
    ## [1] "new value for y"
    ## 
    ## $z
    ## [1] 1 2
    ## 
    ## $m
    ## [1] 4

若要移除列表中的某些成分，只需赋值为 NULL：

``` r
l1[c("z", "m")] <- NULL
l1
```

    ## $x
    ## [1] 0
    ## 
    ## $y
    ## [1] "new value for y"

### 列表函数

用函数 `as.list()` 可将向量转换成列表：

``` r
l3 <- as.list(c(a = 1, b = 2, c = 3))
l3
```

    ## $a
    ## [1] 1
    ## 
    ## $b
    ## [1] 2
    ## 
    ## $c
    ## [1] 3

用函数`unlist()`，可将一个列表强制转换成一个向量。该函数基本上对所有列表
成分进行转换，并把它们存储在一个类型兼容的向量中：

``` r
unlist(l3)
```

    ## a b c 
    ## 1 2 3

若对一个混合了数值和文本的列表进行去列表化（`unlist`），则每个成分都会被
转换为其所能转换成的最近类型（`closest type`）：

``` r
l4 <- list(a = 1, b = 2, c = "hello")
unlist(l4)
```

    ##       a       b       c 
    ##     "1"     "2" "hello"

注： 这里的`l4$a` 和`l4$b`都是数值，可以被转换为字符向量；但是，`l4$c`
是字符向量，无法被转换为数值。因此，能够兼容这些元素的最近类型就是字符向量。

函数 summary() 可以查看列表的汇总信息：

``` r
summary(l0)
```

    ##      Length Class  Mode     
    ## [1,] 1      -none- numeric  
    ## [2,] 2      -none- logical  
    ## [3,] 3      -none- character

## 数据框（数据表）

R语言中做统计分析的样本数据，都是按数据框类型操作的。

数据框是指有若干行和列的数据集，它与矩阵类似，但并不要求所有列都是相同
的类型；本质上讲，数据框就是一个列表，它的每个成分都是一个向量，并且长
度相同，以表格的形式展现。总之，数据框是由列向量组成、有着矩阵形式的列
表。

数据框与最常见的数据表是一致的：每一列代表一个变量属性，每一行代表一条
样本数据：

R 中自带的数据框是 `data.frame`，建议改用更现代的数据框：`tibble` Hadley
在 `tibble` 包中引入一种 `tibble` 数据框，以代替 `data.frame`；而且
`tidyverse` 包都是基于 `tibble` 数据框。 `tibble` 比 `data.frame`
的优势： - `tibble()` 比 `data.frame()`
做的更少：不改变输入变量的类型（不会默认将字
符串转化为因子！），不会改变变量名，不会创建行名； - `tibble`
对象的列名可以是 `R` 中的“非法名”：非字母开头、包含空格，但定义
和使用变量时都需要用倒引号
`括起来； -`tibble`在输出时不自动显示所有行，避免大数据框时显示很多内容； 用`\[\]`选取列子集时，即使仅取一列，返回结果仍是`tibble\`，而不是向量。

### 创建数据框

#### 用 `tibble()` 根据若干列向量创建 `tibble`

``` r
library(tibble)
persons <- tibble(
 Name = c("Ken", "Ashley", "Jennifer"),
 Gender = c("Male", "Female", "Female"),
 Age = c(24, 25, 23),
 Major = c("Finance", "Statistics", "Computer Science"))
persons
```

    ## # A tibble: 3 x 4
    ##   Name     Gender   Age Major           
    ##   <chr>    <chr>  <dbl> <chr>           
    ## 1 Ken      Male      24 Finance         
    ## 2 Ashley   Female    25 Statistics      
    ## 3 Jennifer Female    23 Computer Science

#### 用 `tribble()`按行录入数据式创建 `tibble`

``` r
tribble(
 ~Name, ~Gender, ~Age, ~Major,
 "Ken", "Male", 24, "Finance",
 "Ashley", "Female", 25, "Statistics",
 "Jennifer", "Female", 23, "Computer Science"
)
```

    ## # A tibble: 3 x 4
    ##   Name     Gender   Age Major           
    ##   <chr>    <chr>  <dbl> <chr>           
    ## 1 Ken      Male      24 Finance         
    ## 2 Ashley   Female    25 Statistics      
    ## 3 Jennifer Female    23 Computer Science

#### 用 `as_tibble()` 将 `data.frame`, `list`, `matrix` 转换为 `tibble`

``` r
as_tibble(iris)
```

    ## # A tibble: 150 x 5
    ##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ##           <dbl>       <dbl>        <dbl>       <dbl> <fct>  
    ##  1          5.1         3.5          1.4         0.2 setosa 
    ##  2          4.9         3            1.4         0.2 setosa 
    ##  3          4.7         3.2          1.3         0.2 setosa 
    ##  4          4.6         3.1          1.5         0.2 setosa 
    ##  5          5           3.6          1.4         0.2 setosa 
    ##  6          5.4         3.9          1.7         0.4 setosa 
    ##  7          4.6         3.4          1.4         0.3 setosa 
    ##  8          5           3.4          1.5         0.2 setosa 
    ##  9          4.4         2.9          1.4         0.2 setosa 
    ## 10          4.9         3.1          1.5         0.1 setosa 
    ## # ... with 140 more rows

``` r
l1 <- list(x = c(1, 2, 3), y = c("a", "b", "c"))
as_tibble(l1)
```

    ## # A tibble: 3 x 2
    ##       x y    
    ##   <dbl> <chr>
    ## 1     1 a    
    ## 2     2 b    
    ## 3     3 c

``` r
m1 <- matrix(c(1, 2, 3, 4, 5, 6, 7, 8, 9), nrow = 3)
as_tibble(m1)
```

    ## Warning: The `x` argument of `as_tibble.matrix()` must have unique column names if `.name_repair` is omitted as of tibble 2.0.0.
    ## Using compatibility `.name_repair`.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_warnings()` to see where this warning was generated.
    
    ## # A tibble: 3 x 3
    ##      V1    V2    V3
    ##   <dbl> <dbl> <dbl>
    ## 1     1     4     7
    ## 2     2     5     8
    ## 3     3     6     9

数据框既是列表的特例，也是矩阵的推广，因此访问这两类对象的方式都适用于
数据框。例如与矩阵类似，对数据框的行列重新命名：

``` r
df1 <- tibble(id = 1:4, x = c(0, 2, 1, -1), 
 y = c(0.5, 0.2, 0.1, 0.5))
colnames(df1) <- c("id", "level", "score")
df1
```

    ## # A tibble: 4 x 3
    ##      id level score
    ##   <int> <dbl> <dbl>
    ## 1     1     0   0.5
    ## 2     2     2   0.2
    ## 3     3     1   0.1
    ## 4     4    -1   0.5

另外，可用 `names()` 查看或修改数据框的列名

``` r
names(df1) <- c("x", "y", "z")
df1
```

    ## # A tibble: 4 x 3
    ##       x     y     z
    ##   <int> <dbl> <dbl>
    ## 1     1     0   0.5
    ## 2     2     2   0.2
    ## 3     3     1   0.1
    ## 4     4    -1   0.5

### 提取数据框的元素、子集

数据框是由列向量组成、有着矩阵形式的列表，所以可以用两种操作方式来访问
数据框的元素和子集。

#### 以列表方式提取数据框的元素、子集

若把数据框看作是由向量组成的列表，则可以沿用列表的操作方式来提取元素或
构建子集。例如，可以用 `$` 按列名来提取某一列的值，或者用 `[[]]`
按照位置提 取。

``` r
df1$x # 同df1[[1]]
```

    ## [1] 1 2 3 4

以列表形式构建子集完全适用于数据框，同时也会生成一个新的数据框。提取子
集的操作符`[]`允许用数值向量表示列的位置，用字符向量表示列名，或用逻辑向
量指定是否选择。

``` r
df1[1] # 同df1["x"]
```

    ## # A tibble: 4 x 1
    ##       x
    ##   <int>
    ## 1     1
    ## 2     2
    ## 3     3
    ## 4     4

``` r
df1[1:2] # 同df1[c("x", "y")]
```

    ## # A tibble: 4 x 2
    ##       x     y
    ##   <int> <dbl>
    ## 1     1     0
    ## 2     2     2
    ## 3     3     1
    ## 4     4    -1

``` r
df1[c(TRUE, FALSE, TRUE)]
```

    ## # A tibble: 4 x 2
    ##       x     z
    ##   <int> <dbl>
    ## 1     1   0.5
    ## 2     2   0.2
    ## 3     3   0.1
    ## 4     4   0.5

#### 以矩阵方式提取数据框的元素、子集

以列表形式操作并不支持行选择。以矩阵形式操作更加灵活，若将数据框看作矩
阵，其二维形式的存取器可以很容易地获取一个子集的元素，同时支持列选择和
行选择。
换句话说，可以使用`[i, j]`指定行或列来提取数据框子集，`[ , ]`内可以是数值向
量、字符向量或者逻辑向量。

选择列：

``` r
df1[, "x"]
```

    ## # A tibble: 4 x 1
    ##       x
    ##   <int>
    ## 1     1
    ## 2     2
    ## 3     3
    ## 4     4

``` r
df1[, c("x", "y")] # 同df1[, 1:2]
```

    ## # A tibble: 4 x 2
    ##       x     y
    ##   <int> <dbl>
    ## 1     1     0
    ## 2     2     2
    ## 3     3     1
    ## 4     4    -1

选择行：

``` r
df1[1:3,]
```

    ## # A tibble: 3 x 3
    ##       x     y     z
    ##   <int> <dbl> <dbl>
    ## 1     1     0   0.5
    ## 2     2     2   0.2
    ## 3     3     1   0.1

``` r
df1[c("c", "a"),]
```

    ## Warning: The `i` argument of ``[.tbl_df`()` must use valid row names as of tibble 3.0.0.
    ## Use `NA_integer_` as row index to obtain a row full of `NA` values.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_warnings()` to see where this warning was generated.
    
    ## # A tibble: 2 x 3
    ##       x     y     z
    ##   <int> <dbl> <dbl>
    ## 1    NA    NA    NA
    ## 2    NA    NA    NA

同时选择行和列：

``` r
df1[1:3, "y"]
```

    ## # A tibble: 3 x 1
    ##       y
    ##   <dbl>
    ## 1     0
    ## 2     2
    ## 3     1

``` r
df1[1:3, c("x", "z")]
```

    ## # A tibble: 3 x 2
    ##       x     z
    ##   <int> <dbl>
    ## 1     1   0.5
    ## 2     2   0.2
    ## 3     3   0.1

根据条件筛选数据 例如用 z &gt;= 0.5 筛选 df1 的行，并选择 x 和 y 两列：

``` r
df1$z >= 0.5
```

    ## [1]  TRUE FALSE FALSE  TRUE

``` r
df1[df1$z >= 0.5, c("x", "y")]
```

    ## # A tibble: 2 x 2
    ##       x     y
    ##   <int> <dbl>
    ## 1     1     0
    ## 2     4    -1

按行名属于集合 {a, d, e} 来筛选 df1 的行，并选择 x 和 y 两列：

``` r
ind <- names(df1) %in% c("x", "z", "w")
df1[1:2, ind]
```

    ## # A tibble: 2 x 2
    ##       x     z
    ##   <int> <dbl>
    ## 1     1   0.5
    ## 2     2   0.2

### 给数据框赋值

给数据框赋值，就是选择要赋值的位置，再准备好同样大小且格式匹配的数据，
赋值给那些位置即可，所以同样有列表方式和矩阵方式。 \#\#\#\#
以列表方式给数据框赋值 用 `$` 或 `[[ ]]` 对数据框的 1 列赋值

``` r
df1$z <- c(0.6, 0.3, 0.2, 0.4) 
# 同d1[["z"]] <- c(0.4, 0.5, 0.2, 0.8)
df1
```

    ## # A tibble: 4 x 3
    ##       x     y     z
    ##   <int> <dbl> <dbl>
    ## 1     1     0   0.6
    ## 2     2     2   0.3
    ## 3     3     1   0.2
    ## 4     4    -1   0.4

利用现有列，创建 (计算) 新列：

``` r
df1$w <- df1$y + df1$z 
df1
```

    ## # A tibble: 4 x 4
    ##       x     y     z     w
    ##   <int> <dbl> <dbl> <dbl>
    ## 1     1     0   0.6   0.6
    ## 2     2     2   0.3   2.3
    ## 3     3     1   0.2   1.2
    ## 4     4    -1   0.4  -0.6

``` r
df1$w <- as.character(df1$w) # 转换列的类型
df1
```

    ## # A tibble: 4 x 4
    ##       x     y     z w    
    ##   <int> <dbl> <dbl> <chr>
    ## 1     1     0   0.6 0.6  
    ## 2     2     2   0.3 2.3  
    ## 3     3     1   0.2 1.2  
    ## 4     4    -1   0.4 -0.6

用 `[]` 可以对数据框的 1 列或多列进行赋值：

``` r
df1["z"] <- c(0.8, 0.5, 0.2, 0.4)
df1
```

    ## # A tibble: 4 x 4
    ##       x     y     z w    
    ##   <int> <dbl> <dbl> <chr>
    ## 1     1     0   0.8 0.6  
    ## 2     2     2   0.5 2.3  
    ## 3     3     1   0.2 1.2  
    ## 4     4    -1   0.4 -0.6

``` r
df1[c("y", "z")] <- list(level = c(1, 2, 1, 0), 
 score = c(0.1, 0.2, 0.3, 0.4))
df1
```

    ## # A tibble: 4 x 4
    ##       x     y     z w    
    ##   <int> <dbl> <dbl> <chr>
    ## 1     1     1   0.1 0.6  
    ## 2     2     2   0.2 2.3  
    ## 3     3     1   0.3 1.2  
    ## 4     4     0   0.4 -0.6

#### 以矩阵方式给数据框赋值

以列表方式对数据框进行赋值时，也是只能访问列。若需要更加灵活地进行赋值
操作，可以以矩阵方式进行。

``` r
df1[1:3, "y"] <- c(-1, 0, 1)
df1
```

    ## # A tibble: 4 x 4
    ##       x     y     z w    
    ##   <int> <dbl> <dbl> <chr>
    ## 1     1    -1   0.1 0.6  
    ## 2     2     0   0.2 2.3  
    ## 3     3     1   0.3 1.2  
    ## 4     4     0   0.4 -0.6

``` r
df1[1:2, c("y", "z")] <- list(level = c(0, 0), 
 score = c(0.9, 1.0))
df1
```

    ## # A tibble: 4 x 4
    ##       x     y     z w    
    ##   <int> <dbl> <dbl> <chr>
    ## 1     1     0   0.9 0.6  
    ## 2     2     0   1   2.3  
    ## 3     3     1   0.3 1.2  
    ## 4     4     0   0.4 -0.6

\#\#\#一些有用函数 \#\#\#\# `str()` 该函数作用在 R
对象上，显示该对象的结构：

``` r
str(persons)
```

    ## tibble [3 x 4] (S3: tbl_df/tbl/data.frame)
    ##  $ Name  : chr [1:3] "Ken" "Ashley" "Jennifer"
    ##  $ Gender: chr [1:3] "Male" "Female" "Female"
    ##  $ Age   : num [1:3] 24 25 23
    ##  $ Major : chr [1:3] "Finance" "Statistics" "Computer Science"

#### `summary()`

该函数作用在数据框上，将生成一个汇总表来显示每一列的情况：

``` r
summary(persons)
```

    ##      Name              Gender               Age          Major          
    ##  Length:3           Length:3           Min.   :23.0   Length:3          
    ##  Class :character   Class :character   1st Qu.:23.5   Class :character  
    ##  Mode  :character   Mode  :character   Median :24.0   Mode  :character  
    ##                                        Mean   :24.0                     
    ##                                        3rd Qu.:24.5                     
    ##                                        Max.   :25.0

#### `rbind()` 和 `cbind()`

经常需要将多个数据框（或矩阵）按行或按列进行合并 用函数
`rbind()`，增加行（样本数据），要求宽度（列数）相同； 用函数
`cbind()`，增加列（属性变量），要求高度（行数）相同。 例如，向数据框
persons 数据框中添加一个人的新记录：

``` r
rbind(persons, tibble(Name="John", Gender="Male", 
 Age=25, Major="Statistics"))
```

    ## # A tibble: 4 x 4
    ##   Name     Gender   Age Major           
    ##   <chr>    <chr>  <dbl> <chr>           
    ## 1 Ken      Male      24 Finance         
    ## 2 Ashley   Female    25 Statistics      
    ## 3 Jennifer Female    23 Computer Science
    ## 4 John     Male      25 Statistics

向 persons 数据框中添加两个新列表示每个人是否已注册和其手头的项目数量：

``` r
cbind(persons, Registered=c(TRUE, TRUE, FALSE), 
 Projects=c(3, 2, 3))
```

    ##       Name Gender Age            Major Registered Projects
    ## 1      Ken   Male  24          Finance       TRUE        3
    ## 2   Ashley Female  25       Statistics       TRUE        2
    ## 3 Jennifer Female  23 Computer Science      FALSE        3

注意： `rbind()` 和
`cbind()`不会修改原始数据，而是生成一个添加了行或列的新数据框。

> 更建议用 `dplyr`包中的`bind_rows()`和`bind_cols()`

#### `expand.grid()`

该函数可生成多个属性水平值所有组合（笛卡尔积）的数据框：

``` r
expand.grid(type=c("A", "B"), class=c("M", "L", "XL"))
```

    ##   type class
    ## 1    A     M
    ## 2    B     M
    ## 3    A     L
    ## 4    B     L
    ## 5    A    XL
    ## 6    B    XL

## 因子（factor）

变量分为离散/分类型、连续/数值型（通常的数值变量，可带小数位）；分类型
变量又分为：名义型（无顺序好坏之分的分类变量，如性别）、有序型（有顺序
好坏之分的分类变量，如疗效）。

名义型和有序型的分类变量，在 R 语言中称为因子，因子本质上是一个带有水平
（level）属性的整数向量，其中“水平”是指事前确定可能取值的有限集合。

因子提供了一个简单且紧凑的形式来处理分类数据，因子用水平来表示所有可能
的取值，例如，性别有两个水平：男、女。

### 创建因子

#### 用函数 factor() 创建因子，其基本格式为：

    factor(x, levels, labels, exclude, ordered, nmax, ...)
    其中，
    x：为创建因子的数据向量；
    levels：指定因子的各水平值，默认为 x 中不重复的所有值；
    labels：设置各水平名称（前缀），与水平一一对应；
    exclude：指定有哪些水平是不需要的（设为NA）；
    ordered：设置是否对因子水平排序，默认 FALSE 为无序因子, TRUE 为有序因子；
    nmax：设定水平数的上限。

``` r
x <- c("男", "女", "男", "男", "女")
sex <- factor(x)
sex
```

    ## [1] 男 女 男 男 女
    ## Levels: 男 女

``` r
attributes(sex)
```

    ## $levels
    ## [1] "男" "女"
    ## 
    ## $class
    ## [1] "factor"

``` r
levels(sex) # 访问水平值属性
```

    ## [1] "男" "女"

``` r
class(sex) 
```

    ## [1] "factor"

#### 用 gl() 生成有规律的水平值组合因子

对于多因素试验设计，用该函数可以生成多个因素完全组合，基本格式为：

    gl(n, k, length=n*k, labels, ordered=FALSE)
    其中，
    n：为因子水平个数；
    k：为同一因子水平连续重复次数；
    length：为总的元素个数，默认为 n * k, 若不够则自动重复；
    labels：设置因子水平值；
    ordered：设置是否为有序。

``` r
data.frame(
 Sex = gl(2, 3, length=12, labels=c("男", "女")),
 Class = gl(3, 2, length=12, labels=c("甲", "乙", "丙")),
 Score = gl(4, 3, length=12, labels=c("优", "良", "中", "及格")))
```

    ##    Sex Class Score
    ## 1   男    甲    优
    ## 2   男    甲    优
    ## 3   男    乙    优
    ## 4   女    乙    良
    ## 5   女    丙    良
    ## 6   女    丙    良
    ## 7   男    甲    中
    ## 8   男    甲    中
    ## 9   男    乙    中
    ## 10  女    乙  及格
    ## 11  女    丙  及格
    ## 12  女    丙  及格

### 使用因子

R中因子是以整数型向量存储的，每个因子水平对应一个整数型的数。对字符型
向量创建的因子，可以指定因子水平顺序，否则默认会按照字母顺序，再对应到
整数型向量。  
&gt;注意： 不能直接将因子数据当字符型操作，需要用 as.character() 转化。

考虑用一个变量存储字符串型的月份：

``` r
x1 <- c("Dec", "Apr", "Jan", "Mar")
```

这有两个问题： - 实际只需要12个月份值，对拼写错误也无能为力； -
不会按照想要的方式排序：

``` r
sort(x1)
```

    ## [1] "Apr" "Dec" "Jan" "Mar"

若改用因子型就能避免上述问题。创建因子型，首先要创建一个有效的“水平值”
列表：

``` r
month_levels <- c(
"Jan", "Feb", "Mar", "Apr", "May", "Jun",
 "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
)
```

再来创建因子：

``` r
y1 <- factor(x1, levels = month_levels)
y1
```

    ## [1] Dec Apr Jan Mar
    ## Levels: Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec

``` r
sort(y1)
```

    ## [1] Jan Mar Apr Dec
    ## Levels: Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec

并且任何不在水平集中的值都将转化为 NA：

``` r
x2 <- c("Dec", "Apr", "Jam", "Mar")
y2 <- factor(x2, levels = month_levels)
y2
```

    ## [1] Dec  Apr  <NA> Mar 
    ## Levels: Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec

有时候你可能更希望让水平的顺序与其在数据集中首次出现的次序相匹配，设置
`levels=unique(x)` 即可，或者用管道操作在其后增加 `fct_inorder()`:

``` r
f1 <- factor(x1, levels = unique(x1))
f1
```

    ## [1] Dec Apr Jan Mar
    ## Levels: Dec Apr Jan Mar

### 有用函数

#### `table()`

该函数可以统计因子各水平的出现次数（频数），也可以统计向量中每个不同元
素的出现次数，返回结果为命名向量。

``` r
table(sex)
```

    ## sex
    ## 男 女 
    ##  3  2

#### `cut()`

该函数可以用来做连续变量离散化：将数值向量切分为若干区间段，返回因子。
基本格式为：

    cut(x, breaks, labels, ...)
    其中，x：为要切分的数值向量；
    breaks：切分的界限值构成的向量，或表示切分段数的整数

> 注： 该函数还有其它参数 `right` 设置区间段是否左开右闭,
> `include.lowest` 设置是否包含下界，`ordered_result`
> 设置是否对结果因子排序。

``` r
Age <- c(23, 15, 36, 47, 65, 53)
cut(Age, breaks=c(0, 18, 45, 100), 
 labels=c("Young", "Middle", "Old"))
```

    ## [1] Middle Young  Middle Old    Old    Old   
    ## Levels: Young Middle Old
</font>