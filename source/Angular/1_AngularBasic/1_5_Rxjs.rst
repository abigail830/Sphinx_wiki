1.5. About RxJS
==========================

Website: https://rxjs-dev.firebaseapp.com/guide/overview

  Rx是一个基于可观测序列处理组合异步与事件驱动的库

* `关键概念`_
* `Observable 可观察对象`_
* `Operator 操作`_
* `Subscription 侦听`_
* `Subject 主题`_


关键概念
^^^^^^^^^^^^

**纯函数 Pure Function**
没有副作用，比如不会一边改变值的大小，同时进行值的打印。每个函数都是一个单一职责。

.. code-block:: javascript
  
  //有副作用
  document.addEventListener('click', () => console.log(`Clicked ${++count} times`));
  
  //没有副作用
  fromEvent(document, 'click')
    .pipe(scan(count => count + 1, 0))
    .subscribe(count => console.log(`Clicked ${count} times`));


**流程控制 Flow**

比如 filter, delay, debounceTime, take, takeUntil, distinct, distinctUntilChanged 


**值转换 Value**

比如 map, pluck, pairwise, sample 


Observable 可观察对象
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+---------+---------+--------------+
|         | Single  |  Multiple    |
+=========+=========+==============+
| Pull    |Function | Iterator     |
+---------+---------+--------------+
| Push    |Promise  | Observable   |
+---------+---------+--------------+

对于promise来说，接受一个回调方法，里面会分别处理resolve（对应与then)/reject(对应于catch)两种场景：

.. code-block:: javascript
  
  const promise = new Promise(function(resolve, reject){
    if(/* success */){
      resolve(value);
    } else {
      reject(error);
    }
  });  

Observables are able to deliver values either synchronously or asynchronously.


.. code-block:: javascript
  
  //provider
  const observable = new Observable(function subscribe(subscriber) {
    try {
      // it can return multiple value synchronously
      subscriber.next(1);
      subscriber.next(2);
      
      // happens asynchronously
      setTimeout(() => {
        subscriber.next(300); 
      }, 1000);
      
      subscriber.complete();
    } catch (err) {
      subscriber.error(err); // delivers an error if it caught one
    }
  });
  
  //consumer
  const subscription = observable.subscribe(x => console.log(x));
  subscription.unsubscribe();

Above also showing 4 part of obseravable

* **Create Observable:** new, of, from, interval
* **Subscribe Observable:** .subscribe
* **Executing Observable**

  * "Next" notification: sends a value such as a Number, a String, an Object, etc.
  * "Error" notification: sends a JavaScript Error or exception.
  * "Complete" notification: does not send a value.

* **Dispose Observable:** .unsubscribe


Operator 操作
^^^^^^^^^^^^^^^^^^

https://rxjs-dev.firebaseapp.com/guide/operators


Subscription 侦听
^^^^^^^^^^^^^^^^^^^^^

.. code-block:: javascript
  
  //consumer
  const subscription = observable.subscribe({
    next(x) {
      console.log('got value ' + x)
    },
    error(err) {
      console.error('something wrong occurred: ' + err);
    },
    complete() {
       console.log('done');
    }
  });
  subscription.unsubscribe();

Subject 主题
^^^^^^^^^^^^^^^^^^^^

  一个主题就像一个可观察的对象，但是可以向多个观察者进行多播。主题就像事件发射器:它们维护许多侦听器的注册表。

* **BehaviorSubject**: 存着现有的值（如1），当有新的subscription, 然后有新值（如2）流进来的时候，现有的值（1）也会喷给这个新的subscribe.
* **ReplaySubject**: 类似BehaviorSubject，但可以进一步指定replay过去的多少个值。必要时，还可以指定window Time的时间限制
  const subject = new ReplaySubject(100, 500 /* windowTime */);
* **AsyncSubject**：同样类似BehaviorSubject, 但只在遇到.complete()的时候才会给新subject发送现有值


.. index:: RxJS, Angular
