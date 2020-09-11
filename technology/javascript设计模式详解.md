> 在软件工程中，设计模式（design pattern）是对软件设计中普遍存在（反复出现）的各种问题，所提出的解决方案。 ——维基百科

最近拜读了淘系前端大神修言大佬的《JavaScript 设计模式核⼼原理与应⽤实践》，让我受益匪浅，一直沉浸在业务上重复造轮子的调包虾，每天感觉工作满满，但是未曾感觉自己在进步，此文参照该文章进行了二次消化并记录下来，帮助自己和有同样情况的同学不断加深对设计模式的理解，并运用到日常工作中

### 创建型 - 构造器模式

在`JavaScript`中，我们使用构造函数去初始化对象，就是应用了构造器模式。

```
// 假如你是Riot公司的一名程序员，领导让你使用js重新开发lol这款游戏

// 首先创建一名英雄到系统内
const Aixi = {
  name: '艾希',
  title: '寒冰射手',
  role: '射手'
}

// 创建第二名英雄
const Zhaoxin = {
  name: '赵信',
  title: '德邦总管',
  role: '战士'
};

// 想想还有100多名英雄没创建呢，要加班到深夜呀，于是写个构造函数
function Hero(name, title, role) {
  this.name = name;
  this.title = title;
  this.role = role;
};

// 创建英雄就new一下就搞定了
const Jiakesi = new Hero('贾克斯', '武器大师', '战士');
const Yidashi = new Hero('易大师', '无极剑圣', '刺客');
```

构造器将`name、title、role`赋值给对象的过程封装，确保了每个对象都具备这些属性，确保了共性的不变，同时将 `name、title、role`各自的取值操作开放，确保了个性的灵活

如果在使用构造器模式的时候，我们本质上是去抽象了每个对象实例的变与不变。那么使用工厂模式时，我们要做的就是去抽象不同构造函数（类）之间的变与不变

### 创建型 - 简单工厂模式

产品经理提到不同的英雄类型有不同的特性，比如射手的普攻距离是 `500` 码，战士是 `125` 码

```
// 为了解决上述问题，把Hero拆成两个类别Shorter和Longer
function Longer(name, title, role) {
  this.name = name;
  this.title = title;
  this.role = '射手';
  this.attackDistance = 500;
}

function Shorter(name, title, role) {
  this.name = name;
  this.title = title;
  this.role = '战士';
  this.attackDistance = 125;
}

// 通过一个函数判断英雄角色来使用不同构造器
function handleRole(name, title, role) {
  switch (role) {
    case: '射手':
      return new Longer(name, title, role);
      break;
    case: '战士':
      return new Shorter(name, title, role);
      break;
  }
}

// 我们可以这样创建了
const Jiakesi = new handleRole('贾克斯', '武器大师', '战士');
const Aixi = new handleRole('艾希', '寒冰射手', '射手');

// 为了将共性封装地更彻底，也为了将共性与个性分离地更彻底，我可以再把他们整合回Hero
function Hero(name, title, role, attackDistance) {
  this.name = name;
  this.title = title;
  this.role = role;
  this.attackDistance = attackDistance;
};
// 然后写一个生成英雄的函数，因为英雄们就像是从流水线上生产的产品，所以给这个函数取名为“英雄工厂”
function HeroFactory(name, title, role) {
  let attackDistance;
  switch (role) {
    case "射手":
      attackDistance = 500;
      break;
    case "刺客":
      attackDistance = 125;
      break;
  }
  return new Hero(name, title, role, speciality);
}

const Jiakesi = new HeroFactory('贾克斯', '武器大师', '战士');
const Aixi = new HeroFactory('艾希', '寒冰射手', '射手');
```

### 创建型 - 抽象工厂模式

抽象工厂模式的定义，是围绕一个超级工厂创建其他工厂，抽象工厂是佐证“开放封闭原则”的良好素材

```
// 接下来就是开一把游戏了，建立一种游戏模式需要游戏类型、地图、游戏机制
function Mode(type, map, mechanism) {
  this.type = type;
  this.map = map;
  this.mechanism = mechanism;
}
// 模仿英雄工厂创建有个游戏工厂
function GameFactory(type) {
  let map;
  let mechanism;
  switch (type) {
    case '普通匹配':
      map = '普通模式';
      mechanism = '普通机制';
      break;
    case '克隆模式':
      map = '克隆大作战';
      mechanism = '克隆机制';
      break;
    default:
      map = '';
      mechanism = '';
      brreak;
  }
  return new Mode(type, map, mechanism);
}

// 创建一个克隆模式
const cloneMode = GameFactory('克隆模式');

//  一段时间后，产品经理觉得克隆模式节奏太慢，决定在游戏机制中把技能冷却时间缩短40%；
// 这时你要去GameFactory函数内修改克隆模式里的mechanism逻辑；
// 没过多久，需要在克隆模式地图中加入一条哥斯拉增加游戏难度，这时还要修改对应的map逻辑;
// 几次改写导致GameFactory愈发混乱、臃肿，难以维护

// 这时候，可以把地图map和游戏机制mechanism抽象出来
function xiaguMap() {
  // 召唤师峡谷地图逻辑
}

function cloneMap() {
  // 克隆大作战地图逻辑
}

// 地图工厂
function MapFactory(type) {
  switch (type) {
    case '召唤师峡谷':
      return new xiaguMap();
    case '克隆大作战':
      return new cloneMap()
  }
}

function currentMechanism() {
  // 普通模式游戏机制逻辑
}

function cloneMechanism() {
  // 克隆游戏机制逻辑
}

// 游戏机制工厂
function MechanismFactory(type) {
  switch (type) {
    case '普通模式':
      return new currentMechanism();
    case '克隆机制':
      return new cloneMechanism()
  }
}

function GameFactory(type) {
  let map;
  let mechanism;
  switch (type) {
    case '普通匹配':
      map = MapFactory('召唤师峡谷');
      mechanism = MechanismFactory('普通模式');
      break;
    case '克隆模式':
      map = MapFactory('克隆大作战');
      mechanism = MechanismFactory('克隆机制');
      break;
    default:
      map = '';
      mechanism = '';
      brreak;
  }
  return new Mode(type, map, mechanism);
}

// 好景不长，产品经理觉得新年要新出一个临时模式，叫飞升模式，地图用新年雪人地图，游戏机制全改动;
// 这时我们不得不修改GameFactory中的逻辑，新增type属性，再新写一套逻辑进去，而且游新年结束后，还要改回去;

// 这种对“已建工厂”的修改违反了开放封闭原则（类、模块、函数可以扩展，但是不可修改的原则）;
// 如何避免直接修改工厂内部的逻辑呢？
// 郁闷的你中午吃完饭出去跟同事侃大山时从Java那哥们讲的的抽象类中获取了灵感，决定写“抽象工厂”和”抽象产品

class GameFactory {
  createMap() {
    throw new Error("抽象工厂方法不允许直接调用，你需要将我覆写！");
  }
  createMechanism() {
    throw new Error("抽象工厂方法不允许直接调用，你需要将我覆写！");
  }
}

class Map {
  init() {
    throw new Error("抽象工厂方法不允许直接调用，你需要将我覆写！");
  }
}

class Mechanism {
  init() {
    throw new Error("抽象工厂方法不允许直接调用，你需要将我覆写！");
  }
}

// 新建一个限定活动飞升模式
class FlyMap extends Map {
  init() {
    // 飞升模式逻辑
  }
}

// 飞升模式机制
class FlyMechansims extends Mechansims {
  init() {
    // 飞升模式机制
  }
}

// 飞升模式
class FlyModeFactory extends GameFactory {
  createMap() {
    return new FlyMap();
  }
  createMechansims() {
    return new FlyMechansims();
  }
}

// 新增一个飞升模式
const FlyMode = new FlyModeFactory();

// 总结:
// GameFactory，抽象工厂：声明最终目标产品的共性、规范
// FlyModeFactory，具体工厂：继承自抽象工厂，实现了抽象工厂里声明的那些方法，用于实例化最终目标产品
// Map，Mechanism，抽象产品：具体工厂里实现的方法，会依赖一些产品，抽象产品声明这些产品的共性、规范
// FlyMap，FlyMechansims，具体产品：见抽象产品
```

### 创建型 - 单例模式

单例模式想要做到的是，不管我们尝试去创建多少次，它都只给你返回第一次所创建的那唯一的一个实例。

```
// 游戏中玩家在看tab面板能看到每个人的出装，使用`es6 class`创建单例模式
class GameInfo {};
const player1 = new GameInfo();
const player2 = new GameInfo();

// 玩家之间不能共用信息
console.log(player1 === player2) // false

// 通过GameInfo.instance获取同一对象
class GameInfo {
  static getInstance() {
    if(!GameInfo.instance) {
      GameInfo.instance = new GameInfo();
    }
    return GameInfo.instance;
  }
};
const player3 = GameInfo.getInstance();
const player4 = GameInfo.getInstance();

// 可以共享信息
console.log(player3 === player4) // true
// vuex用到了单例模式

// 闭包实现
SingleDog.getInstance = (function() {
  // 定义自由变量instance，模拟私有变量
  let instance = null
  return function() {
    // 判断自由变量是否为null
    if(!instance) {
      // 如果为null则new出唯一实例
      instance = new SingleDog()
    }
    return instance
  }
})()

```

我的理解单例模式就是共享一块内存，new 一个实例不会再开辟新内存。

面试题：实现一个全局唯一弹窗

```
// 闭包
let Modal = (function() {
  let modal = null;
  return function() {
    if (!modal) {
      modal = document.createElement("div");
      modal.innerHTML = "this is modal!";
      modal.style.display = "none";
      modal.id = "modal";
      document.body.appendChild(modal);
    }
    return modal;
  };
})();

document.getElementById("open").addEventListener("click", () => {
  let modal = new Modal();
  modal.style.display = "block";
});
document.getElementById("close").addEventListener("click", () => {
  let modal = new Modal();
  modal.style.display = "none";
});
// class类
class Modal {
  constructor() {
    this.dom = null;
    if (!Modal.instance) {
      this.dom = document.createElement("div");
      this.dom.innerHTML = "this is modal";
      this.dom.id = "modal";
      this.dom.style.display = "none";
      this.dom.show = this.show.bind(this);
      this.dom.hide = this.hide.bind(this);
      document.body.appendChild(this.dom);
      Modal.instance = this.dom;
    }
    return Modal.instance;
  }
  show() {
    this.dom.style.display = "block";
  }
  hide() {
    this.dom.style.display = "none";
  }
}

const btnShow = document.getElementById("open");
const btnHide = document.getElementById("close");

btnShow.addEventListener("click", () => {
  let modal = new Modal();
  modal.show();
});
btnHide.addEventListener("click", () => {
  let modal = new Modal();
  modal.hide();
});
```

### 创建型 - 原型模式

原型模式不仅是一种设计模式，它还是一种编程范式（programming paradigm），是 JavaScript 面向对象系统实现的根基。

原型编程范式的核心思想就是利用实例来描述对象，用实例作为定义对象和继承的基础。在 JavaScript 中，原型编程范式的体现就是基于原型链的继承。这其中，对原型、原型链的理解是关键。

> ECMAScript 2015 中引入的 JavaScript 类实质上是 JavaScript 现有的基于原型的继承的语法糖。类语法不会为 JavaScript 引入新的面向对象的继承模型。 ——MDN

在 JavaScript 中，每个构造函数都拥有一个 prototype 属性，它指向构造函数的原型对象，这个原型对象中有一个 construtor 属性指回构造函数；每个实例都有一个**proto**属性，当我们使用构造函数去创建实例时，实例的**proto**属性就会指向构造函数的原型对象。

```
// 创建一个Dog构造函数
function Dog(name, age) {
  this.name = name
  this.age = age
}

Dog.prototype.eat = function() {
  console.log('肉骨头真好吃')
}

// 使用Dog构造函数创建dog实例
const dog = new Dog('旺财', 3)
```

实现一个深拷贝

```
// 方法一，缺点是没法拷贝函数和正则
function deepClone(obj) {
  let str = JSON.stringify(obj);
  return JSON.parse(str);
}

// 方法二，使用递归，原理层面实现深拷贝
function deepClone(obj) {
  // 如果是值类型或 null，则直接return
  if(typeof obj !== 'object' || obj === null) {
    return obj;
  }

  // 定义结果对象
  let copy = {};
  // 如果对象是数组，则定义结果数组
  if(obj.constructor === Array) {
    copy = [];
  }
  // 遍历对象的key
  for(let key in obj) {
    // 如果key是对象的自有属性
    if(obj.hasOwnProperty(key)) {
      // 递归调用深拷贝方法
      copy[key] = deepClone(obj[key]);
    }
  }
  return copy;
}
```

### 结构型 - 装饰器模式

装饰器模式，又名装饰者模式。它的定义是“在不改变原对象的基础上，通过对其进行包装拓展，使原有对象可以满足用户的更复杂需求”。

在 ES7 中，通过一个@语法糖轻松地给一个类装上装饰器

```
function classDecorator(target) {
    target.hasDecorator = true
  	return target
}

// 将装饰器“安装”到Button类上
@classDecorator
class Button {
    // Button类的相关逻辑
}
```

React 中的装饰器：HOC

> 高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件。

```
import React, { Component } from 'react'

const BorderHoc = WrappedComponent => class extends Component {
  render() {
    return <div style={{ border: 'solid 1px red' }}>
      <WrappedComponent />
    </div>
  }
}
export default borderHoc
```

装饰目标组件

```
import React, { Component } from 'react'
import BorderHoc from './BorderHoc'

// 用BorderHoc装饰目标组件
@BorderHoc
class TargetComponent extends React.Component {
  render() {
    // 目标组件具体的业务逻辑
  }
}

// export出去的其实是一个被包裹后的组件
export default TargetComponent
```

### 结构型 - 适配器模式

> 适配器模式通过把一个类的接口变换成客户端所期待的另一种接口，可以帮我们解决不兼容的问题。不影响现有实现方式，兼容调用旧接口的代码

```
// 你写了个新的攻击特效
function newSpecial(params) {
  // 业务逻辑
}

// 旧的攻击特效
function oldSpecial(params) {
  // 业务逻辑
}

// 产品经理让你升级一下，替换掉旧的攻击特效，于是写了个适配器
function Apatar(params) {
  newSpecial(params);
}

function oldSpecial(params) {
  Apatar(params);
}

// 适配器类似于转换器, 类似一种可以给安卓和苹果系统充电的手机转换接头。

```

### 结构型 - 代理模式

代理模式，式如其名——在某些情况下，出于种种考虑/限制，一个对象不能直接访问另一个对象，需要一个第三者（代理）牵线搭桥从而间接达到访问目的，这样的模式就是代理模式。

> 最常见的四种代理类型：事件代理、虚拟代理、缓存代理和保护代理

#### 事件代理

其他三种模式参照修言大佬的掘金小册中有详细介绍。时间代理可能是代理模式最常见的一种应用方式，它的场景是一个父元素下有多个子元素，像这样：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>事件代理</title>
</head>
<body>
  <div id="father">
    <a href="#">链接1号</a>
    <a href="#">链接2号</a>
    <a href="#">链接3号</a>
    <a href="#">链接4号</a>
    <a href="#">链接5号</a>
    <a href="#">链接6号</a>
  </div>
</body>
</html>
```

我们现在的需求是，希望鼠标点击每个 a 标签，都可以弹出“我是 xxx”这样的提示。比如点击第一个 a 标签，弹出“我是链接 1 号”这样的提示。这意味着我们至少要安装 6 个监听函数给 6 个不同的的元素(一般我们会用循环，代码如下所示），如果我们的 a 标签进一步增多，那么性能的开销会更大。

```
// 假如不用代理模式，我们将循环安装监听函数
const aNodes = document.getElementById('father').getElementsByTagName('a')

const aLength = aNodes.length

for(let i=0;i<aLength;i++) {
    aNodes[i].addEventListener('click', function(e) {
        e.preventDefault()
        alert(`我是${aNodes[i].innerText}`)
    })
}

// 考虑到事件本身具有“冒泡”的特性，当我们点击 a 元素时，点击事件会“冒泡”到父元素 div 上，从而被监听到。它可以很大程度上提高我们代码的性能。
// 获取父元素
const father = document.getElementById('father')

// 给父元素安装一次监听函数
father.addEventListener('click', function(e) {
    // 识别是否是目标子元素
    if(e.target.tagName === 'A') {
        // 以下是监听函数的函数体
        e.preventDefault()
        alert(`我是${e.target.innerText}`)
    }
} )
```

### 行为型 - 策略模式

> 定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。

终于到了我最喜欢的模式了，这个模式非常实用，在写业务逻辑中经常遇到。<strong>同时坚持开放封闭改造，对扩展开放，对修改封闭</strong>

修言大神给出的经典案例《询价策略》
需求描述:
当价格类型为“预售价”时，满 100 - 20，不满 100 打 9 折

当价格类型为“大促价”时，满 100 - 30，不满 100 打 8 折

当价格类型为“返场价”时，满 200 - 50，不叠加

当价格类型为“尝鲜价”时，直接打 5 折

他首先将四种价格做了标签化：

```
预售价 - pre
大促价 - onSale
返场价 - back
尝鲜价 - fresh
```

使用 if-else ，轻松搞定

```
// 询价方法，接受价格标签和原价为入参
function askPrice(tag, originPrice) {

  // 处理预热价
  if(tag === 'pre') {
    if(originPrice >= 100) {
      return originPrice - 20
    }
    return originPrice * 0.9
  }

  // 处理大促价
  if(tag === 'onSale') {
    if(originPrice >= 100) {
      return originPrice - 30
    }
    return originPrice * 0.8
  }

  // 处理返场价
  if(tag === 'back') {
    if(originPrice >= 200) {
      return originPrice - 50
    }
    return originPrice
  }

  // 处理尝鲜价
  if(tag === 'fresh') {
     return originPrice * 0.5
  }
}
```

缺点： 1.违背了“单一功能”原则。一个 function 里面，它竟然处理了四坨逻辑。 2.违背了“开放封闭”原则。假如增加一个满 100 - 50 的“新人价”怎么办？他只能继续 if-else,由于改动了询价函数，需测试重新回归测试。

解决办法：使用对象映射，同时遵循单一功能和开放封闭原则

```
// 定义一个询价处理器对象
const priceProcessor = {
  pre(originPrice) {
    if (originPrice >= 100) {
      return originPrice - 20;
    }
    return originPrice * 0.9;
  },
  onSale(originPrice) {
    if (originPrice >= 100) {
      return originPrice - 30;
    }
    return originPrice * 0.8;
  },
  back(originPrice) {
    if (originPrice >= 200) {
      return originPrice - 50;
    }
    return originPrice;
  },
  fresh(originPrice) {
    return originPrice * 0.5;
  },
};

// 当我们想使用其中某个询价算法的时候：通过标签名去定位就好了

// 询价函数
function askPrice(tag, originPrice) {
  return priceProcessor[tag](originPrice)
}
```

假如新增一个条件 新人满 100 返 50

```
priceProcessor.newUser = function (originPrice) {
  if (originPrice >= 100) {
    return originPrice - 50;
  }
  return originPrice;
}
```

### 行为型 - 状态模式

状态模式和策略模式相似，但状态模式强调了类的概念

```
// 即把处理过程放在处理对象本身中
class CoffeeMaker {
  constructor() {
    this.state = "init";
    this.stock = {
      coffee: 500,
      milk: 200,
    };
  }

  changeState(state) {
    this.state = state;
    this.changeStateProcessor[state]();
  }

  changeStateProcessor = {
    that: this,
    coffee() {
      console.log("咖啡机现在的咖啡存储量是:", this.that.stock.coffee);
      console.log("我只吐黑咖啡");
    },
    latte() {
      this.coffee();
      console.log("咖啡机现在的咖啡存储量是:", this.that.stock.milk);
      console.log("加点奶");
    },
    vanillaLatte() {
      this.latte();
      console.log("再加香草糖浆");
    },
    mocha() {
      this.latte();
      console.log("再加巧克力");
    },
  };
}

// 测试一下
const coffeeMaker = new CoffeeMaker();
coffeeMaker.changeState("latte");
```

### 行为型 - 观察者模式

> 观察者模式定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个目标对象，当这个目标对象的状态发生变化时，会通知所有观察者对象，使它们能够自动更新。

```
// 观察者订阅发布者，发布者能群通知观察者们
class Publisher {
  constructor() {
    this.observers = [];
  }
  addObserver(observer) {
    this.observers.push(observer);
  }
  removeObserver(observer) {
    this.observers.forEach((item, index) => {
      if (item === observer) this.observers.splice(index, 1);
    });
  }
  notifyObersers() {
    this.observers.forEach((item) => {
      item.update(this);
    });
  }
}
class Observer {
  update(publisher) {
    // 更新操作
  }
}
// 在上面两个类中扩展出具体的类, 游戏中所有玩家设有提醒机制，如大龙死亡通知所有玩家
class DragonPublisher extends Publisher {
  constructor() {
    super();
    this.state = null;
    this.observers = [];
  }
  getState() {
    return this.state;
  }
  setState(state) {
    this.state = state;
    this.notifyObersers();
  }
}
class PlayerObserver extends Observer {
  constructor(id) {
    super();
    this.id = id;
    this.state = null;
  }
  update(publisher) {
    this.state = publisher.getState();
    this.remind();
  }
  remind() {
    console.log(`接收信息 ${this.state}，巴拉巴拉`);
  }
}
// 测试一下
const Dragon = new DragonPublisher();
const player1 = new PlayerObserver("#01");
const player2 = new PlayerObserver("#02");

Dragon.addObserver(player1);
Dragon.addObserver(player2);
Dragon.setState("大龙已被击杀");

```

### 行为型 - 发布——订阅模式

观察者模式：发布者直接接触订阅者；发布——订阅模式：发布者和订阅者通过中间平台间接接触, 发布者无法感知订阅者

```
class EventEmitter {
  constructor() {
    this.handlers = {};
  }

  // 注册事件监听器
  on(event, callback) {
    if (!this.handlers[event]) this.handlers[event] = [];
    this.handlers[event].push(callback);
  }

  // 移除某个事件回调队列里的指定回调函数
  off(event, callback) {
    const callbacks = this.handlers[event],
      index = callbacks.indexOf(callback);
    if (index !== -1) {
      callbacks.splice(index, 1);
    }
  }

  // 触发目标事件
  emit(event, ...params) {
    if (this.handlers[event]) {
      this.handlers[event].forEach((callback) => {
        callback(...params);
      });
    }
  }

  // 为事件注册单次监听器
  once(event, callback) {
    // 对回调函数进行包装，使其执行完毕自动被移除
    const wrapper = (...params) => {
      callback(...params);
      this.off(event, wrapper);
    };
    this.on(event, wrapper);
  }
}
```

参考文档：掘金小册-[《JavaScript 设计模式核⼼原理与应⽤实践》-修言](https://juejin.im/book/5c70fc83518825428d7f9dfb/section/5c70fc845188256282697b96)
