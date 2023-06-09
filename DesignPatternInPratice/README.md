# JavaScript设计模式实践
看到这篇文章《[前端渣渣唠嗑一下前端中的设计模式（真实场景例子）](https://juejin.cn/post/6844904138707337229)》想起了之前的一场面试，所以想正式重温学习。

## 文章参考：
* [JavaScript设计模式es6（23种）](https://juejin.cn/post/6844904032826294286)
* [掌握设计原则，你就是光！（这 25 个问题，你会几个？）](https://mp.weixin.qq.com/s/zlnv4r3neW1SSghznHOBig)（推荐）
* [前端渣渣唠嗑一下前端中的设计模式（真实场景例子）](https://juejin.cn/post/6844904138707337229)
* [重构 - 改善代码的各方面问题](https://juejin.cn/post/6844903597092651015)

## 一、设计模式简介
模式是一种可使用的解决方案，用于解决软件设计中遇到的常见问题。
### 1.设计模式作用
让代码或者项目具备：
- 可读性
- 可扩展性
- 复用性
- 可维护性

总结一句话就是：降低软件开发的复杂度，让迭代的难度保持在合理区间内。


### 2.设计模式原则
设计模式应该根据实际来设计项目，并非按部就班。

#### S – Single Responsibility Principle 单一职责原则
-   一个程序只做好一件事
-   如果功能过于复杂就拆分开，每个部分保持独立

**如何判断职责是否足够单一？**

这里有 5 个技巧：

1. 类中的私有方法过多
1. 比较难给类起一个合适的名字
1. 类中的代码行数、函数或者属性过多
1. 类中大量的方法都是集中操作类中的某几个属性
1. 类依赖的其他类过多，或者依赖类的其他类过多

**职责是否设计得越单一越好？**

单一职责原则通过避免设计大而全的类，避免将不相关的功能耦合在一起，来提高类的内聚性。类的职责单一，其依赖的和被依赖的其他类也会变少，从而实现代码的高内聚、松耦合。

**例子**

```
const userInfo = {
  userId: '',
  username: '',
  email: '',
  telephone: '',
  createTime: '',
  lastLoginTime: '',
  avatarUrl: '',
  provinceOfAddress: '',
  cityOfAddress: '',
  regionOfAddress: '',
  detailedAddress: ''
}
```

1. 从用户业务层面看，满足单一职责原则。
2. 从用户展示信息、地址信息、登录认证信息这些更细粒度的业务层面来看，就不满足单一职责原则。


#### O – OpenClosed Principle 开放/封闭原则
-   对扩展开放，对修改封闭
-   增加需求时，扩展新代码，而非修改已有代码

**修改代码就一定意味着违反开闭原则吗？**

不一定，这个我们要灵活看待：
- 第一：开闭原则并不是说完全杜绝修改，而是以最小修改代码的代价来完成新功能的开发
- 第二：同样的代码改动，在粗代码粒度下，可能被认定为修改，在细代码粒度下，可能又被认定为扩展
- 第三：尽量让最核心、最复杂的那部分逻辑代码满足开闭原则

**怎样的代码改动才被定义为扩展或者说是修改？**

通常情况下，只要它没有破坏原有代码的正常运行，**没有破坏原有的单元测试**，我们就可以认为它是符合开闭原则的。如果破坏了，那我们就可以认为它不符合开闭原则。

**如何做到对扩展开放、修改关闭？**

1. 保持函数、类和模块当前本身的状态，或是近似于他们一般情况下的状态（即不可修改性）
2. 使用组合的方式（避免使用继承方式）来扩展现有的类、函数或模块，以使它们可能以不同的名称来暴露新的特性或功能

**例子**

现实中的例子就是：想象下你的身上穿了一件T恤。天气入冬了，自然往身上添加衣物，而不是去改造你原有的那件T恤。

为什么不提倡去改动原有代码呢？很好理解，就是你并不清楚之前代码的耦合关系，牵一发而动全身。还是再提一句，以实际情况和粒度来衡量，并非改动就是不对。

1：中间件
Koa的洋葱模型采用了类似装饰器模式
```
app.use(A).use(B).use(C)
```

2：`function optional`
以下fn()函数内执行了f1(),f2(),f3()，假设产品说不需要f1()函数内的功能，我们就可以直接剔除f1()的执行。

而不是我们只在fn()内写上大量的业务代码（不符合单一职责），然后改动的时候不得不去再大量代码中修改（不符合开闭原则）。

```
fn(f1(),f2(),f3())
```

3：插件

```
Vue.use(PluginA)
Vue.use(PluginB)
```

4：装饰器
关于装饰器是什么：[Decorator 装饰器](https://juejin.cn/post/7072883925764276254)
```
@get('/hello')
async hello() {  // ...}
```

**总结**

1. 开闭原则是最重要的设计原则，很多设计模式都是以开闭原则为指导原则的
2. 它的核心是为了提高代码的扩展性

#### L – Liskov Substitution Principle 里氏替换原则
-   子类能覆盖父类
-   父类能出现的地方子类就能出现

**什么是里氏替换原则？**

子类在设计的时候，要遵守父类的行为约定（或者叫协议）。父类定义了函数的行为约定，子类可以改变函数的内部实现逻辑，但不能改变函数原有的行为约定。

这里的行为约定包括：函数声明要实现的功能、对输入、输出、异常的约定、甚至包括注释中所罗列的任何特殊说明。

里氏替换原则是针对继承而言的，如果继承是为了实现【代码重用】，也就是为了共享方法，那么共享的父类方法就应该保持不变，不能被子类重新定义。

> 简单来说就是：子类可以扩展父类的功能，但不能改变父类原有的功能

**如何判断是否满足里氏替换原则？**

拿父类的单元测试去验证子类的代码。如果某些单元测试运行失败，就有可能说明，子类的设计实现没有完全遵守父类的约定，子类有可能违背了里氏替换原则。


**例子：**

现实中里氏替换原则的例子：

1. 在Vue项目开发中，有多个页面使用一个组件。作为项目组成员你可以使用这个组件，但是不能够直接去改动这个组件原版，因为你不清楚所有使用这个组件的需求，而且还可能导致新的bug，致使多个页面使用了该组件的页面受到了影响。如果这个组件确实不满足新需求，可以复制一份来扩展。保证新组件可以**替换**原版组件，即能通过原版组件的单元测试。


代码中的例子：

1. 代码执行重复，不符合 `LSP` 原则

```
class People {  constructor(name, age) {    this.name = name    this.age = age  }  eat() {    // ...  }}class Student extends People {  constructor(name, age) {    super(name, age)  }  eat() {    // ...  }}const kuangXiangLu = new Student('KuangXiangLu', 10)kuangXiangLu.eat()
```

为什么不符合 `LSP` 呢？有以下两个原因：

第一：`Student` 类继承了 `People` 类，同时修改了 `People` 类的 `eat` 方法，这时就违背了 `LSP` 原则

第二：没有遵循父类的设计，修改了输出

#### I – Interface Segregation Principle 接口隔离原则
-   保持接口的单一独立
-   类似单一职责原则，这里更关注接口
#### D – Dependency Inversion Principle 依赖倒转原则
-   面向接口编程，依赖于抽象而不依赖于具体
-   使用方只关注接口而不关注具体类的实现

#### 题目一
题目来源：[重构 - 改善代码的各方面问题](https://juejin.cn/post/6844903597092651015)
```JavaScript
//checkType('165226226326','mobile')
//result：false
let checkType=function(str, type) {
    switch (type) {
        case 'email':
            return /^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$/.test(str)
        case 'mobile':
            return /^1[3|4|5|7|8][0-9]{9}$/.test(str);
        default:
            return true;
    }
}

```

这个函数有以下问题：

1.  如果想添加其他规则就得在函数里面增加 case 。这样违反了开闭原则吗？
1.  比如A页面需要添加一个金额的校验，B页面需要一个日期的校验，但是金额的校验只在A页面需要，日期的校验只在B页面需要。如果一直添加 case 。造成不必要的开销。

建议的方式是给这个 API 增加一个扩展的接口:
```JavaScript
let checkType=(function(){
    let rules={
        email(str){
            return /^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$/.test(str);
        },
        mobile(str){
            return /^1[3|4|5|7|8][0-9]{9}$/.test(str);
        }
    };
    //暴露接口
    return {
        //校验
        check(str, type){
            return rules[type]?rules[type](str):false;
        },
        //添加规则
        addRule(type,fn){
            rules[type]=fn;
        }
    }
})();

//调用方式
//使用mobile校验规则
console.log(checkType.check('188170239','mobile'));
//添加金额校验规则
checkType.addRule('money',function (str) {
    return /^[0-9]+(.[0-9]{2})?$/.test(str)
});
//使用金额校验规则
console.log(checkType.check('18.36','money'));

```

对于文章中以上的说法，我存在疑问。开闭原则也是需要看实际业务和细粒度，比如有大量地方用到了金额校验，新增一个 case 也是必要的。就算是现在只用到一次，以后可能其它同事要用呢？分散在各个页面中`checkType.addRule()`是不是难以维护？

所以我认为校验函数在不断增加的情况下，应该是给校验函数分类并拆分，这样也符合单一原则，可读性和维护性都得到了提高。

### 3.设计模式分类

-   创建型
    -   单例模式
    -   原型模式
    -   工厂模式
    -   抽象工厂模式
    -   建造者模式
-   结构型
    -   适配器模式
    -   装饰器模式
    -   代理模式
    -   外观模式
    -   桥接模式
    -   组合模式
    -   享元模式
-   行为型
    -   观察者模式
    -   迭代器模式
    -   策略模式
    -   模板方法模式
    -   职责链模式
    -   命令模式
    -   备忘录模式
    -   状态模式
    -   访问者模式
    -   中介者模式
    -   解释器模式