# 01-块级作用域绑定知识点总结

#### 1.let const 声明不存在变量提升，存在临时死区,不可以重复声明

```js
    typeof(value) // "undefined"
    if(condition) {
        typeof(value2) // "undefined"
        typeof(value) // "Uncaught ReferenceError: Cannot access 'value' before initialization"
        let value = 'qiqi'
        let value = 'xixi' // Uncaught SyntaxError: Identifier 'value' has already been declared
    }
    
```

> 临时死区有别于没有变量提升，在let/const 定义并初始化变量值之前访问会直接抛出错误

#### 2.有别于var,在全局作用域（window)中创建一个新的变量时，var会将新的变量添加到window上

#### 3.let声明后可以不初始化，const必须进行初始化

```js
    let name; // 不会报错
    const age; // Uncaught SyntaxError: Missing initializer in const declaration
```

#### 4.const不允许修改绑定，但允许修改值

```js
    const name = 'ben'
    name ='alice' // VM227:1 Uncaught TypeError: Assignment to constant variable.
    const person = {
        name: 'ben'
    }
    person.name = 'chole' // 可以修改
```
