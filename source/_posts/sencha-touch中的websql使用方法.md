title: 'sencha touch中的websql使用方法'
date: 2014-08-17 01:38:26
tags:
- websql
- sencha-touch
categories:
- sencha touch

---
html5中有个websql的技术可以在浏览器中使用数据库，这个技术现在在sencha touch中可以被使用，而且已经封装好了，存在一个proxy了。但是这个方法在官方文档中介绍极少，使用起来难免一头雾水，我也是经过很久的研究才渐渐磕磕碰碰懂得了很多这其中的因果缘由，拿出来和大家分享啊！
<!--more-->
### 创建一个使用websql存储数据的store&model
#### store
    Ext.define('MyApp.store.testStore',{
        extend:'Ext.data.Store',
        requires: [
            'Ext.data.proxy.Sql'
        ],
        config:{
            model:'MyApp.model.testModel',
            storeId:'testStore',
            proxy:{
                type:'sql',
                database:'testDatabase',
                table:'testTable'
            }
        }
    });
#### model
    Ext.define('MyApp.model.testModel',{
        extend:'Ext.data.Model',
        requires:[
            'Ext.data.Field'
        ],
        config:{
            fields:[
                'id',
                {
                    name:'title',
                    type:'string'
                }
            ],
            identifier:{type:'uuid'},
        }
    });
#### 具体的说明：
1. Store: `proxy`中的`type`定为`sql`，同时，`requires`里面要添加`Ext.data.proxy.Sql`。
2. Store: `proxy`中的`database`为你需要创建的数据库名称。`table`为你需要创建的对应这个store的表的名称。
3. Store: `reader`在普通的`load`操作中不需要使用到。不过当你需要`Store.addData(data)`为store添加数据的时候，需要用到`reader`来将你参数`data`转换成`model`再自动通过`Store.add(model)`的方法来添加进`store`。这种情况就根据你的需求自己更改`reader`啦。
##### 如果你出现了id无法存储的情况：
4. Model: model要注意的如果你存储的数据需要有id字段，那你必须要设置`identifier`,不能设置别的必须设置`uuid`。因为只有`uuid`有一个方法`isUnique`，而sqlproxy在`updateModel`中是这么写的：

>     updateModel: function(model) {
>         if (model) {
>             var modelName = model.modelName,
>                 defaultDateFormat = this.getDefaultDateFormat(),
>                 table = modelName.slice(modelName.lastIndexOf('.') + 1);
> 
>             model.getFields().each(function (field) {
>                 if (field.getType().type === 'date' && !field.getDateFormat()) {
>                     field.setDateFormat(defaultDateFormat);
>                 }
>             });
> 
>             this.setUniqueIdStrategy(model.getIdentifier().isUnique);
>             if (!this.getTable()) {
>                 this.setTable(table);
>             }
>             this.setColumns(this.getPersistedModelColumns(model));
>         }
> 
>         this.callParent(arguments);
>     },

在这里又有一个很关键的属性:`proxy.uniqueIdStrategy`这是一个私有的属性，我们在给类定义的时候如果为该值直接赋值是无效的，会在这个函数中修改掉之前定义的值。
而uniqueIdStrategy和这里的columns会直接影响到之后把数据存储到websql中时，**id这个字段会不会被存储**。
所以在这里唯一没有违和感的解决办法就是在model中设置：`identifier:{type:'uuid'}`

PS：这个问题研究了很久，网上都没有解决办法。所有的国内国外的数据都没有涉及，国内的压根没写到websql，国外的也只有几本略微带过websql，详细写过几行代码的，只有使用model直接操作websql，没有使用到store来存储数据。
### 常见问题解答：
#### 1. 数据没有被添加（log sync的时候add的内容为0/没有目标record）
当你的record已经添加进store了（store.data中已经存在这个数据）但是同步时没有被同步进去，说明你的record被系统判断并不是新添加的记录。这很有可能发生，不对，是经常容易发生，只要你不是Ext.create出来的新record，它的phantom值都是false。（基本上都是这样的，我也没有一个一个去试过什么方法不是false，因为我发现这个问题以后就强迫在同步前为新数据`phantom = true`）**phantom**：代表这个record是否是数据库中不存在的，是则为false，已经存在了则为true。如果我们要新添加一个record，最好手动为他赋值`record.phantom = true`。
#### 2. 存入的id值有问题
这个问题我自己也没有办法解决。比如一个id为1，存进去的数据就是1.0，而且取出来是string格式的，很奇怪。但是按照源代码手动去数据库`INSERT`操作，存进去的就是纯数字。。。
#### 3. 无法存入id
请见具体说明的第四点。（在上面⬆）
#### 4. 数据存不进去了？
数据存不进去的时候，很有可能是因为是有的int类型的字段，你没有为他赋值。比如说有个年龄，压根就没写进去。本来想想不存也可以啊，这个字段本身不是说必须要有啊。但是senchatouch在操作websql中，执行的语句是这样的：
`transaction.executeSql(a,b)`
其中a是sql语句，但是a中的描述value部分是这样的(?,?,?,?,?,?)而b这个数组会按照顺序替换前面的问号，b就是存储的真正的value的值。如果你的年龄没有写，年龄就没有被替换，就是？,但是？不是数字格式的，在存入时就会失败。
有时候可能也不是你的int没写导致存不进去，别的没写，但是你的b因为少了数据错位了，别的int存到了？，就错了，所以最好还是每个值都要赋。
#### 5. 希望一下子读取所有的数据
这是很正常的想法，本地的数据库不需要像请求远程的数据库那样怕消耗太多流量而限制一次load的数据。
sencha本身一次load的数据是25条一页，一次load一页。
只需要在load操作之前设置下：
`store.currentPage = undefined;`就可以了。
因为在数据库读取操作中：
`SELECT * FROM .........`
如果不设置这个currentPage = undefined，sencha就会为你添加`LIMIT`在这个语句后面，这会导致限制一次读取的数据。
### 总结
sencha touch 自带的websql的proxy总的来说还是比较完善的，但是这部分的官方说明很少。详细的内容可能需要经常查阅源代码来了解。
很多问题可以通过慢慢地debug来了解这其中的机制和顺利地解决。
PS：能debug的地方就没有难题！模拟器这种不能debug的地方才是地狱！！！！！！！！！！！！！

