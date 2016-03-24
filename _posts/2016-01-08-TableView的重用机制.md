---
layout: post
title: TableView的重用机制
categories: 问题处理
date: 2016-03-24 16:32:04 +0800
tags: Tableview
description: 最近在写类似sina 微博界面的过程中使用到了cell，那么就是在cell上添加一些控件，但是由于每条微博的内容都是不同的，所以在显示的过程中，出现了内容重叠的问题，其实就是UITableViewCell重用机制的问题。
---
---

    最近在写类似sina 微博界面的过程中使用到了cell，那么就是在cell上添加一些控件，但是由于每条微博的内容都是不同的，所以在显示的过程中，出现了内容重叠的问题，其实就是UITableViewCell重用机制的问题。
----
`TableView的重用机制，为了做到显示和数据分离，IOS tableView的实现并且不是为每个数据项创建一个tableCell。而是只创建屏幕可显示最大个数的cell，然后重复使用这些cell，对cell做单独的显示配置，来达到既不影响显示效果，又能充分节约内容的目的。`        
==========
    但是在实际的使用中我们有时并不希望使用到重用机制，那么如何取消呢？
**1.对在cell中添加的控件设置tag的方法,例如在微博内容中需要添加label，那么就可以对添加的label设置tag，然后新建cell的时候先remove前一个cell tag相同的label，再添加新的label，这样就不会出现cell内容的重叠。**

    [[cell viewWithTag:100] removeFromSuperview];
    [[cell contentView] addSubview:contentLabel];
**2.删除cell中的所有子视图,一般项目中，一个cell会有多个控件（label，imageview...），按理说，对每一个控件都设置tag，按照第一种解决方法，应该是可以实现的。但是在实际运行过程中发现不行，还是会出现内容重叠的问题，所以采用第二种解决方法--在新建cell的时候，如果不是空就删除所有的子视图。**    
     if (cell != nil)
         {
            [cell removeFromSuperview];//处理重用
         }  
**3.通过为每个cell指定不同的重用标识符(reuseIdentifier)来解决,重用机制是根据相同的标识符来重用cell的，标识符不同的cell不能彼此重用。于是我们将每个cell的标识符都设置为不同，就可以避免cell重用问题了。**    
```
 - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
     {
         NSString *CellIdentifier = [NSString stringWithFormat:@"Cell%d%d", [indexPath section], [indexPath row]];//以indexPath来唯一确定cell
     UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier]; //出列可重用的cell
     if (cell == nil) {
         cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
     }
     //...其他代码
     }
```         
