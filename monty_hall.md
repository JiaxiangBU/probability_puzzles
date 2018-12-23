Monty Hall 概率问题
================

Monty Hall(蒙蒂·霍尔) 概率问题， 表述参考 Chi (2018)， 英文叙述见[讲义1](docs/chapter1.pdf)

1.  一个比赛，选手需要从三个门中打开一个门，只有一个门有奖，其他为空。
2.  在选手选定一个门后，主持人会打开剩余的两个门中一个门，其中为空，然后询问选手是否更换自己的选择，即选择另外一个未打开的门。
3.  这里存在的概率问题是选手改变或者不改变选择的概率计算。

这里使用R重复10000次，估算概率。

1.  假设选手一直选门1，奖随机在三个门后出现。

<!-- end list -->

``` r
set.seed(1)
stick_data <- 
data.table(
    initial_choice = 1
    ,prize = sample(1:3,10000,replace = T)
) %>% 
    mutate(
        win = initial_choice == prize
    )
stick_data %>% 
    head
```

    ##   initial_choice prize   win
    ## 1              1     1  TRUE
    ## 2              1     2 FALSE
    ## 3              1     2 FALSE
    ## 4              1     3 FALSE
    ## 5              1     1  TRUE
    ## 6              1     3 FALSE

``` r
stick_data %>% 
    summarise(mean(win))
```

    ##   mean(win)
    ## 1    0.3323

1.  假设选手先选门1，在主持人打开另外一个门后，选择剩余一个没有打开的门，作为变换。
2.  对于主持人来说，
    1.  当选择选择的门1是奖时，那么会随机在剩余的两个门中打开一个，即门2和门3中选择
    2.  当选择选择的门1不是奖时，那么会剩余两个门中有一个是奖，主持人会打开另外一个没奖的门 (因此这里就是概率上升的原因)

以下 `reveal` 衡量打开门这个行为。

``` r
set.seed(1)
switch_data <- 
data.table(
    initial_choice = 1
    ,prize = sample(1:3,10000,replace = T)
) %>% 
    mutate(
        reveal = 
            map2_int(.x = initial_choice,.y = prize
                 ,~if (.x == .y) {
                    sample((1:3)[-.y], size = 1)
                } else {
                    (1:3)[-c(.x,.y)]
                }
                 )
    ) %>% 
    mutate(
        switch = 
            map2_int(.x = initial_choice,.y = reveal
                     ,~(1:3)[-c(.x, .y)]
                     )
    ) %>% 
    mutate(
        win = switch == prize
    )
switch_data %>% 
    head
```

    ##   initial_choice prize reveal switch   win
    ## 1              1     1      2      3 FALSE
    ## 2              1     2      3      2  TRUE
    ## 3              1     2      3      2  TRUE
    ## 4              1     3      2      3  TRUE
    ## 5              1     1      3      2 FALSE
    ## 6              1     3      2      3  TRUE

``` r
switch_data %>% 
    summarise(mean(win))
```

    ##   mean(win)
    ## 1    0.6677

因此切换的概率更高。

<div id="refs" class="references">

<div id="ref-Chi2018">

Chi, Peter. 2018. “Probability Puzzles in R.” 2018.
<https://www.datacamp.com/courses/probability-puzzles-in-r>.

</div>

</div>
