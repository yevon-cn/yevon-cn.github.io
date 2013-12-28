---
layout: post
title: "浅谈EasyUI的使用"
date: 2013-05-29 21:41:32
tags: 
- HTML
- JavaScript
- jQuery
- EasyUI
---
不记得上篇文章是啥时候写的了，似乎是没有时间，或是精力，或是动力，或是真的没有可写的内容~

在我们的人生道路上，走远了就会发现学习然后总结以及分享，这个顺序是那么的理所当然，所以有了此龊文。

**温馨的PS：**以下代码需要有些js、jquery、html、css基础才行哦！

最近对EasyUI感触比较多，很久以前，我还没听说过EasyUI，后来由于工作需要，由于没人教，所以自学了起来。因为没用过这些UI库，所以一无所知，项目还是有点急的吧，至少不是很缓，于是乎就对其只是有了个大概了解罢了，使用起来感觉满吃力的，当时主要就用到了datagrid，至于表单验证之类的都是自己写js来一个个判断的。因为出于对UI的理解，觉得这个库主要提供界面的东西吧，呵呵，很肤浅吧。

最近看了看别人写的EasyUI使用代码，然后在此基础上看了看官方的英文使用文档，发现我以前仅仅只是牵强的用着其功能，并没有发挥它的功用。当然现在我还并未精通，只是进步了不少，因而写个总结，或者说想写点啥吧，好满足下自己。

1、以datagrid为例，easyui的组件一般都有2种创建方式，一种是在html元素上添加专属的class，让easyui在页面加载后自动渲染该元素成目标组件；另一种是通过js直接操作目标元素执行组件的专属方法来渲染。如下所示：

{% highlight html linenos %}
<table class="easyui-datagrid" style="width:400px;height:250px"  
        data-options="url:'datagrid_data.json',fitColumns:true,singleSelect:true">  
    <thead>  
        <tr>  
            <th data-options="field:'code',width:100">Code</th>  
            <th data-options="field:'name',width:100">Name</th>  
            <th data-options="field:'price',width:100,align:'right'">Price</th>  
        </tr>  
    </thead>  
</table>
{% endhighlight %}

{% highlight html linenos %}
<table id="dg"></table>  
<script>
    $('#dg').datagrid({  
        url:'datagrid_data.json',  
        columns:[[  
            {field:'code',title:'Code',width:100},  
            {field:'name',title:'Name',width:100},  
            {field:'price',title:'Price',width:100,align:'right'}  
        ]]  
    }); 
</script>
{% endhighlight %}

个人觉得还是写js灵活自如点啊。

每个js库的使用细节或者说规则上还是有些差异的，至少以我目前的水平看它们，是如此的。如果要对datagrid进行操作，比如重新加载数据，那么一般想来是调用reload方法，EasyUI的做法需要如此：

{% highlight javascript linenos %}
$('#dg').datagrid('reload');
{% endhighlight %}

看着有点古怪，调用方法跟传参数似的，当时我就很难接受这样的做法，但是要用它也没办法，只能如此啊。

对于datagrid的其他使用，建议看看官方文档，还是蛮不错的，有Demo，也有使用文档，还有教程，还是比较全的，虽然是英文的，但是直接看看代码还是蛮直接的，再回来看看英文也差不多了解了。

2、validatebox，这个就是验证表单用的，听说过html5没，html5的表单就提供了几个属性，可以直接配置是否必填、验证表单的正则表达式等功能。而这里的这个validatebox也是类似的功能，通过和form的搭配使用，免去了繁琐的js操作，当然并不是说很完美哦，呵呵，我是个执着于完美的人。
validatebox这个组件同样提供2种方式来创建：

{% highlight html linenos %}
<input id="vv" class="easyui-validatebox" data-options="required:true,validType:'email'" />  
{% endhighlight %}

{% highlight html linenos %}
<input id="vv" />  
<script>
    $('#vv').validatebox({  
        required: true,  
        validType: 'email'  
    });  
</script>
{% endhighlight %}

这里的验证类型是“email”，当然并不是说叫它email，它就知道这个验证的对象是email地址类型了，名字只是形象化了，让人一看明了，而这个email是EasyUI自带的验证类型，但其自带的毕竟有限，难以满足需求的变化，所以提供了扩展规则的方法：

{% highlight html linenos %}
<script>
$.extend($.fn.validatebox.defaults.rules, {  
    minLength: {  
        validator: function(value, param){  
            return value.length >= param[0];  
        },  
        message: 'Please enter at least {0} characters.'  
    }  
}); 
</script>
<input class="easyui-validatebox" data-options="validType:'minLength[5]'">
{% endhighlight %}

这个还是蛮灵活的，能传参，也能直接获取文本框的值。当文本框失去焦点后就会自动触发判断，并提示信息出来。（图就不截了，自己看Demo去！）

如果要手动出发的话，需要如此：

{% highlight javascript linenos %}
$('#vv').validatebox('validate');//触发验证
$('#vv').validatebox('isValid');//触发验证并返回true/false
{% endhighlight %}

对了，还能给一个表单，添加多个规则验证呢，如下所示，js的话自己琢磨下吧，不错的功能哦！不知道在验证的同时能不能做些手脚呢？课后练习吧！

{% highlight html linenos %}
<input class="easyui-validatebox" data-options="
    required:true,
    validType:['email','length[0,20]']
">
{% endhighlight %}


4、可是，这个虽然便利了，但是form中一大堆表单项，一个个调用也是费力的，还要进行判断呢……
所以有了form这个组件，这个针对的还是form标签，这个同名的啊，估计纯属巧合，个人觉得怪怪的，有点混淆，难道是我没理解？-_-!
这个组件的使用，在我看来是个特例了吧，似乎只能通过js来操作，但有两种使用形式。

{% highlight javascript linenos %}
//转换id为ff的form为组件
$('#ff').form({  
    url:...,  
    onSubmit: function(){  
       //...
    },  
    success:function(data){  
       //。。。
    }  
});

//在需要的时候提交
$('#ff').submit();
{% endhighlight %}

{% highlight javascript linenos %}
//转换并立马触发提交事件
$('#ff').form('submit', {  
    url:...,  
    onSubmit: function(){  
      //...
    },  
    success:function(data){  
       //...
    }  
});  
{% endhighlight %}

似乎跑题了，刚才讲到一大堆表单项要进行验证来着。很简单的啦，form说，既然它包养了表单项们，那么这是就包在它身上了哈。

{% highlight javascript linenos %}
$('#ff').form('submit', {
    url: ...,
    onSubmit: function(){
        var isValid = $(this).form('validate');//触发form内的所有validatebox验证，并返回true/false，这个你懂的！
        return isValid;
    },
    success: function(){
             //...
    }
});
{% endhighlight %}

另外form组件还有个实用的功能：clear，即清空表单项数据。

时间过得真快啊，还有好多事要做呢，今天就到这里了，其他大家自己摸索吧，人生也是一路摸索或冒险来的，我们要习惯它，不然生活还是得继续啊，这就是人生吧，咱们没有任何理由去浪费。当然在冒险结束后，丰厚的成果等待着我们。

**学而时习之，不亦说乎？温故而知新，不亦兴乎？**