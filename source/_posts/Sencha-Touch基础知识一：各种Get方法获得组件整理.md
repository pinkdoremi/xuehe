title: 'Sencha Touch基础知识一：获取一个组件方法整理'
date: 2014-07-28 22:33:20
tags:
- sencha touch
categories:
- sencha touch
---

获取一个组件有很多种方法。本文将分成四部分讲解：
1. 根据Id、itemId查找组件
2. 和Dom有关的获取
3. 使用选择器的查询获取组件
4. 使用选择器来查询获取dom元素

此外本文还将介绍关于选择器Selector
<!--more-->
至于在controller中常用的使用ref获取组件的方法，我用的不多，因为我使用了Deft Js。

----- 

## 一、根据Id、itemId查找组件

### Ext.getCmp("Id")
它会根据id值来查找一个已经存在的组件。
Ext.getCmp()中的参数只能是组件config中的id值，不能是itemId值。
它是`Ext.ComponentMgr.get()`的缩写版，当然啦，有缩写版干嘛还用复杂版~

### getComponent("itemId")
举个使用的例子：
`var son = father.getComponent('son');`
这个方法获取组件只能够获取父组件下的第一层子组件,**不能获得子组件的子组件**，也就是说父亲只能拿到儿子，不能拿到孙子。
这个方法**只能使用itemId值**。作为查找的参数，无法通过id值，要用id值请用上面的方法。
此外，这个方法的参数还可以是一个数字，子组件表示在items的位置。

## 二、和Dom有关的获取
### Ext.getDom(el)
参数el可以是：String (id),dom node,Ext.Element.
返回dom node。也就是说：我以孙子做参数，可以返回父亲（如果父亲没有爸爸了，否则还会返回父亲的父亲的父亲。。。直到头）
返回值是：HTMLElement

### Ext.get(element)
参数：
    String/HTMLElement/Ext.Element
    分别代表的是：node的id，dom node（上面提到），现有的element。
    
一样，它的完整版写法是： Ext.dom.Element.get.
返回值：Ext.dom.Element，或null

注意：Ext.getDom()&Ext.get()中提到的id值，都不是我们组件中config里的id值，所以用config中的id值去查找，肯定都是不成功的。
示范：

    Ext.define('test',{
        extend:'Ext.Container',
        id:'right',//这个可以
        config:{
            id:'wrong'//这个无法被找到
        }
    });
    Ext.create('text',{
        id:'wrongstill'//在create中的这个部分已经是属于config部分了，所以和上面一样，无法被找到
    });

## 三、使用选择器的查询获取组件

### query(selector)
这是一个和`getComponent()`差不多的方法。
不过，query可以查询所有后代，`getComponent`只能查子代。
selector是一种表示选择条件的字符串，选择器。
它的返回值可以是一个数组（也就是说可以查询所有满足条件的子代）。

### down(),up(),child()
这些方法和query()是一样的用法。区别在于：
down：查询后代
up：查询父代
child：查询子代
他们的返回值都是一个组件，而不是一个数组！返回第一个查找到的组件。

### Ext.ComponentQuery.query(selector,root)
这个方法是自定义根节点进行向下查找的。而上面的方法是已经确定了根节点，再进行查找的。
root是可选参数。

## 四、使用选择器来查询获取dom元素
### query(selector)
和上面对组件的`query()`方法类似，查询所有后代。
返回HTMLElement元素（数组）。

### select(selector,composite)
composite是对选择取反，默认为false
这个方法的返回值是一个`Ext.dom.CompositeElementLite/Ext.dom.CompositeElement`
它代表了所有符合条件的element，你可以统一对它操作。

        var els = Ext.select("#some-el div.some-class");
        // or select directly from an existing element
        var el = Ext.get('some-el');
        el.select('div.some-class');

        els.setWidth(100); // all elements become 100 width
        els.hide(true); // all elements fade out and hide
        // or
        els.setWidth(100).hide(true);

### down(),up(),child(),prev()
和上面组件的方法一样，这些方法和query()是一样的用法。区别在于：
down：查询后代
up：查询父代
child：查询子代
prev：跳过文本结点查找之前的兄弟姐妹（同一代）
next：跳过文本结点查找之前的兄弟姐妹（同一代）
last：获得最后一个子代
first：获得第一个子代
他们的返回值都是一个HTMLElement/Ext.dom.Element，而不是一个数组！返回第一个查找到的组件。

### Ext.query(selector,[root])
它的完整形式是：`Ext.dom.Query.select`
它和`Ext.DomQuery.select()`的使用方法是一样的。
方法和上面的`Ext.ComponentQuery.query`方法一样，不过查找的结果是一个或多个HTMLElement（数组）。

### Ext.DomQuery.selectNode(selector,root)
和上面的方法一样，返回一个HTMLElement。

## 五、selector
说了上面的方法，一定会需要知道如何使用选择器。
选择器的方法和css是很相似的：
[css选择器的方法](http://www.w3.org/TR/css3-selectors/)
#### id选择器：#buttonId
#### 类别选择器：.buttonclass
#### 属性选择器：button[text = "ok"]
可以选择具有某个属性，或者某个属性为某个值的组件
#### 后代选择器：#father panel
#### 子选择器：#father > panel
#### 群组选择器：button,panel
#### 相邻同胞选择器：h2 + p//同一个父元素下某个元素之后的元素。

