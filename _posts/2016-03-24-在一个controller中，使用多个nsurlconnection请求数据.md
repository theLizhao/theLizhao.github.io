---
layout: post
title: 在一个Controller中，使用多个NSURLConnection请求数据
date: 2016-03-24 16:12:04 +0800
categories: 问题处理
tags: 网络
description: 每个软件皆有不同之功能需求，之前项目中有遇到同一Controller中使用多个NSURLConnection，遇到问题发现返回代理中无法判断是哪一个请求的返回数据，而NSURLConnection又无tag值属性。此时如何应对呢？
---
---

    每个软件皆有不同之功能需求，之前项目中有遇到同一Controller中使用多个NSURLConnection，遇到问题发现返回代理中无法判断是哪一个请求的返回数据，而NSURLConnection又无tag值属性。此时如何应对呢？
`使用其他属性也可以，如果能使用runtime为其添加一个tag则更好`
**1.使用AccessibilityLabel属性**
---
     NSURLConnection *conn=[[NSURLConnection alloc]initWithRequest:request delegate:self];[conn setAccessibilityLabel:@"1”];
**2.判断标志处理不同请求数据**
---
     NSURLConnection *connn=(NSURLConnection *)connection;
    switch ([[connn accessibilityLabel] intValue]) {
        case 1:{
            [receivedData appendData:data];
            currentLength += [receivedData length];
        }
            break;
        case 2:
        {
             [receivedData2 appendData:data];
             currentLength += [receivedData length];
        }
            break;
        default:
            break;
    }
==========
