title: sencha-touch自定义restful接口的proxy
date: 2014-09-30 23:50:06
tags:
- restful
- proxy
- store
- sencha-touch
categories:
- sencha-touch
---
#sencha touch 自定义restful的proxy

使用sencha-touch的store时，有时数据来自rest的接口，因为不同服务器rest的不同，我们需要自己来定制属于自己的proxy。
<!--more-->
###1.为何每次load到只有一个数据
rootproperty设置了没有？
默认值是：''
如果不设置的话，就默认取到的responseText的root部分，既然是root部分肯定就是一个数据了。
所以一定要根据自己项目的具体要求设置一下。例如：
responseText：

    {
      "total_rows": 54,
      "offset": 0,
      "rows": [
        {
          "key": "running",
          "id": "0209D576-F1F5-4760-85CC-A72126F13FC8",
          "doc":{
              "distance":122,
              "duration":12
          }
        },
        {
          "key": "running",
          "id": "0A581D9A-95BD-40F3-BA62-6D54069F432F",
          "doc":{
              "distance":123,
              "duration":12
          }
        }
      ]
    }

这里我需要的rows就是我的rootProperty。
我们只需要在proxy中的reader这么设：

    proxy:{
        reader:{
            type:'json',
            rootProperty:'rows'
        }
    }

###2. 数据格式奇葩怎么办
类似我上面的那个数据格式：

        {
          "key": "running",
          "id": "0A581D9A-95BD-40F3-BA62-6D54069F432F",
          "doc":{
              "distance":123,
              "duration":12
          }
        }

我的model其实对应的是doc下面的内容：

        {
            "distance":123,
            "duration":12
        }

没关系。重写一下reader就好了。
reader有一个方法是：`extractData`
这个方法是把读来的每个json数据转化格式交付给别人再处理成model。
我修改了一下：

        for (i = 0; i < length; i++) {
            clientId = null;
            id = null;
            
            //修改此处以符合我们的接口：
            //原句：node = root[i];
            node = root[i].doc || root[i];

            // When you use a Memory proxy, and you set data: [] to contain record instances
            // this node will already be a record. In this case we should not try to extract
            // the record data from the object, but just use the record data attribute.
            if (node.isModel) {
                data = node.data;
            } else {
                data = me.extractRecordData(node);
            }

            if (data._clientId !== undefined) {
                clientId = data._clientId;
                delete data._clientId;
            }

            if (data[idProperty] !== undefined) {
                id = data[idProperty];
            }

            if (me.getImplicitIncludes()) {
                 me.readAssociated(data, node);
            }

            records.push({
                clientId: clientId,
                id: id,
                data: data,
                node: node
            });
        }

这样就能够正常读取我们的数据，并且读一般的数据的时候也不会有错。
##3.定制自定义的restful的proxy
理想化的情况下，我们希望创建一个store，使用的proxy是可以匹配我们服务器的restful接口.
sencha-touch为我们提供了一个非常方便重写的restproxy，我们可以根据自己的需要定制自己的借口。

###buildUrl
通过buildUrl方法可以定制我们自己的路径URI。

    buildUrl: function(request, readurl) {
        var me = this,
            operation = request.getOperation(),
            records = operation.getRecords() || [],
            record = records[0],
            model = me.getModel(),
            idProperty = model.getIdProperty(),
            format = me.getFormat(),
            url = me.getUrl(request),
            params = request.getParams() || {},
            id = (record && !record.phantom) ? record.getId() : params[idProperty];
        if (request.getAction( ) === 'read') {
            if (me.getDesId()) {
                url += '/_design/' + me.getDesId();
            }
            if (me.getView()) {
                url += '/_view/' + me.getView();
            }
        } else {
            if (me.getAppendId() && id) {
                if (!url.match(/\/$/)) {
                    url += '/';
                }
                url += id;
                delete params[idProperty];
            }
        }

        request.setUrl(url);

        return me.callParent([request]);
    }

以上是我初步为我们使用的couchbase定制而重写的buildUrl。
这里在sencha本身的rest proxy 的定义之外附加了几个config属性可以作为随时可变化的参数：

    config:{
        desId: null,
        view: null
    }

如果是添加过多余的属性的话，就不能使用重写(overide)，要使用继承(extend)。这里没办法只能继承`'Ext.data.proxy.Ajax'`。

###定制参数名称


        directionParam:'descending',
        pageParam:false,
        startParam:false,
        actionMethods: {
            create: 'POST',
            read: 'GET',
            update: 'PUT',
            destroy: 'DELETE'
        },
    
定制自己的restful接口时，有时还需要根据服务器的接口参数的表达方式而改变参数名称。这也是要写在proxy中，其中还有很多的参数可以详见官方API。

##总结
定制自己的proxy可以很方便的使用store的同时对数据进行增删查改并且同步服务器，又和list，dataview绑定。
