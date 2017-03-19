## rxjava认识

### 什么是RxJava？

RxJava 在 GitHub 是这样说的:

 "a library for composing asynchronous and event-based programs using observable sequences for the Java VM"

什么意思呢？ 一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库。

其实， RxJava 的本质可以压缩为"异步" 这一个词。它就是一个实现异步操作的库.

### RxJava介绍和基本使用

由于RxJava 的异步实现，是通过一种扩展的观察者模式来实现的。

#### 扩展的观察者模式概念

先简述一下观察者模式：

比如：
A 对象（观察者）对 B 对象（被观察者）的某种变化高度敏感，需要在 B 变化的一瞬间做出反应

再比如：

警察抓小偷，警察需要在小偷伸手作案的时候实施抓捕。
警察是观察者，小偷是被观察者，警察需要时刻盯着小偷的一举一动，才能保证不会漏过任何瞬间

需要注意:

程序的观察者模式和以上的『观察』略有不同。

**程序的观察者不需要时刻盯着被观察者（例如 A 不需要每过 2ms 就检查一次 B 的状态），而是采用称为订阅(Subscribe)的方式，告诉被观察者：我需要你的某某状态，你要在它变化的时候通知我**

例如：
 Android 开发中一个比较典型的例子是点击监听器 OnClickListener 。对设置 OnClickListener 来说， View 是被观察者， OnClickListener 是观察者，二者通过 setOnClickListener() 方法达成订阅关系。订阅之后用户点击按钮的瞬间，Android Framework 就会将点击事件发送给已经注册的 OnClickListener 。
 
**采取这样被动的观察方式，既省去了反复检索状态的资源消耗，也能够得到最高的反馈速度**
 
 

OnClickListener 的模式大致如下图：

![](/assets/rxjava-01.png)

如图所示:

通过 setOnClickListener() 方法，Button 持有 OnClickListener 的引用（这一过程没有在图上画出）
当用户点击时，Button 自动调用 OnClickListener 的 onClick() 方法。

另外，如果把这张图中的概念抽象出来:

    Button -> 被观察者、
    OnClickListener -> 观察者、
    setOnClickListener() -> 订阅函数，
    onClick() -> 事件


#### RxJava 相关概念

RxJava 有四个基本概念：

- Observable (被观察者)
- Observer (观察者)
- subscribe (订阅)事件
- 事件

      onNext(): 普通事件,相当于onClick() / onEvent()
      onCompleted():队列完结事件
      onError(): 队列异常事件


Observable 和 Observer 通过 subscribe() 方法实现订阅关系，从而 Observable 可以在需要的时候发出事件来通知 Observer。

与传统观察者模式不同， RxJava 的事件回调方法除了普通事件 onNext() （相当于 onClick() / onEvent()）之外，还定义了两个特殊的事件：onCompleted() 和 onError()。


onCompleted(): 
事件队列完结。RxJava 不仅把每个事件单独处理，还会把它们看做一个队列。RxJava 规定，当不会再有新的 onNext() 发出时，需要触发 onCompleted() 方法作为标志。

onError(): 
事件队列异常。在事件处理过程中出异常时，onError() 会被触发，同时队列自动终止，不允许再有事件发出。

在一个正确运行的事件序列中, onCompleted() 和 onError() 有且只有一个，并且是事件序列中的最后一个。需要注意的是，onCompleted() 和 onError() 二者也是互斥的，即在队列中调用了其中一个，就不应该再调用另一个。


