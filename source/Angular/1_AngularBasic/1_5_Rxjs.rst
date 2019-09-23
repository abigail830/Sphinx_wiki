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



.. index:: RxJS, Angular
