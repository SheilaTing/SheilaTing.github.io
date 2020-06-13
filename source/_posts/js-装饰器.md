---
title: js 装饰器
date: 2020-06-13 13:18:15
tags: js

---

**`Decorator` 是 ES7 的一个新语法正如其“装饰器”的叫法所表达的，他通过添加`@方法名`可以对一些对象进行装饰包装然后返回一个被包装过的对象，可以装饰的对象包括：类，属性，方法等。** 在使用它之前需要引入babel模块 `transform-decorators-legacy` 编译成 ES5 或 ES6

### 1. 类的装饰

当装饰的对象是类时，我们操作的就是这个类本身，即装饰器函数的第一个参数，就是所要装饰的目标类。

```
@decorator
class A {}

// 等同于
class A {}
A = decorator(A) || A;
```







### 2. 属性或方法的装饰

**对于类属性或方法的装饰本质是操作其描述符，可以把此时的装饰器理解成是 `Object.defineProperty(obj, prop, descriptor)`的语法糖。**

```javascript
class C {
  @readonly(false)
  method() { console.log('cat') }
}

function readonly(value) {
  return function (target, key, descriptor) { 
	/**
	* 此处 target 为 C.prototype; 
	* key 为 method;
    * 原 descriptor 为：{ value: f, enumarable: false, writable: true, configurable: true }
	*/
    descriptor.writable = value
    return descriptor
  }
}

const c = new C()
c.method = () => console.log('dog')

c.method() // cat
```





```javascript
function observable(target, name, descriptor) {
  let oldValue = descriptor.initializer && descriptor.initializer.call(this);
  console.log("-----" + oldValue); 
  return {
    enumerable: true,
    configurable: true,
    get: function() {
      console.log("读取值...");
      return oldValue + "!";
    },
    set: function(newValue) {
      console.log("现在在设置值...");
      oldValue = newValue;
    }
  };
}
class App {
  @observable
  title = "饥⼈⾕";
}
let app = new App();
console.log(app.title);
app.title = "jirengu.com";
```

