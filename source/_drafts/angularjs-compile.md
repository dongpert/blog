---
title: 'angularjs-compile'
tags:
---
## Directive Definition Object
### scope
If set to true, then a new scope will be created for this directive. If multiple directives on the same element request a new scope, only one new scope is created. The new scope rule does not apply for the root of the template since the root of the template always gets a new scope.

If set to {} (object hash), then a new "isolate" scope is created. The 'isolate' scope differs from normal scope in that it does not prototypically inherit from the parent scope. This is useful when creating reusable components, which should not accidentally read or modify data in the parent scope.

The 'isolate' scope takes an object hash which defines a set of local scope properties derived from the parent scope. These local properties are useful for aliasing values for templates. Locals definition is a hash of local scope property to its source:

* @ or @attr - bind a local scope property to the value of DOM attribute. The result is always a string since DOM attributes are strings. If no attr name is specified then the attribute name is assumed to be the same as the local name. Given <widget my-attr="hello {{name}}"> and widget definition of scope: { localName:'@myAttr' }, then widget scope property localName will reflect the interpolated value of hello {{name}}. As the name attribute changes so will the localName property on the widget scope. The name is read from the parent scope (not component scope).

* @ 또는 @attr - DOM 속성의 값에 로컬 스코프 프로퍼티를 바인딩합니다. 결과는 DOM 속성들은 string이기 때문에 항상 string입니다.

* = or =attr - set up bi-directional binding between a local scope property and the parent scope property of name defined via the value of the attr attribute. If no attr name is specified then the attribute name is assumed to be the same as the local name. Given <widget my-attr="parentModel"> and widget definition of scope: { localModel:'=myAttr' }, then widget scope property localModel will reflect the value of parentModel on the parent scope. Any changes to parentModel will be reflected in localModel and any changes in localModel will reflect in parentModel. If the parent scope property doesn't exist, it will throw a NON_ASSIGNABLE_MODEL_EXPRESSION exception. You can avoid this behavior using =? or =?attr in order to flag the property as optional.

* & or &attr - provides a way to execute an expression in the context of the parent scope. If no attr name is specified then the attribute name is assumed to be the same as the local name. Given <widget my-attr="count = count + value"> and widget definition of scope: { localFn:'&myAttr' }, then isolate scope property localFn will point to a function wrapper for the count = count + value expression. Often it's desirable to pass data from the isolated scope via an expression to the parent scope, this can be done by passing a map of local variable names and values into the expression wrapper fn. For example, if the expression is increment(amount) then we can specify the amount value by calling the localFn as localFn({amount: 22}).
