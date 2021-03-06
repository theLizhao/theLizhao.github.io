---
layout: post
title: UITableViewCell 重用实现分析
categories: 探究
date: 2016-03-24 16:12:04 +0800
tags: Tableview
description: 查看UITableView头文件，会找到NSMutableArray*  visiableCells，和NSMutableDictnery* reusableTableCells两个结构。visiableCells内保存当前显示的cells，reusableTableCells保存可重用的cells。
---
---
###重用实现分析　
```
　查看UITableView头文件，会找到NSMutableArray*  visiableCells，和NSMutableDictnery* reusableTableCells两个结构。visiableCells内保存当前显示的cells，reusableTableCells保存可重用的cells。
　　TableView显示之初，reusableTableCells为空，那么tableView dequeueReusableCellWithIdentifier:CellIdentifier返回nil。开始的cell都是通过[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier]来创建，而且cellForRowAtIndexPath只是调用最大显示cell数的次数。
```
　　**比如：有100条数据，iPhone一屏最多显示10个cell。程序最开始显示TableView的情况是：**
>1. 用[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier]创建10次cell，并给cell指定同样的重用标识(当然，可以为不同显示类型的cell指定不同的标识)。并且10个cell全部都加入到visiableCells数组，reusableTableCells为空。

>2. 向下拖动tableView，当cell1完全移出屏幕，并且cell11(它也是alloc出来的，原因同上)完全显示出来的时候。cell11加入到visiableCells，cell1移出visiableCells，cell1加入到reusableTableCells。

>3. 接着向下拖动tableView，因为reusableTableCells中已经有值，所以，当需要显示新的cell，cellForRowAtIndexPath再次被调用的时候，tableView dequeueReusableCellWithIdentifier:CellIdentifier，返回cell1。cell1加入到visiableCells，cell1移出reusableTableCells；cell2移出visiableCells，cell2加入到reusableTableCells。之后再需要显示的Cell就可以正常重用了。

   所以整个过程并不难理解，但需要注意正是因为这样的原因：配置Cell的时候一定要注意，对取出的重用的cell做重新赋值，不要遗留老数据。

###一些情况

　　**使用过程中，我注意到，并不是只有拖动超出屏幕的时候才会更新reusableTableCells表，还有：**
>1. reloadData，这种情况比较特殊。一般是部分数据发生变化，需要重新刷新cell显示的内容时调用。在cellForRowAtIndexPath调用中，所有cell都是重用的。我估计reloadData调用后，把visiableCells中所有cell移入reusableTableCells，visiableCells清空。cellForRowAtIndexPath调用后，再把reuse的cell从reusableTableCells取出来，放入到visiableCells。
>2. reloadRowsAtIndex，刷新指定的IndexPath。如果调用时reusableTableCells为空，那么cellForRowAtIndexPath调用后，是新创建cell，新的cell加入到visiableCells。老的cell移出visiableCells，加入到reusableTableCells。于是，之后的刷新就有cell做reuse了。