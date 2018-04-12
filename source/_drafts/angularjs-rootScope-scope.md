---
title: angularjs-rootScope-scope
tags:
---
### $watch(watchExpression, listener, [objectEquality]);
Registers a listener callback to be executed whenever the watchExpression changes.

* The watchExpression is called on every call to $digest() and should return the value that will be watched. (watchExpression should not change its value when executed multiple times with the same input because it may be executed multiple times by $digest(). That is, watchExpression should be idempotent.)
* The listener is called only when the value from the current watchExpression and the previous call to watchExpression are not equal (with the exception of the initial run, see below). Inequality is determined according to reference inequality, strict comparison via the !== Javascript operator, unless objectEquality == true (see next point)
* When objectEquality == true, inequality of the watchExpression is determined according to the angular.equals function. To save the value of the object for later comparison, the angular.copy function is used. This therefore means that watching complex objects will have adverse memory and performance implications.
* This should not be used to watch for changes in objects that are or contain File objects due to limitations with angular.copy.
* The watch listener may change the model, which may trigger other listeners to fire. This is achieved by rerunning the watchers until no changes are detected. The rerun iteration limit is 10 to prevent an infinite loop deadlock.

* $digetst()를 호출할 때마다 watchExpression는 호출되고 감시되는 값을 반환해야 합니다.(watchExpression은 $digest()에 의해 여러번 실행될 수 있기 때문에 같은 입력으로 여러번 실행될때 값을 변경해서는 안됩니다.)
* 리스너는 현재 watchExpression의 값과 이전에 호출한 watchExpression이 같지 않을때에만 호출됩니다.(초기 실행은 예외 입니다.  불균등은 불균등을 참조하여 결정됩니다. objectEquality == true가 아닌한 !== 자바스크립트 연산자를 통하여 엄격한 비교)
* objectEquality == true이면 watchExpression의 불균등은 angular.equals 함수에 따라 결정됩니다. 나중에 비교를 위해 객체의 값을 저장하기 하기위해서는 angular.copy 함수가 사용됩니다. 복잡한 객체를 감시하는것은 불리한 메모리와 성능 영향이 있을 수 있습니다.
* angular.copy의 한계때문에 파일 객체이거나 포함하는 객체의 변경을 감시하는데 사용해서는 안됩니다.
* watch 리스너가 모델을 변경하면 다른 리스너가 트리거 될 것입니다. 변경사항이 감지되지 않을때까지 감시자를 재실행하여 수행됩니다.

If you want to be notified whenever $digest is called, you can register a watchExpression function with no listener. (Be prepared for multiple calls to your watchExpression because it will execute multiple times in a single $digest cycle if a change is detected.)

After a watcher is registered with the scope, the listener fn is called asynchronously (via $evalAsync) to initialize the watcher. In rare cases, this is undesirable because the listener is called when the result of watchExpression didn't change. To detect this scenario within the listener fn, you can compare the newVal and oldVal. If these two values are identical (===) then the listener was called due to initialization.
