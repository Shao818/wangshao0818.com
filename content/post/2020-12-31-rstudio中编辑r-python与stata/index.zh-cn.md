---
title: Rstudio中编辑R、Python与Stata
author: 王芍
date: '2020-12-31'
slug: []
categories:
  - R
tags:
  - R
  - 工具
lastmod: '2020-12-31T13:31:13+08:00'
keywords: []
description: ''
comment: no
toc: true
---

<!--more-->



<div align=center> <font face="华文中宋" color=#C46657 size=5 >目录</font></div>

------- 
<font face="华文中宋" >
-   [插入不同的代码块](#插入不同的代码块)
    -   [插入`R`代码块](#插入r代码块)
    -   [插入`Python`代码块](#插入python代码块)
    -   [插入`Stata`代码块](#插入stata代码块)
-   [`R`与`Python`的数据交换](#r与python的数据交换)
    -   [由`Python`到`R`的数据传递](#由python到r的数据传递)
    -   [由`R`到`Python`的数据传递](#由r到python的数据传递)
    -   [在 `R`中运行 `Stata`](#在-r中运行-stata)
    -   [在 `Stata`中运行`R`](#在-stata中运行r)</font>
    
------- 


<font face="华文中宋" >

# 插入不同的代码块


## 插入`R`代码块

``` r
car_data<- head(cars)
head(car_data)
```

    ##   speed dist
    ## 1     4    2
    ## 2     4   10
    ## 3     7    4
    ## 4     7   22
    ## 5     8   16
    ## 6     9   10

## 插入`Python`代码块

``` python
print("Hello Python!")
```

    ## Hello Python!

``` python
import numpy as np
a = np.array([[1,2,4.0],[3,6,9]])
a
```

    ## array([[1., 2., 4.],
    ##        [3., 6., 9.]])

``` python
a.ndim
```

    ## 2

## 插入`Stata`代码块

``` stata
sysuse auto
summarize
```

    ## 
    ## 
    ## . sysuse a(1978 Automobile Data)
    ## 
    ## . summarize
    ## 
    ##     Variable |        Obs        Mean    Std. Dev.       Min        Max
    ## -------------+---------------------------------------------------------
    ##         make |          0
    ##        price |         74    6165.257    2949.496       3291      15906
    ##          mpg |         74     21.2973    5.785503         12         41
    ##        rep78 |         69    3.405797    .9899323          1          5
    ##     headroom |         74    2.993243    .8459948        1.5          5
    ## -------------+---------------------------------------------------------
    ##        trunk |         74    13.75676    4.277404          5         23
    ##       weight |         74    3019.459    777.1936       1760       4840
    ##       length |         74    187.9324    22.26634        142        233
    ##         turn |         74    39.64865    4.399354         31         51
    ## displacement |         74    197.2973    91.83722         79        425
    ## -------------+---------------------------------------------------------
    ##   gear_ratio |         74    3.014865    .4562871       2.19       3.89
    ##      foreign |         74    .2972973    .4601885          0          1

# `R`与`Python`的数据交换

## 由`Python`到`R`的数据传递

``` r
library(reticulate)
```

Create a variable `x` in the Python session:

``` python
x = [1, 2, 3]
```

Access the Python variable `x` in an R code chunk:

``` r
data_py_to_r1<-py$x;data_py_to_r1
```

    ## [1] 1 2 3

``` r
data_py_to_r2<-py$a;data_py_to_r2
```

    ##      [,1] [,2] [,3]
    ## [1,]    1    2    4
    ## [2,]    3    6    9

## 由`R`到`Python`的数据传递

``` r
py$car_data<-car_data
```

``` python
print(car_data)
```

    ##    speed  dist
    ## 0    4.0   2.0
    ## 1    4.0  10.0
    ## 2    7.0   4.0
    ## 3    7.0  22.0
    ## 4    8.0  16.0
    ## 5    9.0  10.0

## 在 `R`中运行 `Stata`

``` r
library(RStata)  ##加载包
options("RStata.StataPath" =  "\"C:\\Program Files\\Stata16\\StataMP-64\"") 
options("RStata.StataVersion" = 16)  ##设置版本
```

``` r
stata_src <- " 
sysuse auto, clear
reg mpg weight "
```

``` r
stata(stata_src)
```

    ## .  
    ## . sysuse auto, clear
    ## (1978 Automobile Data)
    ## . reg mpg weight 
    ## 
    ##       Source |       SS           df       MS      Number of obs   =        74
    ## -------------+----------------------------------   F(1, 72)        =    134.62
    ##        Model |   1591.9902         1   1591.9902   Prob > F        =    0.0000
    ##     Residual |  851.469256        72  11.8259619   R-squared       =    0.6515
    ## -------------+----------------------------------   Adj R-squared   =    0.6467
    ##        Total |  2443.45946        73  33.4720474   Root MSE        =    3.4389
    ## 
    ## ------------------------------------------------------------------------------
    ##          mpg |      Coef.   Std. Err.      t    P>|t|     [95% Conf. Interval]
    ## -------------+----------------------------------------------------------------
    ##       weight |  -.0060087   .0005179   -11.60   0.000    -.0070411   -.0049763
    ##        _cons |   39.44028   1.614003    24.44   0.000     36.22283    42.65774
    ## ------------------------------------------------------------------------------

## 在 `Stata`中运行`R`

``` stata
clear
webuse grunfeld,clear //利用webuse从网络读取数据
list in 1/10          // 显示该数据集的前10行
xtset company year, yearly //设置面板数据格式
xtreg invest mvalue kstock, fe  //fe表示固定效应
```

    ## 
    ## 
    ## . cl. webuse grunfeld,clear //利用webuse从网络读取数据
    ## 
    ## . list in 1/10          // 显示该数据集的前10行
    ## 
    ##      +--------------------------------------------------+
    ##      | company   year   invest   mvalue   kstock   time |
    ##      |--------------------------------------------------|
    ##   1. |       1   1935    317.6   3078.5      2.8      1 |
    ##   2. |       1   1936    391.8   4661.7     52.6      2 |
    ##   3. |       1   1937    410.6   5387.1    156.9      3 |
    ##   4. |       1   1938    257.7   2792.2    209.2      4 |
    ##   5. |       1   1939    330.8   4313.2    203.4      5 |
    ##      |--------------------------------------------------|
    ##   6. |       1   1940    461.2   4643.9    207.2      6 |
    ##   7. |       1   1941      512   4551.2    255.2      7 |
    ##   8. |       1   1942      448   3244.1    303.7      8 |
    ##   9. |       1   1943    499.6   4053.7    264.1      9 |
    ##  10. |       1   1944    547.5   4379.3    201.6     10 |
    ##      +--------------------------------------------------+
    ## 
    ## . xtset company year, yearly //设置面板数据格式
    ##        panel variable:  company (strongly balanced)
    ##         time variable:  year, 1935 to 1954
    ##                 delta:  1 year
    ## 
    ## . xtreg invest mvalue kstock, fe  //fe表示固定效应
    ## 
    ## Fixed-effects (within) regression               Number of obs     =        200
    ## Group variable: company                         Number of groups  =         10
    ## 
    ## R-sq:                                           Obs per group:
    ##      within  = 0.7668                                         min =         20
    ##      between = 0.8194                                         avg =       20.0
    ##      overall = 0.8060                                         max =         20
    ## 
    ##                                                 F(2,188)          =     309.01
    ## corr(u_i, Xb)  = -0.1517                        Prob > F          =     0.0000
    ## 
    ## ------------------------------------------------------------------------------
    ##       invest |      Coef.   Std. Err.      t    P>|t|     [95% Conf. Interval]
    ## -------------+----------------------------------------------------------------
    ##       mvalue |   .1101238   .0118567     9.29   0.000     .0867345    .1335131
    ##       kstock |   .3100653   .0173545    17.87   0.000     .2758308    .3442999
    ##        _cons |  -58.74393   12.45369    -4.72   0.000    -83.31086     -34.177
    ## -------------+----------------------------------------------------------------
    ##      sigma_u |  85.732501
    ##      sigma_e |  52.767964
    ##          rho |  .72525012   (fraction of variance due to u_i)
    ## ------------------------------------------------------------------------------
    ## F test that all u_i=0: F(9, 188) = 49.18                     Prob > F = 0.0000
    
</font>