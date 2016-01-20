title: redux心得
date: 2015-11-19 00:30:33
tags:
- redux
- react
categories:
- redux
---

## redux介绍
redux是flux的一种实现方案,相较于flux,更加清晰易用,它可以实现日志打印,热加载,同构应用,录制回放..

+ <https://github.com/happypoulp/redux-tutorial> 非常清晰的教程,但是说的不深入,速成上手用
+ <http://camsong.github.io/redux-in-chinese/> 官方文档的中文版
+ <http://rackt.github.io/redux/> 官方文档的英文原版
+ <https://github.com/xgrommx/awesome-redux> 各种redux相关网站的收录

本文接下来说提到的一些词汇(state,action,reducer,action creator,dispatch,store,middleware)可以通过下面找到解释:
+ 中文:<http://camsong.github.io/redux-in-chinese/docs/Glossary.html>
+ 英文:<http://rackt.org/redux/docs/Glossary.html>

### [flux](https://facebook.github.io/flux/)
flux也是一种强调单向数据流的架构思想,也可以说是一种模式.它的单向数据流如下图所意:
![](https://facebook.github.io/flux/img/flux-simple-f8-diagram-with-client-action-1300w.png)
在他之后的有redux,reflux...这些都是借鉴了flux的思想,细看他们的思想和架构大体上都是相似的.

#### 与redux的区别一
flux的dispatch是在action中直接调用的

```
var TodoActions = {
  create: function(text) {
    AppDispatcher.dispatch({
      actionType: 'TODO_CREATE',
      text: text
    });
  },
 };
TodoActions.create('test');
```

而redux的action,实际上是action creator,把action creator和store的dispatch绑定起来后，直接调用action creator的产物会被直接dispatch出来。

<!--more-->

```
function addTodo(text) {
  return { type: ADD_TODO, text };
}

store.addTodo('test')


//这是redux源码中的绑定的代码。
function bindActionCreator(actionCreator, dispatch) {
  return (...args) => dispatch(actionCreator(...args));
}
```

所以说，在redux中，是没有flux的dispatcher的。在没有了dispatcher这个东东之后，明显架构的复杂性又降低了很多。

#### 与redux的区别二
redux和flux的另一个重要的区别就是。redux对store的更新，每次一次更新都是一个新的对象，而flux的更新是修改旧的对象：

```
//flux
function create(text) {
  var id = +new Date();
  _todos[id] = {//这个_todo就是store
    id: id,
    complete: false,
    text: text
  };
}
//redux
function todos(state = initialState, action) {
  switch (action.type) {
    case ADD_TODO:
      return [//返回的就是生成的新的store,显然是个新对象
        {
          id: state.reduce((maxId, todo) => Math.max(todo.id, maxId), -1) + 1,
          completed: false,
          text: action.text
        }, 
        ...state//es6的语法，意思是把老的state中的所有元素插入到这个新的数组中。
      ]
    }
}
```

其他的区别就是细节操作上的区别，其实思路都相似就是实现的具体方式不同罢了。
redux比flux更好的一点就是redux已经生成了不错的生态圈了，使用的人渐渐增多并且口碑也都不错，毕竟这是一个flux实现非常不错的架构。

### [redux dev tool](https://github.com/gaearon/redux-devtools)
开发redux得力的助手.可以查看当时的state和触发的ation.并且可以任意撤销/前进.
![](https://camo.githubusercontent.com/a0d66cf145fe35cbe5fb341494b04f277d5d85dd/687474703a2f2f692e696d6775722e636f6d2f4a34476557304d2e676966)
安装方法详见官方文档与demo.


### 单向数据流
由于应用的复杂性的提升,不同组件之间state和model的相互影响和数据缓存啊等等东西也会越来越复杂,所以会产生类似于[`Flux`](http://facebook.github.io/flux/docs/overview.html)这些提供单向数据流的架构.redux和Flux在这点上的思想是一致的.

#### store
所以`redux`全应用只有一个`store`,用于存储`state`,更新`state`(发起`action`),以及提供对`state`的监听.所有的上层组件的状态变化都跟随这一个`store`.所以数据是从`store`单向流出的.
下面是`store`的创建过程:

```
import { createStore } from 'redux';
import todoApp from './reducers';

let store = createStore(todoApp);
```

所有组件,view的状态来自store的`state`,而用户交互操作,异步请求又如何去更新这个store呢?
#### Action
这里使用了action的方式来触发动作,Action是store生成后唯一的数据来源,只能通过action来改变store的状态.通过`store.dispatch()`把 action 传到 store。
action是这样的东西:

```
{
  type: 'ADD_TODO',
  text: 'Build my first Redux app'
}
```

#### reducer
`store.dispatch(action)`后store会把这个action交给reducer去处理.reducer就是专门针对不同的action,进行不同的数据处理,最后返回出来对象就是新的state,如果state有变化,这个返回的结果就必须是个新对象,而不是在原来的state上进行的修改.

```
function todos(state = [], action) {
  switch (action.type) {
  case ADD_TODO:
    return [...state, {
      text: action.text,
      completed: false
    }];
  case COMPLETE_TODO:
    return [
      ...state.slice(0, action.index),
      Object.assign({}, state[action.index], {
        completed: true
      }),
      ...state.slice(action.index + 1)
    ];
  default:
    return state;
  }
}
```

reducer还负责store状态的初始化,store在创建的时候reducer会执行一遍,而且是不带action参数的,此时返回的state即时初始化后的状态.

reducer进行了数据处理后,改变了store的状态.导致view,组件重新收到了新的props,于是就会产生view上的变化.这就是redux的单向数据流.

这是官方的todolist的[demo](http://rackt.org/redux/docs/basics/ExampleTodoList.html)
## 组件抽离
让`view`使用`redux`而让`component`独立于`redux`存在,从而让`component`可以被抽离.

方法是:让`component`尽量只读取`props`来渲染,不要去依赖组件自己的`state`.让`view`调用`action`,并且直接读取`store`的数据.因此,[`connect`](http://camsong.github.io/redux-in-chinese/docs/api/bindActionCreators.html)最好只应该存放在`view`上

## 异步数据流以及middleware
对于一开始接触redux的异步需求,不知该如何下手.下面一顿凌乱的异步取数据的思考....
>
如果我希望有个用来获取数据的名叫`getData`的`action`,我触发了这个`action`之后,需要再触发一个`ajax`请求,在`ajax`请求结束之后再触发一个拿到数据`gotData`的`action`.嗯.思路很好....于是,我写一个`getData` 的 `actioncreator`,但是返回什么`action`呢,`reducer`拿到这个`action`后,还没有拿到目标数据,并不能对`store`进行任何修改,这个`reducer`没有任何意义.先不管...那我要在触发`getData`这个`action`的过程中顺便发出`ajax`请求,然后在回调函数中再`dispatch`一个`gotData`的`action`.但是作用域中的`dispatch`哪来呢,`action`中的作用域并没有`dispatch`.....
>

再看官方介绍的异步[demo](http://camsong.github.io/redux-in-chinese/docs/advanced/ExampleRedditAPI.html),按照官方介绍的方式成功解决了问题,细看官方文档和源码,突然间就顺便懂了[`middleware`](http://camsong.github.io/redux-in-chinese/docs/api/applyMiddleware.html)是个什么的东西.

一些异步的操作是没办法通过`actionCreator`直接返回`action`的.所以在`redux`中可以通过`Middleware`来实现异步的`action`.它可以在**`ACTION`发起之后,到达`reducer`之前**进行他的操作. 

### redux-thunk
官方用于异步`action`的`redux-thunk`就作用在`action creator`生产了一个匿名函数被`dispatch`之前的时候:

```
//Action Creator
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  };
}
//被dispatch
store.dispatch(addTodo(text));
```
观察源码得知,`dispatch`是创建`Store`(即`createStore`的时候),store暴露出来的一个方法.只能从创建好的`store`中获得,是`store`的一个方法.它的作用是拿取`Action`对象(不可以是别的类型),把当前的`state`和`Action`一起交给`reducer`来处理.这个过程是同步的.

这样的话,要让`action`真正地被`dispath`之前,被拦截下来.于是,`middleware`就有用了,顺便能理解一下它到底是干嘛用的.

**`middleware`通过给`dispatch`包装了一层**,使得`dispatch`可以接受匿名函数作为参数.`redux-thunk`就是用来干这个的`middleware`:

当`dispatch`接收到了`actionCreator`创建的东西后,如果得知它是个匿名函数,则会把`dispatch`和`state`一起作为参数把这个`function`给调用了,而不会触发原来的`dispatch`了.有了`dispatch`在作用域之后匿名函数的就能想怎么`dispatch`就怎么`dispatch`了!

所以最终的结果就是.如果`action creator`返回了一个匿名函数就会直接执行它并且是自带`(dispatch,state)`参数的!

这是官方异步数据流的[demo](http://rackt.org/redux/docs/advanced/ExampleRedditAPI.html)

## 使用感受
### 好处
+ 只有view这些页面级别的组件需要使用redux,关注业务逻辑,其他的组件都能够脱离redux独立使用,非常适合迁移.
+ 用redux进行的业务数据的处理会让逻辑非常得非常得非常得清晰!!!!!
+ redux的生态圈还都不错.

### 抱怨
+ 写action花费的时间很多,普通的一个异步请求只要发请求->回调->重新渲染模板就能完成.而redux需要.定义action->写触发请求的action的代码->写发请求的actioncreator->写拿到数据的actioncreator->写处理这个两个action的reducer

