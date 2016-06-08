---
date: '2011-12-16 23:33:39'
layout: post
slug: handwriting-recognition-with-50-lines-r-code
status: publish
title: 手写识别，50行R代码
wordpress_id: '661'
categories:
- R
tags:
- 度量
- 手写
- 相似
- 相关
- 识别
- R
- 程序
---

标题当然是唬人的，不过代码真的是50行正好（不算注释和空行啊），可以玩简单的数字识别。玩法很简单，先把附加的RData文件加载到R空间中（就是`load("train.RData")`这句），然后调用函数`guess()`，会打开一个空白的图形窗口。按住鼠标左键画一个0到9范围内的数字，然后右击鼠标确认，R会把猜测的结果输出出来。

<!-- more -->

```r
library(grid);
getData = function()
{
    pushViewport(viewport());
    grid.rect();
    px = NULL;
    py = NULL;
    mousedown = function(buttons, x, y)
    {
        if(buttons == 2) return(invisible(1));
        eventEnv$onMouseMove = mousemove;
        NULL
    }
    mousemove = function(buttons, x, y)
    {
        px <<- c(px, x);
        py <<- c(py, y);
        grid.points(x, y);
        NULL
    }
    mouseup = function(buttons, x, y) {
        eventEnv$onMouseMove = NULL;
        NULL
    }
    setGraphicsEventHandlers(onMouseDown = mousedown,
                             onMouseUp = mouseup);
    eventEnv = getGraphicsEventEnv();
    cat("Click down left mouse button and drag to draw the number,
   		right click to finish.\n");
    getGraphicsEvent();
    dev.off();
    s = seq(0, 1, length.out = length(px));
    spx = spline(s, px, n = 500)$y;
    spy = spline(s, py, n = 500)$y;
    return(cbind(spx, spy));
}

traceCorr = function(dat1, dat2)
{
    cor(dat1[, 1], dat2[, 1]) + cor(dat1[, 2], dat2[, 2]);
}

# recogTrain = list();
# for(i in 1:10)
# {
#     recogTrain[[i]] = getData();
# }
# save(recogTrain, file = "train.RData");

load("train.RData");
guess = function(verbose = FALSE)
{
    test = getData();
    coefs = sapply(recogTrain, traceCorr, dat2 = test);
    num = which.max(coefs);
    if(num == 10) num = 0;
    if(verbose) print(coefs);
    cat("I guess what you have input is ", num, ".\n", sep = "");
}
```

有玩成功了的同志在评论里吼一嗓子啊（貌似4很难识别对），谢谢。

下载：[train.RData](https://bitbucket.org/yixuan/cn/downloads/HandwritingRecognition.zip)
