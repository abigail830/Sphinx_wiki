1.5. About RxJS
==========================

Website: https://rxjs-dev.firebaseapp.com/guide/overview

  Rx是一个基于可观测序列处理组合异步与事件驱动的库

关键概念：
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


Observable
-----------------

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
  
  const foo = new Observable(subscriber => {
    console.log('Hello');
    
    // it can return multiple value synchronously
    subscriber.next(42);
    subscriber.next(100);
    subscriber.next(200);
    
    // happens asynchronously
    setTimeout(() => {
      subscriber.next(300); 
    }, 1000);
  });

* Create Observable
* Subscribe Observable
* Executing Observable

  * "Next" notification: sends a value such as a Number, a String, an Object, etc.
  * "Error" notification: sends a JavaScript Error or exception.
  * "Complete" notification: does not send a value.

* Dispose Observable

.. code-block:: javascript
  
  //provider
  const observable = new Observable(function subscribe(subscriber) {
    try {
      subscriber.next(1);
      subscriber.next(2);
      subscriber.complete();
    } catch (err) {
      subscriber.error(err); // delivers an error if it caught one
    }
  });
  
  //consumer
  const subscription = observable.subscribe(x => console.log(x));
  subscription.unsubscribe();



.. index:: RxJS, Angular
