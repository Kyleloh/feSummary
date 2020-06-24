# 设计模式

### 单例模式singleton
保证一个类仅有一个实例，并提供一个访问它的全局访问点。
#### 核心
确保只有一个实例，并提供全局访问

```js
function getSingleton(fn) {
    var instance = null;

    return function() {
        if (!instance) {
            instance = fn.apply(this, arguments);
        }

        return instance;
    }
}
```

---

### 策略模式
定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

#### 核心
- 将算法的使用和算法的实现分离开来。
- 一个基于策略模式的程序至少由两部分组成：
    - 第一个部分是一组策略类，策略类封装了具体的算法，并负责具体的计算过程。
    - 第二个部分是环境类Context，Context接受客户的请求，随后把请求委托给某一个策略类。要做到这点，说明Context 中要维持对某个策略对象的引用

```js
// 加权映射关系
var levelMap = {
    S: 10,
    A: 8,
    B: 6,
    C: 4
};

// 组策略
var scoreLevel = {
    basicScore: 80,

    S: function() {
        return this.basicScore + levelMap['S']; 
    },

    A: function() {
        return this.basicScore + levelMap['A']; 
    },

    B: function() {
        return this.basicScore + levelMap['B']; 
    },

    C: function() {
        return this.basicScore + levelMap['C']; 
    }
}

// 调用
function getScore(level) {
    return scoreLevel[level] ? scoreLevel[level]() : 0;
}
```

---

### 代理模式
为一个对象提供一个代用品或占位符，以便控制对它的访问

#### 核心
当客户不方便直接访问一个 对象或者不满足需要的时候，提供一个替身对象 来控制对这个对象的访问，客户实际上访问的是 替身对象。

替身对象对请求做出一些处理之后， 再把请求转交给本体对象

代理和本体的接口具有一致性，本体定义了关键功能，而代理是提供或拒绝对它的访问，或者在访问本体之前做一 些额外的事情

#### 实现
代理模式主要有三种：保护代理、虚拟代理、缓存代理

##### 保护代理
主要实现了访问主体的限制行为，例如对入参进行限制，不满足条件的不让访问。

##### 虚拟代理
加入额外操作。例如节流防抖。

##### 缓存
缓存计算结果。

---

### 迭代器模式
迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示

#### 核心
在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素

```js
function each(obj, cb) {
    var value;

    if (Array.isArray(obj)) {
        for (var i = 0; i < obj.length; ++i) {
            value = cb.call(obj[i], i, obj[i]);

            if (value === false) {
                break;
            }
        }
    } else {
        for (var i in obj) {
            value = cb.call(obj[i], i, obj[i]);

            if (value === false) {
                break;
            }
        }
    }
}
```

---

### 发布-订阅模式
也称作观察者模式，定义了对象间的一种一对多的依赖关系，当一个对象的状态发 生改变时，所有依赖于它的对象都将得到通知

#### 核心
取代对象之间硬编码的通知机制，一个对象不用再显式地调用另外一个对象的某个接口。

与传统的发布-订阅模式实现方式（将订阅者自身当成引用传入发布者）不同，在JS中通常使用注册回调函数的形式来订阅

```js
// 订阅
document.body.addEventListener('click', function() {
    console.log('click1');
}, false);

document.body.addEventListener('click', function() {
    console.log('click2');
}, false);

// 发布
document.body.click(); // click1  click2
```

---

### 中介者模式
如：事件委托

---

### 装饰者模式
以动态地给某个对象添加一些额外的职责，而不会影响从这个类中派生的其他对象。
是一种“即用即付”的方式，能够在不改变对 象自身的基础上，在程序运行期间给对象动态地 添加职责

---

### 适配器
是解决两个软件实体间的接口不兼容的问题，对不兼容的部分进行适配

---

