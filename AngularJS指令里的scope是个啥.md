# AngularJS指令里的scope是个啥

directive里，scope有三种取值，true/false/{}。

## scope的三种取值
### false(默认值)
和指令所在作用域相同，即共享作用域。

### true
新建一个作用域，原型继承指令所在controller作用域。

### {}
新建一个隔绝作用域，不继承指令所在controller作用域。可以通过 scope.$parent 使用controller的作用域，但更推荐使用 @、&、= 来获取controller的作用域属性值。


## {}的三种符号 
### @
单向，字符串。绑定一个局部 scope 属性到当前 dom 节点的属性值。结果总是一个字符串，因为 dom 属性是字符串。

### &
单向，表达式。提供一种方式执行一个表达式在父 scope 的上下文中。如果没有指定 attr 名称，则属性名称为相同的本地名称。

### =
双向，字符串/表达式。通过 directive 的 attr 属性的值在局部 scope 的属性和父 scope 属性名之间建立双向绑定。


## 失效的绑定
```javascript
.directive('abc',() => {
  restrict:'E',
  scope: {
    a:'='
  },
  link(scope) {
    scope.a = false;
  }
}
})
```

```HTML
<abc ng-show="isA" a="isA"></abc>
```

上面的例子，会发现原本双向绑定的a与isA，在link函数中修改值后，并未生效。

原因是，新作用域的值只是继承，并未产生引用，所以在新作用域内的修改未反映到原作用域。

这一情况，使用对象来存储值即可解决。如下：
```javascript
.directive('abc',() => {
  restrict:'E',
    scope: {
      a:'='
    },
    link(scope) {
      scope.a.isA = false;
    }
  }
})
```
```HTML
<abc ng-show="a.isA" a="a"></abc>
```
