# flux的概念(译)
> 原文地址：[flux-concepts](https://github.com/facebook/flux/blob/master/examples/flux-concepts/README.md)
> 我的英语水平不高。这是我第一次翻译英语文章，不足之处多多见谅…
 
当你在使用Flux写app的时候，你必须知道这些非常重要的高水平的概念与原则。

## 概览
Flux是一种在应用程序中管理数据流动模式的模式，其中最重要的概念是数据是单向流动的。在这篇文章中我们将会讨论使用Flux设计的应用程序的不同的部分，并且展示数据是如何在应用程序中单向流动的。

## Flux的几个部分
* Dispatcher
* Store
* Action
* View

## Dispatcher
Dispatcher接收Action并且将他们分发给在Dispatcher上进行过注册操作的Store。**每一个Store都会接收到每一个Action**(原文：Every store will receive every action)。在每一个应用程序中都应该只有一个单例的Dispatcher。

Example：
1. 用户在一个todo应用程序中输入标题并且按下回车；
2. View捕获到这个事件并且将该事件封装成包含用户输入的标题、名字为"add-todo"的Action，并将该Action分发出去；
3. 之后每一个Store都将接收到这个Action。

## Store
Store含有应用程序的数据。Store会向应用程序的Dispatcher注册以便Store能接收到View发出的Action。**Store内的数据只能通过响应Action来改变**(原文：The data in a store must only be mutated by responding to an action)。Store不应该含有公开的setter方法，应该只含有公开的getter方法。Store可以决定哪些Action是需要响应的。**每当Store的data发生改变，Store都应该广播出'change'的事件**(原文：Every time a store's data changes it must emit a "change" event)。在一个应用程序中应该有很多个Store。

Example:
1. Store收到View发出的"add-todo"的Action；
2. Store处理这个Action，从发送过来的Action内提取出用户输入的标题，并将其添加到今日需要完成的列表里；
3. Store更新数据并且发出一个'change'事件。


## Actions
应用程序内的API油Actions定义。它们捕获了任何可能与应用程序进行交互的方式。它们是包含有'type'字段与其他数据的、非常简单的object。
Actions应该是语义化并且具有描述性的。它们不应该详细描述它们在内部的执行细节。比如我们应该使用'delete-user'而不是使用'delete-user-id','clear-user-data','refresh-credentials'(或者其他任何过程化的工作)。请记住，Store会接受到'delete-user'的Action并且知道他们需要做'delete-user-id'，'clear-user-data'，'refresh-credentials'等操作。

Example:
1. 当用户点击删除按钮，一个名为'delete-todo'的Action被分发出去了：
```
{
  type: 'delete-todo',
  todoID: '1234',
}
```


## Views
数据从Store传来，并且在View上展示。在View层上你可以使用任何你喜欢的框架(我们更多的使用React)。**当一个View使用从Store那里获取到的数据的时候，它必须监听该Store的'change'事件**(原文：When a view uses data from a store it must also subscribe to change events from that store)。之后当Store触发'change'事件的时候，View可以立即获取到最新的数据并且重新渲染。如果一个组件使用了一个Store但是没有监听其'change'事件，那可能会具有一个等待被发现的Bug。Actions一般会通过用户的交互从View被分发出去。

Example:
1. 主界面监听了TodoStore；
2. 主界面接收到了Todo的列表并且将他们渲染在了界面上；
3. 当一个用户输入一个新的Todo项的标题并且按下回车之后，主界面告诉Dispatcher来分发这个Action；
4. 所有的Store都接受到了这个被分发的Action；
5. TodoStore处理了这个Action，并且将用户输入的标题加入到内部的数据结构中，并触发一个'change'事件；
6. 主界面监听到了'change'事件，并通过TodoStore获取到了最新的数据，并将其重新渲染到用户界面上。


## 数据流
我们可以将Flux对数据的部分处理转化为图表。
1. Views发送一个Action给Dispatcher；
2. Dispatcher分发Action给所有的Store；
3. Store响应事件，并且处理数据，将数据返回给View。

* (换句话说：Views从Store获取数据)
![demo](https://image.hduzplus.xyz/image/1500471261025.png)
(图中还有一个不是来源于View的操作，这也是很常见的)


## 下一步
在互联网上也有大量关于Flux架构的总结，如果需要的话可以自由的搜索更多你需要的资源。
另外，你可以开始尝试去写一个[flux-todomvc](https://github.com/facebook/flux/blob/master/examples/flux-todomvc)的例子，或者返回查看[example topics](https://github.com/facebook/flux/blob/master/examples)的列表。