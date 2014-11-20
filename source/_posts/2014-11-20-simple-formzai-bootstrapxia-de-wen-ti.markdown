---
layout: post
title: "simple-form在bootstrap下的问题"
date: 2014-11-20 17:18:19 +0800
comments: true
categories: simple_form gem ruby bootstrap rails
---

今天被simple_form 调戏了一下。

我写的一个小项目，原来一直没有用过simple_form，后来觉得毕竟simple_form 还是可以提高一些开发效率，并且学习一些新的东西没什么不好的。

我的小项目使用了bootstrap3 作为前端，
依照官方的事例，使用了
`rails generate simple_form:install --bootstrap`命令，分别建立了simple_form 和simple_form_bootstrap.rb 文件

根据官方的事例，很快我修改了simple_form_bootstrap 里的wrappers,看起来一切正常

```
config.wrappers :horizontal_form, tag: 'div', class: 'form-group', error_class: 'has-error' do |b|
    b.use :html5
    b.use :placeholder
    b.optional :maxlength
    b.optional :pattern
    b.optional :min_max
    b.optional :readonly
    b.use :label, class: 'col-sm-3 control-label'
    b.wrapper tag: 'div', class: 'col-sm-10' do |ba|
      ba.use :input, class: 'form-control'
      ba.use :error, wrap_with: { tag: 'span', class: 'help-block' }
      ba.use :hint,  wrap_with: { tag: 'p', class: 'help-block' }
    end
  end

```
之后views 如下所示

```
<%= simple_form_for(@prisoner, html:{class: 'form-horizontal'},wrapper: :horizontal_form)  do |f| %>
  <%= f.input :name %>
  <%= f.input :idcard %>
  <%= f.input :gender, collection: ['男','女'], :as => :radio_buttons %>
  <hr class="inner-separator" />
  <%= f.button :submit %>
<% end %>

```

看起来一切正常不是么，理论上来说，效果应该是label 有col-sm-3 和control-label 的class，但是诡异的事情发生了

生成的代码如下所示

```html
<div class="form-group string optional prisoner_idcard">
    <label class="string optional control-label" for="prisoner_idcard">身份证号</label>
    <div class="col-sm-9">
      <input class="string optional" id="prisoner_idcard" name="prisoner[idcard]" type="text" />
    </div>
</div>
```

我注意到了代码里有生成**col-sm-9** 这说明本身wrapper是正确加载了的 
实际上是use xxx,class 没有加载成功
折腾了半天，都没有什么思路，
再次仔细读官方的demo,
后来才注意到，原来Gemfile 里Simple-form 版本有所不同

改为例子中所述版本后，问题解除，能够正确生成对应的class 了
`gem 'simple_form', '~> 3.1.0.rc1', github: 'plataformatec/simple_form', branch: 'master'`

但是感觉还是有点奇怪

总觉得官方文档这个给的似乎太含糊了，好歹应该有点说明或者其他的 

或者是我压根没有正确阅读官方文档？
