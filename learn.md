## factory

> 用于构建对象的函数

```js
function createUser(role) {
  function User(options) {
    this.name = options.name;
    this.viewPages = options.viewPages;
  }
  switch (role) {
    case 'superAdmin':
      return new User({name: 'superAdmin', viewPages: ['1', '2', '3', '4']});
      break;
    case 'admin':
      return new User({name: 'admin', viewPages: ['1', '3']});
      break;
    default:
      throw new Error('参数错误!');
  }
}

createUser('admin');
```

优化如下

```js
function createUser(role) {
  return new this[role]();
}
createUser.prototype.superAdmin = function () {
  this.name = 'superAdmin';
  this.viewPages = ['1', '2', '3', '4'];
};
createUser.prototype.admin = function () {
  this.name = 'admin';
  this.viewPages = ['1', '3'];
};
new createUser('admin');
```

适用场景

- 对象的构建较为复杂
- 依靠不同环境创建具体实例
- 处理大量具有相同类型的小对象

## 单例模式

保证一个特定类最多只有一个实例 当第二次使用同一个类创建新对象时 应得到和第一次创建对象完全相同

> 实现思路 将已生成的对象通过闭包维护 下次再生成新对象时 就直接返回老对象

```js
let SingleUser1 = (function(){
    let instance = null
    return functoin User () {
        if (instance) {
            return instance
        }
        return instance = this
    }
})()
```

将构造函数 User 和判断实例是否存在的逻辑解耦

```js
let SingleUser2 = (function () {
  let instance = null;
  function User(name) {
    this.name = name;
  }
  return function (name) {
    if (instance) {
      return instance;
    }
    return (instance = new User(name));
  };
})();
```

进一步封装

```js
let singleton = function (fn) {
  let instance = null;
  return function (args) {
    if (instance) {
      return instance;
    }
    return (instance = new fn(args));
  };
};
```

凡是使用唯一对象的场景, 都适用于单例模式

```js
// 如登录框
const createMask = singleton(function () {
  const mask = document.createElement('div');
  mask.style.background = 'red';
  mask.style.width = '100%';
  mask.style.position = 'fixed';
  document.body.appendChild(mask);
  return mask;
});
```

## 适配器模式

适配器模式将一个类的接口(方法或属性)转化成用户希望的另外一个接口(方法或属性), 适配器模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作

```js
class Power {
    constructor(){
        this.serveVoltage = 110
        this.serveShape = 'triangle'
    }
}
class Adaptor {
    constructor(){
        this.consumeVoltage = 110
        this.consumeShape = 'triangle'
    }
    usePower(power) {
        if (!power) {
            throw new Error('请输入电源')
        }
        if (power.serveVoltage != this.consumeVoltage || power.serveShape !== this.consumeShape) {
            throw new Error('电源规格不对')
        }
        this.serveVoltage = 220
        this.serveShape = 'double'
    }
}

class User {
    constructor () {
        this.consumeVoltage = 220
        this.consumeShape = 'double'
    }
    usePoser(power) {
        if (!power) {
            throw new Error ('请插入电源')
        }
        if(power.serveVoltage !== this.consumeVoltage || power.serveShape !== this.         consumeShape){
            throw new Error('电源规格不对')
        }
    console.log('电源已接入');
        }
    }
}
let power = new Power()
let user = new User()
let adaptor = new Adaptor()
adaptor.usePower(power)
user.userPower(adaptor)
```
