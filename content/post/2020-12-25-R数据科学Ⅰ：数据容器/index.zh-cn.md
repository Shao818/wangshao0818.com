---
title: R数据科学Ⅰ：数据容器
author: 王芍
date: '2020-12-25'
slug: []
categories:
  - R
  - 数据科学
tags:
  - R
  - 数据科学
  - 数据容器
reward: no
mathjax: no
toc: true
weight: 101
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

  - [原子型向量](#原子型向量)
    - [双整型](#双整型)
    - [整型](#整型)
    - [字符型](#字符型)
    - [逻辑型](#逻辑型)
    - [复数类型](#复数类型)
    - [原始类型](#原始类型)
  - [向量类型转换](#向量类型转换)
    - [数值型转字符型](#数值型转字符型)
    - [字符型转数值型](#字符型转数值型)
    - [数值型转逻辑型](#数值型转逻辑型)
    - [逻辑型转数值型](#逻辑型转数值型)
  - [属性](#属性)
    - [名称](#名称)
    - [维度](#维度)
    - [类](#类)
  - [日期和时间](#日期和时间)
  - [因子](#因子)
-------------------------------------------------------------------------------


<font size=1  face="仿宋">

> <font face="仿宋"  size=2>　　R中的数据类型与存储方式以原子型向量为基础，通过改变其对应属性可以生成矩阵、列表和数据框等数据容器。下面将从原子型向量开始，揭示各个数据容器之间的内在联系，帮助大家更好地理解与使用R来存储与分析数据。</font>

# 原子型向量

原子型向量就是最简单的包含数据的向量，在R中以下均是原子型向量:

```r
x <- 1
y <- 1:6
z <- c(1,2,3,4,5,6)
x
```

    ## [1] 1

``` r
y
```

    ## [1] 1 2 3 4 5 6

``` r
z
```

    ## [1] 1 2 3 4 5 6

原子型向量仅保存类型相同的数据，
如果将数字与字符混合，R会将任何数值的类型转换为字符。运算符c()用于创建存储数字、字符或布尔值的向量。其中c代表连接(concatenate)，也可理解为收集(collect)或者合并(combine)。

``` r
first_vector <- c(1,2,3,4,5,6)
second_vector <- c('a', 'b', 'c','d')
third_vector <- c('a', 1, 2, 3)
first_vector
```

    ## [1] 1 2 3 4 5 6

``` r
second_vector
```

    ## [1] "a" "b" "c" "d"

``` r
third_vector
```

    ## [1] "a" "1" "2" "3"

``` r
# 向量合并
new_vector <- c(first_vector,second_vector)
new_vector
```

    ##  [1] "1" "2" "3" "4" "5" "6" "a" "b" "c" "d"

判断某个对象是否为原子型向量：

``` r
is.vector(x)
```

    ## [1] TRUE

``` r
# TRUE
```

> R中的原子型向量可以存储以下6种数据类型：双整型(double)，整型(integer)，字符型(character)，逻辑型(logical)，复数类型(complex)，原始类型(raw)

## 双整型

R中数值的默认以双整型(double)存储，也可以将双整型称为数值型(numeric)。

``` r
z <- c(1,2,3,4,5,6)
class(z)
```

    ## [1] "numeric"

``` r
typeof(z)
```

    ## [1] "double"

## 整型

整型用来存储整数数据，整形数据也可以被存储为双整型。在R中，设定整型的方法是在数值之后加上L，否则数值将被默认存储为双整型。

``` r
int <- c(1L, 2L, 3L)
class(int)
```

    ## [1] "integer"

``` r
typeof(int)
```

    ## [1] "integer"

## 字符型

字符型向量存储文本数据，在R中，字符类型需要加引号。

``` r
text <- c("Hello", "World")
typeof(text)
```

    ## [1] "character"

``` r
class(text)
```

    ## [1] "character"

## 逻辑型

逻辑型向量用来存储TRUE（真）和FALSE（假），R也会默认把T和F分别当做TRUE和FALSE的简写。

``` r
logic <- c(TRUE, FALSE, TRUE)
typeof(logic)
```

    ## [1] "logical"

``` r
class(logic)
```

    ## [1] "logical"

## 复数类型

复数类型用来存储复数。

``` r
# 注意虚部即使为1也要添加上去
comp <- c(1+1i, 2+2i, 1+3i)
typeof(comp)
```

    ## [1] "complex"

``` r
class(comp)
```

    ## [1] "complex"

## 原始类型

``` r
# 生成长度为3的原始类型向量
raw(3)
```

    ## [1] 00 00 00

# 向量类型转换

R中通过强制转换数据类型使得向量中仅存储单一类型的数据：如果一个原子型向量包含字符串，R会将该向量中的所有元素都转换成字符型；如果一个原子型向量包含逻辑型和数值型元素，R会将逻辑型全部转换成数值型（所有的TRUE值都将被转换成1，所有的FALSE值都将被转换成0）。

对逻辑型向量进行运算，R也会应用上述转换规则：

``` r
sum(c(TRUE,FALSE,TRUE,FALSE))
```

    ## [1] 2

``` r
mean(c(TRUE,FALSE,TRUE,FALSE))
```

    ## [1] 0.5

其中, `sum`函数相当于是在计算向量包含多少个`TRUE`值，而`mean`函数则计算TRUE值在向量中所占的比例。
通过`as`系列函数可以实现数据类型的转换：

## 数值型转字符型

``` r
as.character(1)
```

    ## [1] "1"

## 字符型转数值型

``` r
as.numeric("1")
```

    ## [1] 1

## 数值型转逻辑型

``` r
as.logical(1)
```

    ## [1] TRUE

## 逻辑型转数值型

``` r
as.numeric(FALSE)
```

    ## [1] 0

# 属性

属性是附加给原子型向量的额外信息，一个原子型向量最常见的三种属性是：名称(name)，维度(dim)和类(class)。查看所有属性信息：

``` r
attributes(1:6)
```

    ## NULL

## 名称

添加名称属性：使用`names`函数将一串与原子型向量等长的向量赋给原子型向量作为它的名称属性值。

``` r
vec <- 1:6
names(vec) <- c("one", "two", "three",'four', "five", "six")
```

``` r
vec
```

    ##   one   two three  four  five   six 
    ##     1     2     3     4     5     6

查询名称属性值:

``` r
vec
```

    ##   one   two three  four  five   six 
    ##     1     2     3     4     5     6

``` r
names(vec)
```

    ## [1] "one"   "two"   "three" "four"  "five"  "six"

``` r
attributes(vec)
```

    ## $names
    ## [1] "one"   "two"   "three" "four"  "five"  "six"

修改名称属性与创建名称属性的方法相同，删除名称属性只需要将`NULL`值赋给`names`函数即可。

## 维度

原子型向量可以通过添加维度属性的方式转换为二维矩阵(`matrix`)或者多维数组(`array`)。在分配维度属性时，R始终将第一个值赋给行数，将第二个值赋给列数。
将`vec`向量组织成一个2行3列的矩阵:

``` r
dim(vec) <- c(2,3)
class(vec)
```

    ## [1] "matrix" "array"

``` r
vec
```

    ##      [,1] [,2] [,3]
    ## [1,]    1    3    5
    ## [2,]    2    4    6

``` r
dim(vec) <- c(1,2,3)
class(vec)
```

    ## [1] "array"

``` r
vec
```

    ## , , 1
    ## 
    ##      [,1] [,2]
    ## [1,]    1    2
    ## 
    ## , , 2
    ## 
    ##      [,1] [,2]
    ## [1,]    3    4
    ## 
    ## , , 3
    ## 
    ##      [,1] [,2]
    ## [1,]    5    6

## 类

通过上面的例子可以发现，类是原子型向量的一个特例，例如，矩阵`vec`其实是一个特殊的（`dim`属性为`c(2,3)`,
`class`属性为`matrix`）整型向量。当改变`vec`的维度时，R就为其新添了一个`class`属性。在运行`attributes`函数时，对象的`class`属性并非总是会显示。

# 日期和时间

日期和时间型在R中本质上是一种特殊（`class`属性为`"POSIXct"`与`"POSIXt"`
）的双整型。`POSIXct`是一个被广泛用于表示日期与时间的框架，在`POSIXct`框架下，时间被表示为自1970年1月1日零点（UTC时间）开始所逝去的秒数。

``` r
# 返回计算机当前时间
now <- Sys.time()
now
```

    ## [1] "2020-06-15 21:57:18 CST"

``` r
typeof(now)
```

    ## [1] "double"

``` r
class(now)
```

    ## [1] "POSIXct" "POSIXt"

查看日期与时间类型对应的具体数值:

``` r
unclass(now)
```

    ## [1] 1592229438

计算1970年1月1日零点之后的100万秒对应的时间:

``` r
seconds <- 10^6
class(seconds) <- c("POSIXct","POSIXt")
seconds
```

    ## [1] "1970-01-12 21:46:40 CST"

# 因子

因子用来存储R中的分类信息，向factor函数传递一个原子型向量即可生成一个因子。R会将向量中的值重新编码为一串整数值，再将编码的结果存储在一个整型向量中。此外，R还会将levels属性和class属性添加到该整形向量，其中levels属性包含因子值的一组标签，而class属性为factor。

``` r
gender <- factor(c("male", "female", "female", "male"))
typeof(gender)
```

    ## [1] "integer"

``` r
attributes(gender)
```

    ## $levels
    ## [1] "female" "male"  
    ## 
    ## $class
    ## [1] "factor"

查看因子的存储方式：

``` r
unclass(gender)
```

    ## [1] 2 1 1 2
    ## attr(,"levels")
    ## [1] "female" "male"

将因子强制转换为字符串</font>  

``` r
as.character(gender)
```

    ## [1] "male"   "female" "female" "male"
    
</font>