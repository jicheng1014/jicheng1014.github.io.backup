---
layout: post
title: "current_user永不进入model"
date: 2014-11-21 11:03:57 +0800
comments: true
categories: ruby rubyonrails testing 
---

上次写代码的时候用到了devise，自然也会用到比较常用的current_user 
之后我写的时候有这样一个需求

> 在用户新建、保存一个model 的时候，应该顺手保存这个model是由谁建立的

我觉得这是一个典型的callback 应用，于是在before_validate 加入了
`self.user_id = current_user.id`
这个写法,但是很显然的，运行一下程序，没有通过

发现是current_user 没有被正确的读出来

看来current_user 这种东西model 是读不到的。本来想看能否做一些手脚，
但是后来我想了一下，这要是加入进去了，那Rspec该怎么写？

又想起了妈妈的教诲  model 只做model该做的事情。current_user 是页面级别登录后产生的产物，
跟模型自身是没有任何原因的，看来callback 这个策略是行不通了，最多写成validate 提醒自己

只有在controller 下进行验证了 
```ruby
def create
    @dress = current_user.shop.dresses.build(params[:dress])
    @dress.currency = current_user.shop.currency
    if @dress.save
       .....
    else
       .....
    end
  end
```

大概就是这样了

