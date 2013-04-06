---
date: '2012-01-18 16:16:48'
layout: post
slug: new-method-to-read-excel-file-in-r
status: publish
title: 用R读取Excel的新方法
wordpress_id: '674'
categories:
- R
- 学习中
tags:
- Excel
- Java
- Office
- R
- rJava
- xlsx
- 函数
---

还在纠结用如何用R读取Excel的同学有福了。昨天逛CRAN的时候发现了一个xlsx包，它给出的介绍是可以读取、写入Excel 2007/2003文件并支持格式的设置。简单地来说，将Excel读取为数据框，以及将数据框写入为Excel文件都不是问题，而更加强大的是它能处理Excel中的格式，比如合并单元格，设置列的宽度，设置字体和颜色等等。

如果只需要基本的读取/写入操作，那么其中的`read.xlsx()`和`write.xlsx()`应该就能满足大部分的需求了，其用法也很简单，看看帮助文档就了解了。此外，还有两个相应的函数`read.xlsx2()`和`write.xlsx2()`，按作者的话说，这两个函数使用了不同的实现方式，效率上会更高一些。

除了基本的读写操作之外，如之前所说，xlsx包还能进行格式方面的设置。下面是一个简单的例子，说明了如何创建工作簿和工作表，如何操作单元格等。感兴趣的朋友不妨运行一下下面的例子，看看最终的效果。

<!-- more -->

{% highlight r %}
link = "http://yixuan.cos.name/cn/wp-content/uploads/2012/01/ind.txt";
ind = read.table(url(link), sep = "\t");

library(xlsx);
# Create a new workbook
wb = createWorkbook();
# Create a new sheet with a name
sheet1 = createSheet(wb, "第一页");
# Set the zoom ratio when you open the Excel file
setZoom(sheet1, 50, 100);
# Set the width of columns
setColumnWidth(sheet1, 1:100, 2.8);

# Create rows
rows = createRow(sheet1, 1:40);
# Create cells for each row
cells = createCell(rows, 1:73);
# Merge the first row into one cell
addMergedRegion(sheet1, 1, 1, 1, 73);
# Create the style for title cell
title_cell_style = CellStyle(wb,
    alignment = Alignment(horizontal = "ALIGN_CENTER"),
    font = Font(wb, "blue", 50, isBold = TRUE));
# Create the style for black cells
black_cell_style = CellStyle(wb,
    border = Border(),
    fill = Fill(foregroundColor= "black"));
# Get the first row
first_row = getRows(sheet1, 1);
# Get the title cell from first row
title_cell = getCells(first_row, 1)[[1]];
# Set the value of the title cell
setCellValue(title_cell, "Read/Write Excel!");
# Set the style of the title cell
setCellStyle(title_cell, title_cell_style);
# Set the style of black cells
tmp = mapply(function(x, y) setCellStyle(cells[[x, y]], black_cell_style),
    ind[, 1] + 3, ind[, 2] + 5);
# Save the workbook into a file
saveWorkbook(wb, "test.xlsx");
{% endhighlight %}

总的来说，xlsx包是我目前见过的功能最全的操作Excel的R包，它只依赖于Java环境和rJava、xlsxjars两个包，在多种平台下都能运行，局限是写操作只支持Excel 2007格式（*.xlsx），对于机器上只有MS Office 2003的人来说可能会有些不便。（LibreOffice和OpenOffice.org都可以打开Excel 2007文件）
