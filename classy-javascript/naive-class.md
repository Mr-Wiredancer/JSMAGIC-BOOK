```bash
$ mkdir classy-js & cd classy-js

# 进入 classy-js 目录
classy-js$ npm init -f
classy-js$ curl https://www.gitignore.io/api/node > .gitignore
classy-js$ git init
classy-js$ git add .
classy-js$ git commit -m 'init'

#设置测试套件，可以用来验证自己的实现是否正确
classy-js$ git clone https://github.com/sikeio/js-classy-spec.git verify # 把测试用的脚本下载到目录里
classy-js$ npm install mocha chai --save-dev # 安装 mocha 和 chai 到全局，这样可以在命令行使用
```

## 实现

### 定义 Class 函数

从题目分析，`Class` 函数应该返回一个类，也就是 JavaScript 中的函数：

```JavaScript
// index.js
function Class() {
  let myClass = function () { };

  return myClass;
}

module.exports = Class;
```

我们可以来跑一下测试：

```bash
classy-js$ mocha verify -R spec -g 'Implement Class Constructor'

  Implement Class Constructor
    ✓ should return a class constructor function
    1) should be able define a class
    ✓ should be able define a class without constructor


  2 passing (16ms)
  1 failing

  1) Implement Class Constructor should be able define a class:
     AssertionError: expected undefined to equal 1
      at Context.<anonymous> (verify/class_spec.js:20:12)
```

哈，没想到已经通过了两个测试了。没有通过的测试说的不是很清楚，那么接下来我们还要做什么呢？既然已经有测试了，不如去看一下测试的代码：

```bash
// /verify/class_spec.js
 describe("Implement Class Constructor", function() {
   const constructor = function(a, b) {
     this.a = a;
     this.b = b;
   };

   const Foo = Class({ initialize: constructor });

   it("should return a class constructor function", function() {
     assert.isFunction(Foo);
   });

   it("should be able define a class", function() {
     const obj = new Foo(1, 2);
     assert.equal(obj.constructor, Foo);
     assert.equal(obj.a, 1);
     assert.equal(obj.b, 2);

     const obj2 = new Foo(3, 4);
     assert.equal(obj2.a, 3);
     assert.equal(obj2.b, 4);
   });

   it("should be able define a class without constructor", function() {
     const klass = Class({});
     const obj = new klass()
     assert.equal(obj.constructor, klass);
   });
 });

```

结合题目里面给的例子，我们发现 `Class` 函数应该接受一个对象，而如果这个对象里面有一个叫 initialize 的函数，那么它应该在这个新创建的类里面充当构造函数的作用，也就是在每次创建新实例的时候，initialize 里面的代码会自动运行一次：

```JavaScript
...
function Class(data) {
  let myClass;

  if (data.initialize)
    myClass = data.initialize;
  else
    myClass = function() {};

  return myClass;
}
...

```
这时候再跑一次测试应该就可以通过了。你也可以再炫酷一点：

```JavaScript
...
function Class(data) {
  return data.initialize || function() {};
}
...
```

### 定义实例方法

从本课前面的示例代码来看，我们知道要使用原型来实现我们的实例方法。另外一个瞄到测试里面有一条 "should not define initialize as a method"。所以我们的代码如下：

```javascript
...
function Class(data) {
  const myClass = data.initialize || function() {};

  for (var fn in data) {
    if (fn !== "initialize")
      myClass.prototype[fn] = data[fn];
  }

  return myClass;
}
...
```

测试应该妥妥地通过。