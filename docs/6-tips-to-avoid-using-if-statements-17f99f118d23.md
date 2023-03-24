# 避免使用 IF 语句的 6 个技巧

> 原文：<https://levelup.gitconnected.com/6-tips-to-avoid-using-if-statements-17f99f118d23>

## 这些优化技巧将防止我们在 JavaScript 中过多使用 IF 语句

![](img/f5d16e58ee196acc306bc2c77d2d4c69.png)

在最近重构我的代码时，我注意到早期的代码使用了太多的 if 语句，达到了我从未见过的程度。这就是为什么我认为分享这些简单的技巧很重要，它们可以帮助我们避免使用太多的 if 语句。

接下来，我们将介绍 6 种使用 if 的方法。这并不是抵制使用 if 妄想症，而是换一种方式思考我们的编码思路。

## 1.三元条件运算符

**例 1:**

用 IF 编码:

```
function customerValidation(customer) {
  if (!customer.email) {
    return error('email is require')
  } else if (!customer.login) {
    return error('login is required')
  } else if (!customer.name) {
    return error('name is required')
  } else {
    return customer
  }
}
```

重构代码:

```
const customerValidation = customer =>
  !customer.email   ? error('email is required')
  : !customer.login ? error('login is required')
  : !customer.name  ? error('name is required')
                    : customer
```

**例二:**

用 IF 编码:

```
function getEventTarget(evt) {
    if (!evt) {
        evt = window.event;
    }
    if (!evt) {
        return;
    }
    const target;
    if (evt.target) {
        target = evt.target;
    } else {
        target = evt.srcElement;
    }
    return target;
}
```

重构代码:

```
function getEventTarget(evt) {
  evt = evt || window.event;
  return evt && (evt.target || evt.srcElement);
}
```

## 2.短路逻辑运算符

**例一:**

用 IF 编码:

```
const isOnline = true;
const makeReservation= ()=>{};
const user = {
    name:'Damian',
    age:32,
    dni:33295000
};

if (isOnline){
    makeReservation(user);
} 
```

重构代码:

```
const isOnline = true;
const makeReservation= ()=>{};
const user = {
    name:'Damian',
    age:32,
    dni:33295000
};

isOnline&&makeReservation(user);
```

**例 2:**

用 IF 编码:

```
const active = true;
const loan = {
    uuid:123456,
    ammount:10,
    requestedBy:'rick'
};

const sendMoney = ()=>{};

if (active&&loan){
    sendMoney();
}
```

重构代码:

```
const active = true;
const loan = {
    uuid:123456,
    ammount:10,
    requestedBy:'rick'
};

const sendMoney = ()=>{};

active && loan && sendMoney();
```

## 3.职能委托

**例 1:**

用 IF 编码:

```
function itemDropped(item, location) {
    if (!item) {
        return false;
    } else if (outOfBounds(location) {
        var error = outOfBounds;
        server.notify(item, error);
        items.resetAll();
        return false;
    } else {
        animateCanvas();
        server.notify(item, location);
        return true;
    }
}
```

重构代码:

```
function itemDropped(item, location) {
    const dropOut = function() {
        server.notify(item, outOfBounds);
        items.resetAll();
        return false;
    }

    const dropIn = function() {
        server.notify(item, location);
        animateCanvas();
        return true;
    }

    return !!item && (outOfBounds(location) ? dropOut() : dropIn());
}
```

## 4.无分支策略

**例 1:**

带开关的代码:

```
switch(breed){
    case 'border':
      return 'Border Collies are good boys and girls.';
      break;  
    case 'pitbull':
      return 'Pit Bulls are good boys and girls.';
      break;  
    case 'german':
      return 'German Shepherds are good boys and girls.';
      break;
    default:
      return 'Im default'
}
```

重构代码:

```
const dogSwitch = (breed) =>({
  "border": "Border Collies are good boys and girls.",
  "pitbull": "Pit Bulls are good boys and girls.",
  "german": "German Shepherds are good boys and girls.",  
})[breed]||'Im the default';

dogSwitch("border xxx")
```

## 5.作为数据的函数

我们知道在 JS 中函数是第一类，所以使用它我们可以把代码分割成一个函数对象。

用 IF 编码:

```
const calc = {
    run: function(op, n1, n2) {
        const result;
        if (op == "add") {
            result = n1 + n2;
        } else if (op == "sub" ) {
            result = n1 - n2;
        } else if (op == "mult" ) {
            result = n1 * n2;
        } else if (op == "div" ) {
            result = n1 / n2;
        }
        return result;
    }
}

calc.run("sub", 5, 3); //2
```

重构代码:

```
const calc = {
    add : function(a,b) {
        return a + b;
    },
    sub : function(a,b) {
        return a - b;
    },
    mult : function(a,b) {
        return a * b;
    },
    div : function(a,b) {
        return a / b;
    },
    run: function(fn, a, b) {
        return fn && fn(a,b);
    }
}

calc.run(calc.mult, 7, 4); //28
```

## 6.多态性

多态是一个对象拥有多种形式的能力。OOP 中多态性最常见的用途是使用父类引用来引用子类对象。

用 IF 编码:

```
const bob = {
  name:'Bob',
  salary:1000,
  job_type:'DEVELOPER'
};

const mary = {
  name:'Mary',
  salary:1000,
  job_type:'QA'
};

const calc = (person) =>{

    if (people.job_type==='DEVELOPER')
        return person.salary+9000*0.10;

    if (people.job_type==='QA')
        return person.salary+1000*0.60;
}

console.log('Salary',calc(bob));
console.log('Salary',calc(mary));
```

重构代码:

```
 const qaSalary  = (base) => base+9000*0.10;
const devSalary = (base) => base+1000*0.60;

//Add function to the object.
const bob = {
  name:'Bob',
  salary:1000,
  job_type:'DEVELOPER',
  calc: devSalary
};

const mary = {
  name:'Mary',
  salary:1000,
  job_type:'QA',
  calc: qaSalary
};

console.log('Salary',bob.calc(bob.salary));
console.log('Salary',mary.calc(mary.salary));
```