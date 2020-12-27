---
title: R数据科学Ⅳ：字符串、日期时间型数据与时间序列对象
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
  - 字符串
  - 日期时间型数据
  - 时间序列对象
lastmod: '2020-12-25T15:56:52+08:00'
keywords: []
description: ''
comment: no
toc: true
weight: 104
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

-   [字符串](#字符串)
    -   [字符串的长度（包含字符个数）](#字符串的长度包含字符个数)
    -   [字符串合并与拆分](#字符串合并与拆分)
    -   [字符串拆分](#字符串拆分)
    -   [字符串排序](#字符串排序)
    -   [检测匹配](#检测匹配)
    -   [提取字符串子集](#提取字符串子集)
    -   [修改字符串](#修改字符串)
    -   [转化大小写](#转化大小写)
    -   [其它函数](#其它函数)
-   [日期时间数据](#日期时间数据)
    -   [识别日期时间](#识别日期时间)
    -   [格式化输出日期时间](#格式化输出日期时间)
    -   [提取日期时间数据的年月日周时分秒](#提取日期时间数据的年月日周时分秒)
    -   [时间段数据](#时间段数据)
    -   [duration() ——
        以数值+时间单位存储时段的长度](#duration-以数值时间单位存储时段的长度)
    -   [period() —— 同 duration()](#period-同-duration)
    -   [日期的时间的计算](#日期的时间的计算)
    -   [时间序列数据](#时间序列数据)

-------------------------------------------------------------------------------

<font size=1  face="仿宋">

## 字符串

-   字符串是用双引号或单引号括起来的若干字符，建议用双引号，除非字符串中包
    含双引号。
-   字符串处理不是 R
    语言中的主要功能，但也是必不可少的，数据清洗、可视化等
    操作都会用到。
-   `stringr` 包提供了一系列接口一致的、简单易用的字符串操作函数。

``` r
library(stringr)
```

### 字符串的长度（包含字符个数）

``` r
str_length(c("a", "R for data science", NA))
```

    ## [1]  1 18 NA

``` r
str_pad(c("a", "ab", "abc"), 3) # 填充到长度为3
```

    ## [1] "  a" " ab" "abc"

``` r
str_trunc("R for data science", 10) # 截断到长度为10
```

    ## [1] "R for d..."

``` r
str_trim(c("a ", "b ", "a b")) # 移除空格
```

    ## [1] "a"   "b"   "a b"

> 注： 后三个函数都包含参数 side=c(“both”, “left”, “right”))
> 设定操作的方向。

### 字符串合并与拆分

#### 字符串合并

    str_c(..., sep="", collapse=NULL)
    其中，
    sep：设置间隔符，默认为空；
    collapse：指定间隔符，将字符串向量打开合并为一个字符串。

``` r
# 同paste0("x", 1:3) 或 paste("x", 1:3, sep="")
str_c("x", 1:3, sep="")
```

    ## [1] "x1" "x2" "x3"

``` r
str_c("x", 1:3, collapse="_")
```

    ## [1] "x1_x2_x3"

> 注： 1:3 自动向下兼容以适应字符串运算，效果同 c(“1”,“2”,“3”)

将字符串重复 n 次

    str_dup(string, times)
    其中，
    string：为要重复的字符向量；
    times：为重复的次数。

``` r
str_dup(c("A","B"), 3)
```

    ## [1] "AAA" "BBB"

``` r
str_dup(c("A","B"), c(3,2))
```

    ## [1] "AAA" "BB"

### 字符串拆分

    str_split(string, pattern)
    str_split_fixed(string, pattern, n)
    其中，
    string：为要拆分的字符串；
    paĴern：指定拆分的分隔符，可以是正则表达式。
    注: 前者返回列表, 后者返回矩阵，且能用 n 控制返回的列数。

``` r
x <- '10,8,7'
str_split(x, ",")
```

    ## [[1]]
    ## [1] "10" "8"  "7"

``` r
str_split_fixed(x, ",", n=2)
```

    ##      [,1] [,2] 
    ## [1,] "10" "8,7"

#### 字符串插入值

只要在字符串内用 {变量名} ，则函数 `str_glue()`和`str_glue_data`就可以
将字符串中的变量名替换成变量值，后者的参数`.x`支持引入数据框、列表等。

``` r
str_glue("Pi = {pi}")
```

    ## Pi = 3.14159265358979

``` r
name <- " 李明"
tele <- "13912345678"
str_glue("姓名: {name}", "电话号码: {tele}", .sep="；")
```

    ## 姓名:  李明；电话号码: 13912345678

### 字符串排序

    str_sort(x, decreasing=FALSE, locale="en", ...)
    str_order(x, decreasing=FALSE, locale="en", ...)

-   默认为升序，前者返回排好序的元素，后者返回排好序的索引；参数 locale
    可设 定语言。

``` r
x <- c("banana", "apple", "pear")
str_sort(x)
```

    ## [1] "apple"  "banana" "pear"

``` r
str_order(x)
```

    ## [1] 2 1 3

``` r
str_sort(c("香蕉", "苹果", "梨"), locale="ch")
```

    ## [1] "梨"   "苹果" "香蕉"

### 检测匹配

    `str_detect(string, pattern, negate=FALSE)` —— 检测是否存在匹配
    `str_which(string, pattern, negate=FALSE)` —— 查找匹配的索引
    `str_count(string, pattern)` —— 计算匹配的次数
    `str_locate(string, pattern)` —— 定位匹配的位置
    `str_starts(string, pattern)` —— 检测是否以 paĴern 开头
    `str_ends(string, pattern)` —— 检测是否以 paĴern 结尾
    其中，
    string：为要检测的字符串；
    paĴern：为匹配的模式，可以是正则表达式；
    negate：默认为 FALSE 表示正常匹配，若为 TRUE 则反匹配（找不匹配）。

``` r
x
```

    ## [1] "banana" "apple"  "pear"

``` r
str_detect(x, "p")
```

    ## [1] FALSE  TRUE  TRUE

``` r
str_which(x, "p")
```

    ## [1] 2 3

``` r
str_count(x, "p")
```

    ## [1] 0 2 1

``` r
str_locate(x, "a.") # 正则表达式, .匹配任一字符
```

    ##      start end
    ## [1,]     2   3
    ## [2,]     1   2
    ## [3,]     3   4

### 提取字符串子集

#### str\_sub()

-   根据指定的起始和终止位置提取子字符串，基本格式为：

<!-- -->

    str_sub(string, start=1, end=-1)

``` r
str_sub(x, 1, 3)
```

    ## [1] "ban" "app" "pea"

``` r
str_sub(x, 1, 5) # 若长度不够, 则尽可能多地提取
```

    ## [1] "banan" "apple" "pear"

``` r
str_sub(x, -3, -1)
```

    ## [1] "ana" "ple" "ear"

> 注： 若对提取的子串赋值，则为修改字符串。

#### str\_subset()

-   查找字符串向量中匹配的字符串，基本格式为：

<!-- -->

    str_subset(string, pattern, negate=FALSE) 若 negate=TURE, 则返回不匹配的字符串。

``` r
str_subset(x, "p")
```

    ## [1] "apple" "pear"

#### str\_extract() 和 str\_match()

-   用 `str_extract()`来提取匹配的内容；
-   用`str_match()`来提取匹配的内容以及各个分组捕获，返回矩阵，每行对应于
    字符串向量中的一个字符串，每行的第一个元素是匹配内容，其它元素是各个分
    组捕获，没有匹配则为 NA

``` r
x <- c("1978-2000", "2011-2020-2099")
pat <- "\\b(19|20)([0-9]{2})\\b" # 正则表达式
str_extract(x, pat)
```

    ## [1] "1978" "2011"

``` r
str_match(x, pat)
```

    ##      [,1]   [,2] [,3]
    ## [1,] "1978" "19" "78"
    ## [2,] "2011" "20" "11"

### 修改字符串

-   用新字符串赋值给 `str_sub()`提取的子字符串；
-   用`str_replace()` 做字符替换，基本格式为：

<!-- -->

    str_replace(string, pattern, replacement)
    其中，
    paĴern：要替换的子字符串或模式；
    replacement：要替换为的新字符串。

``` r
x
```

    ## [1] "1978-2000"      "2011-2020-2099"

``` r
str_replace(x, "-", "/")
```

    ## [1] "1978/2000"      "2011/2020-2099"

### 转化大小写

-   `str_to_upper()` —— 转换为大写；
-   `str_to_lower()` —— 转换为小写；
-   `str_to_title()` —— 转换标题格式（单词首字母大写）

``` r
str_to_lower("I love r language.")
```

    ## [1] "i love r language."

``` r
str_to_upper("I love r language.")
```

    ## [1] "I LOVE R LANGUAGE."

``` r
str_to_title("I love r language.")
```

    ## [1] "I Love R Language."

### 其它函数

-   `str_conv(string, encoding)` —— 转化字符串的字符编码
-   `str_view(string, paĴern, match)` —— 在 Viewer
    窗口输出（正则表达式）模式匹配结果
    -`word(string, start, end, sep=" ")` —— 从英文句子中提取单词
-   `str_wrap(string, width=80, indent=0, exdent=0)` —— 调整段落格式

> 关于 stringr 包的注： -
> 以上查找匹配的各个函数，只是查找第一个匹配，要想查找所有匹配，各个函数
> 都有另一版本：加后缀 `_all`，例如 `str_extract_all()` -
> 以上各个函数中的参数 pattern 都支持用 正则表达式 （Regular Expression,
> RE）表示模式 -
> 正则表达式，是根据字符串规律按一定法则，简洁表达一组字符串的表达式。 -
> 正则表达式是表示一组字符串的特征（或模式）, 正则表达式是处理文本数据非
> 常强大的工具，需要专门篇幅来讲解。

## 日期时间数据

-   日期值通常以字符串形式传入 R
    中，然后转化为以数值形式存储的日期变量。
-   R 的内部日期是以 1970 年 1 月 1 日至今的天数来存储，内部时间则是以
    1970 年1 月 1 日至今的秒数来存储。
-   `lubridate`包提供了更加方便的函数，生成、转换、管理日期时间数据。

``` r
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'
    
    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

### 识别日期时间

``` r
today()
```

    ## [1] "2020-12-25"

``` r
now()
```

    ## [1] "2020-12-25 16:25:50 CST"

``` r
as_datetime(today()) # 日期型转日期时间型
```

    ## [1] "2020-12-25 UTC"

``` r
as_date(now()) # 日期时间型转日期型
```

    ## [1] "2020-12-25"

-   无论年月日/时分秒按什么顺序及以什么间隔符分隔，总能正确地识别成日期时间
    值：

``` r
ymd("2020/03~01")
```

    ## [1] "2020-03-01"

``` r
myd("03202001")
```

    ## [1] "2020-03-01"

``` r
dmy("03012020")
```

    ## [1] "2020-01-03"

``` r
ymd_hm("2020/03~011213")
```

    ## [1] "2020-03-01 12:13:00 UTC"

> 注： 根据需要可以 `ymd_h/myd_hm/dmy_hms` 任意组合；可以用参数 tz =“…”
> 指定时区。

-   也可以用 `make_date()` 和 `make_datetime()`
    从日期时间组件创建日期时间：

``` r
make_date(2020, 3, 5)
```

    ## [1] "2020-03-05"

``` r
make_datetime(2020, 3, 5, 21, 7, 15)
```

    ## [1] "2020-03-05 21:07:15 UTC"

### 格式化输出日期时间

-   用 `format()` 函数

``` r
d <- make_date(2020, 3, 5)
format(d, '%Y/%m/%d')
```

    ## [1] "2020/03/05"

-   用 `stamp()`函数，按给定模板格式输出

``` r
t <- make_datetime(2020, 3, 5, 21, 7, 15)
fmt <- stamp("Created on Sunday, Jan 1, 1999 3:34 pm")
```

    ## Multiple formats matched: "Created on Sunday, %Om %d, %Y %H:%M %Op"(1), "Created on Sunday, %Om %d, %Y %H:%M pm"(1)
    
    ## Using: "Created on Sunday, %Om %d, %Y %H:%M %Op"

``` r
fmt(t)
```

    ## [1] "Created on Sunday, 03 05, 2020 21:07 下午"

### 提取日期时间数据的年月日周时分秒

``` r
t<-ymd_hms("2020/03/05 21:07:15") 
t
```

    ## [1] "2020-03-05 21:07:15 UTC"

``` r
year(t)
```

    ## [1] 2020

``` r
quarter(t) # 第几季度
```

    ## [1] 1

``` r
month(t)
```

    ## [1] 3

``` r
day(t)
```

    ## [1] 5

``` r
yday(t) # 当年的第几天
```

    ## [1] 65

``` r
hour(t)
```

    ## [1] 21

``` r
minute(t)
```

    ## [1] 7

``` r
second(t)
```

    ## [1] 15

``` r
weekdays(t)
```

    ## [1] "星期四"

``` r
wday(t) # 数值表示本周第几天, 默认周日是第1天
```

    ## [1] 5

``` r
wday(t,label=T) # 字符因子型表示本周第几天
```

    ## [1] 周四
    ## Levels: 周日 < 周一 < 周二 < 周三 < 周四 < 周五 < 周六

``` r
week(t) # 当年第几周
```

    ## [1] 10

``` r
tz(t) # 时区
```

    ## [1] "UTC"

用 `with_tz()`将时间数据转换为另一个时区的同一时间；`force_tz()` 将时间
数据的时区强制转换为另一个<font color=blue  >时区： </font>

``` r
with_tz(t, tz="America/New_York")
```

    ## [1] "2020-03-05 16:07:15 EST"

``` r
force_tz(t, tz="America/New_York")
```

    ## [1] "2020-03-05 21:07:15 EST"

-   还可以模糊提取（取整）到不同时间单位：

``` r
round_date(t, unit="hour") # 四舍五入取整到小时
```

    ## [1] "2020-03-05 21:00:00 UTC"

> 注： 类似地，向下取整: `floor_date()`; 向上取整: `ceiling_date()` -
> `rollback(dates, roll_to_first=FALSE, preserve_hms=TRUE)`：回
> 滚到上月最后一天或本月第一天

### 时间段数据

#### interval() —— 计算两个时间点的时间间隔，返回时间段数据

``` r
begin <- ymd_hm("2019-08-10 14:00")
end <- ymd_hm("2020-03-05 18:15")
gap <- interval(begin, end)
gap
```

    ## [1] 2019-08-10 14:00:00 UTC--2020-03-05 18:15:00 UTC

``` r
time_length(gap,'day') # 计算时间段的长度为多少天
```

    ## [1] 208.1771

``` r
time_length(gap,'minute') # 计算时间段的长度为多少分钟
```

    ## [1] 299775

``` r
t %within% gap # 判断t是否属于该时间段
```

    ## [1] FALSE

### duration() —— 以数值+时间单位存储时段的长度

``` r
duration(100, units="day")
```

    ## [1] "8640000s (~14.29 weeks)"

``` r
int <- as.duration(gap)
int
```

    ## [1] "17986500s (~29.74 weeks)"

### period() —— 同 duration()

-   二者区别：`duration`是基于数值线，不考虑闰年和闰秒；`period`
    是基于时间线， 考虑闰年和闰秒。
-   比如，`duration` 中的 1 年总是365天，而 period 的平年 365 天闰年 366
    天。

#### 固定单位的时间段

-   period 时间段：years(), months(), weeks(), days(), hours(),
    minutes(), seconds();
-   duration 时间段：dyears(), dmonths(), dweeks(), ddays(), dhours(),
    dminutes(),dseconds().

``` r
dyears(1)
```

    ## [1] "31557600s (~1 years)"

``` r
years(1)
```

    ## [1] "1y 0m 0d 0H 0M 0S"

### 日期的时间的计算

-   时间点+时间段生成一个新的时间点：

``` r
t + int
```

    ## [1] "2020-09-30 01:22:15 UTC"

``` r
leap_year(2020) # 判断是否闰年
```

    ## [1] TRUE

``` r
ymd(20190305) + years(1) # 加period的一年
```

    ## [1] "2020-03-05"

``` r
ymd(20190305) + dyears(1) # 加duration的一年, 365天
```

    ## [1] "2020-03-04 06:00:00 UTC"

``` r
t + weeks(1:3)
```

    ## [1] "2020-03-12 21:07:15 UTC" "2020-03-19 21:07:15 UTC"
    ## [3] "2020-03-26 21:07:15 UTC"

-   除法运算：

``` r
gap / ddays(1) # 除法运算, 同time_length(gap,'day')
```

    ## [1] 208.1771

``` r
gap %/% ddays(1) # 整除
```

    ## [1] 208

``` r
gap %% ddays(1) #余数
```

    ## [1] 2020-03-05 14:00:00 UTC--2020-03-05 18:15:00 UTC

``` r
as.period(gap %% ddays(1))
```

    ## [1] "4H 15M 0S"

-   月份加运算:
    `%m+%`，可实现日期按月数增加。例如，得到每月同一天的日期数 据：

``` r
date <- as_date("2019-01-01")
date %m+% months(0:11)
```

    ##  [1] "2019-01-01" "2019-02-01" "2019-03-01" "2019-04-01" "2019-05-01"
    ##  [6] "2019-06-01" "2019-07-01" "2019-08-01" "2019-09-01" "2019-10-01"
    ## [11] "2019-11-01" "2019-12-01"

-   prety\_dates()生成近似的时间刻度：

``` r
x <- seq.Date(as_date("2019-08-02"), by="year", length.out=2)
pretty_dates(x, 12)
```

    ##  [1] "2019-08-01 UTC" "2019-09-01 UTC" "2019-10-01 UTC" "2019-11-01 UTC"
    ##  [5] "2019-12-01 UTC" "2020-01-01 UTC" "2020-02-01 UTC" "2020-03-01 UTC"
    ##  [9] "2020-04-01 UTC" "2020-05-01 UTC" "2020-06-01 UTC" "2020-07-01 UTC"
    ## [13] "2020-08-01 UTC" "2020-09-01 UTC"

### 时间序列数据

-   为了研究某一事件的规律，依据时间发生的顺序将事件在多个时刻的数值记录下
    来，就构成了一个时间序列，用 *Y**t* 表示。

-   例如，国家或地区的年度财政收入，股票市场的每日波动，气象变化，工厂按小
    时观测的产量等等。另外，随温度、高度等变化而变化的离散序列，也可以看作
    时间序列。

-   `ts`
    数据类型是专门为时间序列设计的，一个时间序列数据，其实就是一个数值型
    向量，且每个数都有一个时刻与之对应。

-   用 `ts()` 函数生成时间序列，基本格式为：

<!-- -->

    ts(data, start=1, end, frequency=1, ...)
    其中，data：为数值向量或矩阵；
    start：设置起始时刻；
    end：设置结束时刻；
    frequency：设置时间频率，默认为 1，表示一年有 1 个数据。

``` r
ts(data=1:10, start=2010, end=2019) # 年度数据
```

    ## Time Series:
    ## Start = 2010 
    ## End = 2019 
    ## Frequency = 1 
    ##  [1]  1  2  3  4  5  6  7  8  9 10

``` r
ts(data=1:10, start=2010, frequency=4) # 季度数据
```

    ##      Qtr1 Qtr2 Qtr3 Qtr4
    ## 2010    1    2    3    4
    ## 2011    5    6    7    8
    ## 2012    9   10

-   同理，月度数据则 `frequency=12`，周度数则为
    `frequency=52`，日度数据则 为 frequency=365

> 注： `tsibble` 包、`fable` 包、`feasts` 包是最新 “tidy”
> 系列的时间序列数据处理包

关于 R 数据结构的注：

-   从向量的角度来看，R 的数据结构分为：
    -   原子向量：各个值都是同类型的，包括 6 种类型：logical、integer、
        double、character、complex、raw，其中 integer 和 double 也统称为
        numeric；
    -   列表：各个值可以是不同类型的，NULL 表示空向量（长度为 0 的向量）
-   向量都有两个属性：type（类型）、length（长度）
-   还能以属性的方式向向量中任意添加额外的
    metadata（元数据），属性可用来创
    建扩展向量，以执行一些新的操作。常用的扩展向量有：
    -   基于整数型向量构建的因子
    -   基于数值型向量构建的日期和日期时间
    -   基于数值型向量构建的时间序列
    -   基于列表构建的数据框和 tibble