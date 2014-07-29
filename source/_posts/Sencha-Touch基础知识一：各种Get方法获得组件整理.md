title: 'Sencha Touch基础知识一：获取一个组件方法整理'
date: 2014-07-28 22:33:20
tags:
- sencha touch
categories:
- sencha touch 基础知识
---

获取一个组件有很多种方法。本文将会介绍常用的：
- Ext.getCmp( )
- getComponent( )
- Ext.getDom()
- Ext.get()

<!--more-->
至于在controller中常用的使用ref获取组件的方法，我用的不多，因为我使用了Deft Js。

###Ext.getCmp("Id")
它会根据id值来查找一个已经存在的组件。
Ext.getCmp()中的参数只能是组件config中的id值，不能是itemId值。
它是Ext.ComponentMgr.get()的缩写版，当然啦，有缩写版干嘛还用复杂版~

###getComponent("itemId")
举个使用的例子：
`var son = father.getComponent('son');`
这个方法获取组件只能够获取父组件下的第一层子组件,**不能获得子组件的子组件**，也就是说父亲只能拿到儿子，不能拿到孙子。
这个方法**只能使用itemId值**。作为查找的参数，无法通过id值，要用id值请用上面的方法。

###Ext.getDom(el)
参数el可以是：String (id),dom node,Ext.Element.
返回dom node。也就是说：我以孙子做参数，可以返回父亲（如果父亲没有爸爸了，否则还会返回父亲的父亲的父亲。。。直到头）
返回值是：html元素

###Ext.get(element)
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
