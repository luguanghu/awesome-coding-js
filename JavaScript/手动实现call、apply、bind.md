## 模拟实现call

- 1.判断当前`this`是否为函数，防止` Function.prototype.myCall()` 直接调用
- 2.`context` 为可选参数，如果不传的话默认上下文为 `window`
- 3.为`context` 创建一个 `Symbol`（保证不会重名）属性，将当前函数赋值给这个属性
- 4.处理参数，传入第一个参数后的其余参数
- 4.调用函数后即删除该`Symbol`属性

```js
    Function.prototype.myCall = function (context) {
      if (typeof this !== 'function') {
        return undefined; // 用于防止 Function.prototype.myCall() 直接调用
      }
      context = context || window;
      const fn = Symbol();
      context[fn] = this;
      const args = [...arguments].slice(1);
      const result = context[fn](...args);
      delete context[fn];
      return result;
    }
```

## 模拟实现apply

`apply`实现类似`call`，参数为数组

```js
    Function.prototype.myApply = function (context) {
      if (typeof this !== 'function') {
        return undefined; // 用于防止 Function.prototype.myCall() 直接调用
      }
      context = context || window;
      const fn = Symbol();
      context[fn] = this;
      let result;
      if (arguments[1] instanceof Array) {
        result = context[fn](...arguments[1]);
      } else {
        result = context[fn]();
      }
      delete context[fn];
      return result;
    }
```

## 模拟实现bind


- 1.处理参数，返回一个闭包
- 2.判断是否为构造函数调用，如果是则使用`new`调用当前函数
- 3.如果不是，使用`apply`，将`context`和处理好的参数传入

```js
    Function.prototype.myBind = function (context) {
      if (typeof this !== 'function') {
        throw new TypeError('Error')
      }
      const _this = this
      const args = [...arguments].slice(1)
      return function F() {
        // 判断是否用于构造函数
        if (this instanceof F) {
          return new _this(...args, ...arguments)
        }
        return _this.apply(context, args.concat(...arguments))
      }
    }
```