# 02-函数知识点总结

#### 1.es6之后函数参数的改变不会导致arguments改变

```js
    function mixArgs(first, second = b) {
        console.log(arguments.length); // 1 ['a']
        console.log(first === arguments[0]); // true
        console.log(second === arguments[1]); // false
        first = 'c';
        second = 'd';
        console.log(first === arguments[0]); // false
        console.log(second === arguments[1]); // false
    }
    mixArgs('a')
```

#### 2.函数的参数也存在临时死区

```js
    function add(first = second, second) {
        return first + second
    }
    console.log(add(1, 1)) // 2
    // JS 内部执行的代码
    let first = 1;
    let second = 1

    console.log(add(undefined, 1)) //  抛出错误
    // JS 内部执行的代码
    let first = second 
    let second = 1
```

#### 3. 函数name 属性

```js
    const doSomething =  function doSomethingElse(){}

    var person = {
        get firstName(){
            return 'alice'
        },
        sayName: function () {

        }
    }
    console.log(doSomething.name); // doSomethingElse
    console.log(person.firstName.name); // get firstName
    console.log(person.sayName.name); // sayName
```

#### 4.函数多重用途，普通函数与构造函数

```js
    function Person(name) {
        this.name = name;
    }

    var person = new Person('ben');
    var notAPerson = Person('ben')

    console.log(person); // Person {name:'ben'}
    console.log(notAPerson); // undefined
```

     函数有两个不同的内部方法：[[Call]]与[[Construct]]。
     通过new关键词调用函数时，执行的是[[Construct]]函数，创建一个对象作为实例，然后执行函数体，将this绑定到这个实例上，如果函数有非基础类型的返回则，则返回这个值，反之则返回这个实例。
     函数不通过new调用则执行[[call]]函数，直接代码中的函数体。
     具有[[Construct]]的函数统称为构造函数。
     箭头函数没有[[Construct]]。

#### 5.Es5判断函数被调用的方法

```js
function Person (name ){
    if(this instanceof Person){
        this.name = name;
    } else {
        throw new Error('必须通过new 关键字来调用Person')
    } 
}

 var person = new Person('ben'); 
 var notAPerson = Person('ben');  // 抛出错误
 var notAPerson2 = Person.call(person,'june') //  不会报错，person 被更改为{name:'june'}
 //Person的this改为person,person 由Person 构造，if检测通过
```

#### 6.Es6判断函数被调用的方法 new.target(元属性)

> 元属性指非对象的属性，其可以提供非对象目标的补充信息（例如new);

    调用函数[[Construct]]方法时，new.target被赋值为new操作符的目标，通常为构造函数。如果用[[Call]]方法，则new.target的值为undefined.

```js
    function Person(name){
        console.log(name,new.target);
        if(new.target === Person){
            this.name = name
        } else {
           throw new Error('必须通过new 关键字来调用Person')
        }
    }

    function AnotherPerson(name) {
        Person.call(this,name)
    }
    var person = new Person('ben')
    var anotherPerson = new AnotherPerson('june') // 抛出错误
```

#### 7.箭头函数

*  没有this、super、arguments和new.target 这些值由最近一层非箭头函数决定。
* 不能通过new关键字调用 因为没有\[[Construct]]
* 没有原型 prototype
* 不可以改变this的绑定
* 不支持arguments
* 不支持重复的命名阐述

#### 8.尾调优化

> 指函数作为另一个函数的最后一条语句被调用

 es6在严格模式下限制了尾调用栈的大小。满足以下条件，尾调不再创建新的栈帧，而是清除并重用当前帧。

* 尾调用不访问当前栈帧的变量（即不是闭包）
* 在函数内部，尾调用是最后一条语句
* 尾调用的结果作为函数值返回

> 有问题的代码，递归爆栈（栈溢出——写入的内容大小超出栈的大小）
```js
    function factorial1 (n) {
        if(n > 1) {
            return n * factorial1(n - 1) 
        }else {
            return 1
        }
    } 
    // 递归方法factorial(阶乘)调用前执行了 *n 的操作，阶乘函数就无法被引擎优化，该栈帧内随着 n 递减 factorial(n) * factorial(n-1) * ··· 一直在增加，如果n很大，会爆帧。
```

> 优化后的代码

```js
    function factorial2 (n, p = 1) {
        if(n > 1){
            const result = n * p
            return factorial2(n - 1, result)
        }else {
            return p * 1
        }
    }

// 递归factorial2调用前已经计算出结果，将n-1和结果传递给下次调用，当前调用会就可以被引擎删除。
