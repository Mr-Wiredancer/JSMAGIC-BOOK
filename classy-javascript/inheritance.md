## 属性继承

如果接收到第二个参数（父类）的话，就操纵原型链来实现继承的目的

```javascript
...
function Class(data, parent) {
  const myClass = data.initialize || function() {};

  for (var fn in data) {
    if (fn !== "initialize")
      myClass.prototype[fn] = data[fn];
  }

  if (parent)
    myClass.prototype.__proto__ = parent.prototype;

  return myClass;
}
...
```

## __super__ 属性

```javascript
...
function Class(data, parent) {
  const myClass = data.initialize || function() {};

  for (var fn in data) {
    if (fn !== "initialize")
      myClass.prototype[fn] = data[fn];
  }

  if (parent)
    myClass.prototype.__proto__ = parent.prototype;

  myClass.__super__ = parent || Object;

  myClass.prototype.super = function() {

  }

  return myClass;
}
...

## 调用父类的方法

按照教程上面的提示，我们先看看第一个测试用例。简单的定义一个 super 方法：

```javascript
...
  myClass.__super__ = parent || Object;

  myClass.prototype.super = function() {};

  return myClass;
...
```

第二个用例是要能够调用父类的方法：

```javascript
...
  myClass.__super__ = parent || Object;

  myClass.prototype.super = function(fnName) {
    return parent.prototype[fnName]();
  };

  return myClass;
...
```

然后看第三个测试用例。这个稍微有点复杂，我们会发现我们在 super 方法里面直接调用了 parent.prototype[fnName] 这个函数，而不是传统的通过对象方法的方式去调用，这样的话函数体里面的 this 就丢失了。这时候我们要用 apply/call 来显式得声明 this:

```javascript
...
  myClass.__super__ = parent || Object;

  myClass.prototype.super = function(fnName) {
    return parent.prototype[fnName].apply(this);
  };

  return myClass;
...
```

啊，我们还忘了把参数传进去。这里我们可以用到 arguments 这个变量，这个对象储存的是当前函数调用的参数。如果它是一个数组，那么我们可以直接用操作数组的方法来操作它。但它不是数组，只是一个类似数组的对象。所以这里我会用一个小技巧：

```javascript
...
  myClass.__super__ = parent || Object;

  myClass.prototype.super = function(fnName) {
    let args = Array.prototype.slice.call(arguments, 1)
    return parent.prototype[fnName].apply(this, args);
  };

  return myClass;
...
```

## 调用父类的父类的方法

所以问题在于，在父类方法里面，this 还是原来的实例，所以在父类的 super() 里面的 this.super() 还是指向父类的 super()。所以这个时候，我们需要引入另一个变量，用来告诉我们到底处于哪一个层级：

```javascript
...
  myClass.__super__ = parent || Object;

  let current = myClass

  myClass.prototype.super = function(fnName) {
    let args = Array.prototype.slice.call(arguments, 1)

    current = current.__super__;

    let result = current.prototype[fnName].apply(this, args);

    return result;
  };

  return myClass;
...
```

这时候我们来跑一下测试，发现第二个测试没有通过。去看一下测试的代码：

```javascript
...
    assert.equal(c.foo(1), 200);
    assert.equal(c.foo(1), 200);
...
```

然后再看我们的实现，发现我们的 super 只能调用一次，再下一次再调用的时候，current 的值是不正确的。所以我们每次完成调用之后要把 current 改回去：

```javascript
...
  myClass.__super__ = parent || Object;

  let current = myClass

  myClass.prototype.super = function(fnName) {
    let args = Array.prototype.slice.call(arguments, 1)

    let save = current;
    current = current.__super__;

    let result = current.prototype[fnName].apply(this, args);

    current = save;

    return result;
  };

  return myClass;
...
```

这样就完成啦

