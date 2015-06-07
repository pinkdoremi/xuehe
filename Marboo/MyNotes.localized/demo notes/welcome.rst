====
欢迎
====

.. |date| date:: 2012-08-31
.. title:: 欢迎页
.. author: amoblin
.. publish: YES

约定
=====

* 文件名，新建笔记时确定
* 第二行，新建笔记时生成，并显示在中栏
* title定义：自定义名，发布博客时用

第二行是标题，例如：

.. code-block:: restructuredtext

    1 ====
    2 欢迎
    3 ====

STEP 1
=======

通过任何方法在当前目录添加文件夹，MarkBook会同步显示。

添加后缀为rst的restructureText文件，MarkBook会显示对应的HTML页面。

双击rst文件可使用编辑器打开rst文件。


以下是各个语言的样例代码：

终端代码
--------

.. code:: console

    $ echo "hello, world!"

c代码
-------

.. code-block:: c

    #include <stdio.h>

    int main() {
        char* a[3];
        int i;

        a[0] = "你好";
        a[1] = "hello";
        a[2] = "world!";

        printf("a's address is: %p\n", a);
        for(i=0; i<3; i++) {
            printf("%p: %s\n", a[i], a[i]);
        }
    }

.. sourcecode:: c

    #include <stdio.h>
    int main() {
        printf("hello, world!\n");
        return 0;
    }

.. code-block:: sh

    #!/bin/sh
    echo "hello"

python代码
-----------

.. code-block:: python

    import os
    import sys
    os.system("cat hello")

git diff代码
-------------

.. code-block:: diff


    diff --git a/src/maya_db_handler.py b/src/maya_db_handler.py
    index da246ca..967ceb6 100644
    --- a/src/maya_db_handler.py
    +++ b/src/maya_db_handler.py
    @@ -391,7 +391,7 @@ class MayaDBGroupHandler(DBHandler):
     
         def get_groups_by_app(self, app_id):
             ''' get db groups by app id '''
    -        sql = "select ta.id as cluster_id,ta.name as cluster_name,ta.idc,tb.id as app_id,tb.app_name,tc.id as db_group_id,tc.group_name,tc.mysql_db_name from maya_cluster ta, maya_app tb ,maya_db_group tc where ta.id= tb.cluster_id and tc.app_id= tb.id and tb.id = %s"  % app_id
    +        sql = "select ta.id as cluster_id,ta.name as cluster_name,ta.idc,tb.id as app_id,tb.app_name,tc.id as db_group_id,tc.group_name as name,tc.mysql_db_name from maya_cluster ta, maya_app tb ,maya_db_group tc where ta.id= tb.cluster_id and tc.app_id= tb.id and tb.id = %s"  % app_id
             return self.query(sql)
     
         def get_groups_by_name(self, group_name):

.. ############
.. ************
.. ============
.. ------------
.. ^^^^^^^^^^^^
.. """"""""""""

