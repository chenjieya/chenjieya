# ES6
    [toc] 
## 一.使用let声明变量

1. var声明变量的问题：
    1. 全局变量挂载到全局对象：全局对象成员污染问题
    2. 允许重复的变量声明：导致数据被覆盖
    3. 变量提升：怪异的数据访问、闭包问题

2. let声明的变量：
    - let声明的变量不会挂在到全局对象（window）；
        ```js
        let a = 2;
        console.log(window.a);  // undefined
        console.log(a);  // 2
        // 说明window对象身上没有a这个属性
        ```
    - let声明的变量不允许在同一个作用域内重复声明；
        ```js
        let cj = 'chenjie';
        function cj() {
            console.log(cj);
        }
        cj();  
        // 都是在全局作用域内，是不可以出现重复的变量名的
        // Identifier 'cj' has already been declared(报错)
        ```
    - 使用let不会有变量提升，因此，不能在定义let变量之前使用它;
        ```js
        console.log(cj);
        let cj = 'chenjie';
        //Cannot access 'cj' before initialization
        ```
        [扩展]：其实在底层实现上，let声明的变量是可以提升的，但是提升后会同时将变量放到‘暂时性死区’，如果访问的变量位于‘暂时性死区’，则会报错：“Cannot access 'a' before initialization”。当代码运行到该变量的声明语句的时候，会将变量从‘暂时性死区’中移除。
        ```js
        console.log(cj);
        let cj = 'chenjie';
        //1.let cj 提升到上面，同时将他放到暂时性死区中。
        //2.然后如果先log的a，此时的a还在暂时性死区中，则报错
        //3.如果先执行的let声明，则将他从死区中移除
        ```
    - ES6不仅引入了let关键字用于解决声明变量的问题，还引入了块级作用域的概念。
    **块级作用域**：代码执行是遇到花括号，会创建一个块级作用域，花括号结束，销毁块级作用域。
        ```js
        if(Math.random()<0.5){
            let cj = 'chenjie';
            console.log(cj);
        }else{
            console.log(cj);
        }
        console.log(cj);
        //因为let在块级作用中，无论进不进入到if里面，后两个log指定会报错的。
        ```
    - 在循环中，用let声明的循环变量，会特殊处理，每次进入到循环体，都会开启一个新的作用域，并且将循环变量绑定到当前的作用域。（每次循环使用的都是一个新的循变量）
        ```html
        <div class="btn"></div>
        ```
        ```js
        var div = document.getElementsByClassName('btn')[0];
        for(let i = 0; i < 10; i++){
            var btn = document.createElement('button');
            btn.innerHTML = '按钮' + i;
            btn.onclick = function () {
                console.log(i);   // 点击那个输出的就是那个按钮对应的i
            }
        }
        //因为let声明的变量，就相当于每次循环的时候，都重新创建一个循环变量
        ```
## 二.使用const声明变量
    > const和let完全相同，仅在于用const声明的变量，必须在声明的时候赋值，而且不可以重新赋值。
1. 实际开发中尽量使用const来声明变量，以保证变量的值，不会被篡改。原因如下：
    - 根据经验，开发中的很多变量，都是不会更改的，也不应该更改。
    ```js
    const div = document.getElementsByClassName('btn')[0];
    ```
    - 后续的很多框架或者是第三方的js库，都要求数据不可变，使用常量可以一定程度上保证这一点。
2. 注意的细节
    - 常量不可变，是指声明的常量的内存空间不可变，并不能保证内存空间中的地址指向的空间不可变。
    ```js
    const obj = {
        name:'chenjie',
        age:18
    }
    obj.name = 'xiaochen';  // 这里改变的是地址指向的空间，并不是改变const声明的变量的空间
    ```
    - 常量的命名：
        1. 特殊的常量：该常量从字面意思上就是不可变的，例如：圆周率，月地距离...通常情况下**该变量的命名全部用大写单词，如果多个单词用下划线分隔开。**
        ```js
        const PI = 3.14;
        const MOOD_EARTH = 3861025;  //月地距离
        ```
        2. 普通的变量：使用和之前一样的命名即可。
    - 在for循环中，循环变量不可以使用const，在forin循环中，可以使用const。
## 三.更好的Unicode支持
早期，由于存储空间宝贵，Unicode使用16位二进制来存储文字，我们将一个16位的二进制编码叫做一个码元（Code Unit）。
后来，由于技术的发展，Unicode对文字编码进行了扩展，将某些文字扩展到了32位（占用两个码元），并且将某个文字对应的二进制数字，叫做码点（Code Point）。
**也就是说16位的字是占用一个码元的，32位的数是占用两个码元**
但是我们的js有一段时间是十年没有更新的，所以，我们的js只会根据**码元**来取长度。如下代码：
```js
const text = '𠮷';
console.log(text.length); //2
console.log(/^.&/.test(text));  //正则是匹配一个文字 false
//明明是一个文字，但是长度为什么是2，因为我们的js是按照码元来看的，这是一个32位的数，他占两个码元。
```
ES6为了解决这个困扰，为字符串提供了一个方法：codePointAt，根据字符串码元的位置得到码点（如果这个数有两个码元，那么根据第一个码元得到这个文字的码点。要是用第二个码元的话，会直接返回该位置的码元）

同时，为正则表达式添加了一个flage：u,如果添加了该配置，则配对时，使用码点匹配。
```js
const text = '𠮷';
console.log('得到第一个码点: ' + text.codePointAt(0)); //返回的是码点
console.log('得到第二个码点: ' + text.codePointAt(1)); //返回的是第二个码元
```
1. 判断一个字符串是32位吗？
```js
function is32bit(str) {
    // 如果我们字符串的码点大于16进制的最大值，那么就是32位
    return str.codePointAt(0) > 0xffff;    
}
console.log(is32bit('𠮷'));  // true
```
2. 得到一个字符串的真实长度
```js
function is32bit(str, i) {
    return str.codePointAt(i) > 0xffff;    
}
console.log(is32bit('𠮷'));  // true

function getStringLength(str) {
    let len = 0;  
    // 注意：这里面的i在索引码元
    for(let i = 0; i < str.length; i++){
        if(is32bit(str, i)){
        // 如果是32位的话，进入到里面i++，调到了32位数的第二个码元上，然后执行外层的for循环，会再次++，跳过了32位第二个码元
            i++;
        }
        len++
    }
    return len;
}
console.log(getStringLength('abc𠮷ac'));
```

## 四.更多的字符串API
以下均为字符串的实例（原型）方法：
- includes
> 判断字符串中是否含有指定的字符串，第二个参数是从指定位置开始查找。  

```js
const cj = '小陈一定可以进大厂';
console.log(cj.includes('可以', 1)); //从索引为1的后面查找，看看是否包含‘可以’   true
```
- startsWith
> 判断字符串中是否以指定的字符串开始，第二个参数也可以指定开始的位置是哪里。
```js
const cj = '小陈一定可以进大厂';
console.log(cj.startsWith('小陈', 0)); //以索引为0为开始位置，看看开头是都包含‘小陈’   true
```
- endsWith
>判断字符串中是否以指定的字符串结尾。
```js
const cj = '小陈一定可以进大厂';
console.log(cj.endsWith('进大厂')); // true
```
- repeat
>将字符串重复指定的次数，然后返回一个新的字符串。
```js
const cj = '小陈一定可以进大厂';
const str = cj.repeat(6);
console.log(str); // 返回的是新的字符串
```
## [扩展]正则中的粘连标记
**标记名**： y
**含义**：匹配时完全按照正则对象中的lastIndex位置开始匹配，并且匹配的位置必须在lastIndex位置。默认是0，但是可以更改。
```js
const str = 'Hello World!!!';
// 正则匹配的是W后面跟任意字符，1到多次
const reg = /W\w+/y;
// 必须是lastIndex匹配上才返回true
console.log(reg.lastIndex);  // 0
console.log(reg.test(str));  //false
reg.lastIndex = 6;
console.log(reg.test(str));  //true
```

## 五.模板字符串
1. ES6之前处理字符串繁琐的两个方面：
    - 多行字符串
    ```js
    const str = '小陈后面想要换行\n转义字符';
    console.log(str);
    /*
        小陈后面想要换行
        转义字符
    */
    ```
    - 字符串拼接
    ```js
    const cj = '小陈';
    const str = '' + cj + '后面想要换行转义字符';
    console.log(str);  //小陈后面想要换行转义字符
    ```
2. 在ES6中提供了模板字符串的书写，可以非常方便的换行和拼接，要做的仅仅是将字符串的开始和结尾换成 ` 符号。
```js
const cj = '小陈';
const str = `${cj}后面想要换行
转义字符`;
console.log(str);
/* 
小陈后面想要换行
转义字符
*/
```

- 在模板字符串中也可以使用转义字符
```js
const cj = '小陈';
const str = `${cj}后面想要换行
转义字符\${cj}`;
console.log(str);
/* 
小陈后面想要换行
转义字符${cj}
*/
```
## [扩展]模板字符串标记
1. 在模板字符串书写之前可以先加上标记：
```js
标记名`模板字符串`
```
2. 标记是一个函数，函数的参数如下：
    - 参数1：被插值分割的字符串数组
    - 后续参数：所有的插值
```js
const cj = '小陈';
const a = '啊';
const str = myTag`${cj}后面想要换行${a}转义字符`;
// 相当于
// myTag(['', '后面想要换行', '转义字符'], '小陈', '啊')
```
3. 封装一个模板字符串的拼接功能
```js
function myTag(arr) {
    // 取到插值
    const values = Array.prototype.slice.apply(arguments).slice(1);
    // 插值的长度+1 = 拼接字符的长度
    // arr.length = values.length + 1;
    let str = '';
    for (let i = 0; i < values.length; i++) {
        str += arr[i] + values[i];
        if(i === values.length - 1){
            str += arr[i+1];
        }
    }
    return str;
}
console.log(str);
```
4. 插值里面出现特殊字符处理。
```html
 <!-- 当用户点击按钮的时候，把我们输入的值，放到div里面 -->
    <p>
        <textarea class="txt"></textarea>
        <button class="btn">设置div的内容</button>
    </p>
    <div class="container"></div>
```
```js
const container = document.getElementsByClassName('container')[0];
const txt = document.getElementsByClassName('txt')[0];
const btn = document.getElementsByClassName('btn')[0];
console.log();
btn.onclick = function () {
   container.innerHTML = myTag`<p>
        ${txt.value}
   </p>`
}
// 我们还可以对里面的插值进行操作，不用转义字符，就实现特殊的符号

function myTag(arr) {
    // 取到插值
    const values = Array.prototype.slice.apply(arguments).slice(1);
    let str = '';
    for (let i = 0; i < values.length; i++) {
        // 将插值里面的特殊字符匹配出来，替换掉
        const v = values[i].replace(/</g, '&lt;').replace(/>/g, '&gt;');
        str += arr[i] + v;
        if(i === values.length - 1){
            str += arr[i+1];
        }
    }
    return str;
}
```
>这样如果我们在文本框里面输入`<h1></h1>`等特殊标签的时候，就可以直接显示出来，不会被转化成有意义的效果了。就会把标签输出出来，不会被当成代码解析。

## 六.函数
### 6-1参数默认值

>**使用**：我们在书写形参的时候，直接给形参赋值，付的值即为默认值。
这样一来，我们在代用函数的时候，如果没有给对应的实参（undefined），则会自动调用默认值。

```js
function fn(a = 1, b = 2, c) {
    return a + b +c;
}
// 假设我们前面两个数字会经常是1， 2
console.log(fn(undefined, undefined, 3));  // 6
console.log(fn(undefined, undefined, 4));  // 7
// 因为我们前两位传的是undefined，所以会调用默认值
console.log(fn(2, undefined, 4));  // 8
// 我们第一位传入了数字，所以不会调用默认值
```

>默认值不仅仅可以是数字，还可以是表达式之类的。

```html
<div class="container">
    <!-- 我们要在这里面创建多个div -->
</div>
```

```js
// 创建div元素   元素里面的内容   元素的父级
function creatElementDiv(ele = 'div', content = '', pare = document.getElementsByClassName('container')[0]) {
    const div = document.createElement(ele);
    if(content){
        div.innerHTML = content;
    }
    pare.appendChild(div);
}
creatElementDiv(undefined, '小陈一定可以进大厂', undefined);
creatElementDiv(undefined, '陈杰一定可以进大厂', undefined);
```

**面试题**：
```js
function getContent() {
    console.log('abc');
    return 'div';
}

function creatElementDiv(ele = getContent(), content = '', pare = document.getElementsByClassName('container')[0]) {
    const div = document.createElement(ele);
    if(content){
        div.innerHTML = content;
    }
    pare.appendChild(div);
}
creatElementDiv(undefined, '小陈一定可以进大厂', undefined);
creatElementDiv(undefined, '陈杰一定可以进大厂', undefined);
creatElementDiv('div', '陈杰一定可以进大厂', undefined);
// 请问这个abc被打印了几次？
// 打印了两次，只有是undefined的时候，才会调用默认值
```
### [扩展]对arguments的影响

严格模式下的arguments和参数没有映射这种关系。
```js
function arg(a, b){
    'use strict';
    a = 3;
    console.log(a,b);  // 3 2
    console.log(arguments[0], arguments[1]);  // 1 2
}
arg(1, 2);
// 在严格模式下，即使改变参数的值，arguments是不会跟着改变的。
```
如果给函数里面的形参，添加上*默认值*，arguments就相当于开启了严格模式的规则。

```js
function arg(a, b = 4){
    a = 3;
    console.log(a,b);  // 3 2
    console.log(arguments[0], arguments[1]);  // 1 2
}
arg(1, 2);   
```

### [扩展]留意暂时性死区

形参和ES6中的let和const声明一样，具有作用域，并且根据参数的声明顺序，存在暂时性死区。

```js
function fn(a, b = a) {
    console.log(a + b);
}
fn(1);   // 5
//先声明并赋值了a=1，然后b=a

function fn(a = b, b) {
    console.log(a + b);
}
fn(undefined, 1);   // 报错
//此时的b还没有进行赋值，所以会报错
/* 
其实我们的变量提升，然后存在暂时性死区中，当我们用到b的时候，b还在暂时性死区，所以会报错。
*/
```

### 6-2剩余参数

1. arguments的缺陷：
    - 如果和形参配合使用，容易导致混乱
    - 从语义上，使用arguments获取参数，由于形参缺失，无法从函数定义上理解函数的真实意图

2. ES6的剩余参数是专门用于收集末尾的所有参数，将其放置到一个形参的数组中。

```js
function sum(...args) {
    let sum = 0;
    // 将所有的剩余参数放到了一个形参args的数组中
    for (let i = 0; i < args.length; i++){
        sum += args[i];
    }
    return sum;
}
console.log(sum(1));
console.log(sum(1, 2));
console.log(sum(1, 2, 3));
console.log(sum(1, 2, 3, 4));
console.log(sum(1, 2, 3, 4, 5));
```

**需要注意的是：**
- 一个函数，仅能出现一个剩余参数。
- 一个函数，如果有剩余参数，剩余参数必须是最后一个参数。

### 6-3展开运算符

1. 对数组展开ES6

```js
function sum(...args) {
    let sum = 0;
    for (let i = 0; i < args.length; i++) {
        sum += args[i];
    }
    return sum;
}

function getLengthRandom(len) {
    let arr = [];
    for (let i = 0; i < len; i++) {
        arr.push(Math.random());
    }
    return arr;
}

// 一个长度为10的数组
/* 
剩余参数也是将参数组成数组，如果我们把数组传进去，那剩余参数就变成
[[数组是随机生成的]]这样
*/
console.log(getLengthRandom(10));
let arr = getLengthRandom(10);
/*
...arr是将数组的每一项展开，依次作为参数传递，
而不是把整个数组作为一个参数传递
相当于sum(1, 2, 3, 4, ...., 10)
 */
console.log(sum(...arr));
console.log(sum(1, 2, ...arr, 4, 6));
```
- 数组克隆

```js
const arr = [1, 2, 3, 4, 5, 6];
const newArr = [...arr];
console.log(newArr);  //[1, 2, 3, 4, 5, 6]
```

2. 对对象展开ES7

```js
const obj = {
    name: 'chenjie',
    age: 18,
    love: 'money'
}
const cloneObj = {
    ...obj
}
/* 
对象的展开就相当于{
    name:obj.name,
    age:obj.age,
    love:obj.love
}
*/
console.log(cloneObj);
```

- 对象的克隆问题

```js
const obj = {
    name: 'chenjie',
    age: 18,
    love: 'money'
}

const cloneObj = {
    ...obj,
    name:'xiaochen'   //覆盖掉克隆过来的name属性
}
console.log(cloneObj);
```
我们这样的克隆只是浅克隆。

```js
const obj = {
    name: 'chenjie',
    age: 18,
    loves:{
        people: 'girls',
        rich: 'money'
    } 
}

const cloneObj = {
    ...obj,
    name:'xiaochen'   
}
console.log(cloneObj);
// 对象里面的对象还是同一个地址
console.log(cloneObj.loves === obj.loves);  // true
```

深度克隆需要单独处理，并不是通用的。

```js
const obj = {
    name: 'chenjie',
    age: 18,
    loves:{
        people: 'girls',
        rich: 'money'
    } 
}

const cloneObj = {
    ...obj,
    name:'xiaochen',
    loves: {
        ...obj.loves
    }   
}
console.log(cloneObj);
console.log(cloneObj.loves === obj.loves);  // false
```
### 6-4明确函数的双重用途

1. 调用函数的两种方法：
    - new （构造函数）
    - 直接调用 （普通函数）

```js
function Person(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.fullName = `${firstName} ${lastName}`;
}
console.log(new Person('陈', '杰'));
// 最后return的是this对象
console.log(Person('陈', '杰')); //undefined
// 最后return的是undefined
```

2. 以前我们判断该函数是不是new调用的方法。

```js
function Person(firstName, lastName) {
    //判断this的原型链上，有没有Person，如果要不是new调用的，则抛出错误
    if(!(this instanceof Person)){
        throw new Error ('该函数没有使用new调用');
    }

    this.firstName = firstName;
    this.lastName = lastName;
    this.fullName = `${firstName} ${lastName}`;
}
console.log(new Person('陈', '杰'));

console.log(Person('陈', '杰')); //报错
```

3. ES6新增的API(new.target)

```js
function Person(firstName, lastName) {
    // console.log(new.target);
    if(new.target == undefined){
        throw new Error('该函数没有使用new调用');
    }
    this.firstName = firstName;
    this.lastName = lastName;
    this.fullName = `${firstName} ${lastName}`;
}

new Person('陈', '杰');
// 如果是new调用函数，则返回后面的函数本身
// 如果不是new调用函数，则返回undefined
Person('陈', '杰'); //报错
```

### 6-5箭头函数

1. 回顾this指向问题：
    - 通过对象调用函数，this指向对象
    - 直接调用函数， this指向全局对象   
    - 如果通过new调用函数，this指向新创建的对象
    - 如果通过apply,call,bind调用函数，this指向指定的数据
    - 如果是DOM事件函数，this指向事件源

2. 语法使用：
>箭头函数是一个函数表达式，理论上，任何使用函数表达式的场景都可以使用箭头函数。

```js
// 箭头函数语法：
1. (参数1， 参数2，、、、) =>{
    //函数体
}
2.如果里面只有一个参数，可以省略括号
参数=>{
    //函数体
}
3.如果函数里面只有一个返回值，那么大括号和return都可以省略
参数=>返回值

```
```js
// 判断一个数是不是奇数(以前的写法)
function isOdd(num){
    return num % 2 !== 0; 
}

console.log(isOdd(3)); //true
console.log(isOdd(4)); // false

// 箭头函数的写法1
const isOdd = (num)=>{
    return num % 2 !== 0;
}
console.log(isOdd(3)); //true
console.log(isOdd(4)); //false
//箭头函数的写法2
const isOdd = num =>{
    return num % 2 !== 0;
}
console.log(isOdd(3)); //true
console.log(isOdd(4)); //false
// 箭头函数的写法3
const isOdd = num=>num % 2 !== 0;
console.log(isOdd(3)); //true
console.log(isOdd(4)); //false
```

3. 箭头函数的细节注意事项：
    - 箭头函数的简写，如果函数体内只有一个返回的表达式，如果该表达式是对象的话，并不能直接的简写。

    ```js
    const obj = (num1, num2) = >{
        num1: 'a',
        num2: 'b'
    }
    obj();
    /* 
    虽然返回的是一个对象，花括号省略了，但是系统会把对象的花括号
    当成函数的花括号，就相当于直接把键值对写在了函数体里面，指定报错
    */
    //解决办法：
    const obj = (num1, num2) = >({
        num1: 'a',
        num2: 'b'
    })
    obj();
    //给对象填上一个括号，这样就变成表达式了
    ```

    - 箭头函数中，不存在this、arguments、new.target，如果使用了，则使用的是函数外层的对应的this、arguments、new.target

    ```js
    const obj = {
        count: 0,
        add: function () {
            const _this = this;
            setInterval(function () {
            console.log( _this.count++);
            }, 1000)
        }
    }
    obj.add();
    // 普通的函数实现，让count++

    const obj = {
        count: 0,
        add: function () {
            //里面的this是本层的this，因为箭头函数里面没有this
            setInterval(()=>{
                this.count++;
                console.log(this.count);
            }, 1000)
        }
    }
    add();
    //箭头函数里面没有this，他的this是外层的。
    ```
    巩固提升
    ```js
    const obj = {
        method:function(){
            const func = ()=>{
                console.log(this);  // obj
                console.log(arguments); //234
            }
            func();
        }
    }
    obj.method(234);
    //解释一下：因为我们的是箭头函数，里面没有this和arguments，他们身上的其实是上一层的。也就是method的。
    ```
    - 在对象的属性后面不可以直接写箭头函数

    ```js
    const obj = {
        count: 0,
        print: function () {
            // console.log(this)
            console.log(this.count)
        }
    }
    print();
    //要是普通函数的话，我们的this指向的是调用者
    //要是换成箭头函数的话  
    const obj = {
        count: 0,
        print: ()=>{
            // console.log(this)
            console.log(this.count)
        }
    }
    obj.print();
    //箭头函数的话，里面没有this，那也就是说，这个this是对象身上的，this不在函数里面就指向了window
    ```
    - 箭头函数没有原型
    - 箭头函数不能作为构造函数使用，因为他都没有原型
    
4. 箭头函数的应用场景
    - 临时性使用的函数，并不会可以调用它，比如：
        - 事件处理函数
        - 异步处理函数
        - 其他临时性函数
    ```js
    事件处理函数：  
    window.onclick = ()=>{
        console.log('事件处理函数');
    }
    异步处理函数：
    setInterval(()=>{
        console.log('异步处理函数');
    }, 1000)
    ```
    - 为了绑定外层的this函数

    ```js
    const obj = {
        count: 0,
        add: function () {
            setInterval(()=>{
                this.count++;
                console.log(this.count);
            }, 1000)
        }
    }
    obj.add();
    //定时器里面的this就是为了绑定外层函数的this
    ```
    - 在不影响其他代码的情况下，保持代码的简洁，最常见的，数组方法中的回调函数

    ```js
    const number = [10, 20, 25, 30, 35];
    const result = number.filter(item=>item%2!==0).map(item=>item*2).reduce((pre,item)=>pre+item);
    console.log(result);
    ```
## 七.对象

### 7-1新增的对象字面量语法

1. 成员速写

    >如果对象字面量初始化时，成员的名称来自于一个变量，并且和变量的名称相同，则可以进行简写
    
    ```js
    function creatPeople(uname, age, id) {
        return {
            uname,
            age,
            id,
            sex:'男'
        }
    }
    console.log(creatPeople('陈杰', 18, 130));
    ```

2. 方法速写
    > 对象字面初始化时，方法可以省略冒号和function关键字

    ```js
    const user = {
        uname: 'cj',
        age:18,
        sayHello() {
            console.log('hello');
        }
    }
    user.sayHello(); //hello
    ```

3. 计算属性名
    >有的时候，初始化对象时，某些属性名可能来自于某个表达式的值，在ES6，可以使用中括号来表示该属性名是通过计算得到的。

    ```js
    const prop1 = 'uname';
    const prop2 = 'sayHello';
    const obj = {
        [prop1]: 'cj',
        age:18,
        [prop2]() {
            console.log('hello');
        }
    }
    console.log(obj);
    obj.sayHello();
    ```
### 7-2Object的新增API

1. Object.is(数据， 数据)
    >用于判断两个数据是否相等，基本上就和严格全等（===）一样，除了一下两点：
    NaN和NaN相等，+0不等于-0。

    ```js
    console.log(NaN === NaN);  // false
    console.log(-0 === +0);    // true

    console.log(Object.is(NaN, NaN)); // true
    console.log(Object.is(-0, +0));  // false
    ```

2. Object.assign(对象1， 对象2，...)
    >用于将多个对象混合到一个对象里面，如果里面的属性名重复，则覆盖掉前面的属性，这个的缺点就是传的第一个对象会被修改后，返回。

    ```js
    const obj = {
        a: 123,
        b: 456,
        c: 'chen'
    }
    const obj1 = {
        a : 'jie',
        d: 7789
    }
    Object.assign(obj, obj1);
    console.log(obj); //被修改了
    //如果想不被修改的话：
    const obj3 = {
        ...obj,
        ...obj1
    }
    console.log(obj3); // 合并的对象
    console.log(obj);  // 以前的对象没有改变

    //第二种方法：
    Object.assign({}, obj1, obj2);
    ```

3. Object.getOwnPropertyNames 的枚举顺序(打印出来的数属性，返回新的数组)
    >Object.getOwnPropertyNames方法之前就存在，只不过，官方没有明确要求，对属性的顺序如何排序，如何排序，完全由浏览器厂商决定
    ES6规定了该方法返回的数组的排序方式如下：
        - 先排数字，并按照升序排序
        - 再排其他，按照书写顺序排序

4. Object.setPrototypeOf()
    >该函数用于设置对象的隐式原型

    比如：Object.setProprotypeOf(obj1, obj2);
    相当于：```obj1.__proto__ = obj2```

    ```js
    const obj = {
        a: 1
    }
    const obj1 = {
        b: 2
    }
    Object.setPrototypeOf(obj, obj1);
    console.log(obj); // __proto = obj1
    ```

### 7-3面向对象
1. 类:构造函数的语法糖

    - 传统构造函数的问题

    1. 属性和原型方法定义分离，降低了可读性
    2. 原型成员可以被枚举
    3. 默认情况下，构造函数仍然可以被当作普通函数使用
    ```js
    function Animal(type, name, age, sex) {
        this.type = type;
        this.name = name;
        this.age = age;
        this.sex = sex;
    }
    Animal.prototype.print = function () {
        console.log(this.type);
        console.log(this.name);
        console.log(this.age);
        console.log(this.sex);
    }
    const a = new Animal('狗', '旺财', 3, '公');
    a.print(); //'狗', '旺财', 3, '公'
    ```

    - 类的特点
    1. 类声明不会被提升，与 let 和 const 一样，存在暂时性死区
    2. 类中的所有代码均在严格模式下执行
    3. 类的所有方法都是不可枚举的
    4. 类的所有方法都无法被当作构造函数使用
    5. 类的构造器必须使用 new 来调用

    ```js
    class Animal {
        constructor(type,name,age,sex){
            this.type = type;
            this.name = name;
            this.age = age;
            this.sex = sex;
        }

        print(){
            console.log(this.type);
            console.log(this.name);
            console.log(this.age);
            console.log(this.sex);
        }
    }
    const a = new Animal('狗', '旺财', 3, '公');
    a.print();  //'狗', '旺财', 3, '公'
    //解释下第四条
    new a.print();  //这样不可以
    ```
2. 类的其他书写方式

    - 可计算的成员名

    ```js
    const printName = 'print';
    class Animal {
        constructor(type, name, age, sex) {
            this.type = type;
            this.name = name;
            this.age = age;
            this.sex = sex;
        }

        [printName]() {
            console.log(this.type);
            console.log(this.name);
            console.log(this.age);
            console.log(this.sex);
        }
    }
    const a = new Animal('狗', '旺财', 3, '公');
    a[printName]();
    ```

    - getter和setter
    >使用getter和setter创建的属性不在原型上，在构造函数身上

    ```js
    class Animal {
        constructor(type, name, age, sex) {
            this.type = type;
            this.name = name;
            this.age = age;
            this.sex = sex;
        }
        // 创建一个age属性，读取该属性的时候，会调用该函数
        get age() {
            return this._age + '岁';
        }
        // 给构造函数创建一个age属性，给该属性赋值的时候，会调用该函数
        set age(val) {
            if (val > 1000) {
                val = 1000;
            }
            if (val < 0) {
                val = 0;
            }
            this._age = val;
        }
        print() {
            console.log(this.type);
            console.log(this.name);
            console.log(this.age);
            console.log(this.sex);
        }
    }
    const a = new Animal('狗', '旺财', 3, '公');
    a.print();
    //当我们在调用函数的时候，我们的this.age=3了，此时赋值了，就调用set age()这个函数，在调用print函数的时候，我们是读取数据，就调用get age();
    ```
    - 静态成员
    什么是静态成员呢？
    >我们的构造函数，有一个原型prototype，这个原型身上的属性和方法，都是实例成员。同时，构造函数还会通过new实例化对象，这个对象身上的属性和方法也是实例化成员。但是我们的**构造函数自己身上的属性和方法，就被我们成为静态成员。**

    在类中，我们通过static关键字定义静态成员：

    ```js
    class Jt {
        constructor(name){
            // 实例成员
            this.uname = name;
        }
        static age = 18;
    }
    const jt = new Jt('chenjie');
    console.log(Jt.age);   //18 在构造函数身上
    ```

    - 字段初始化器(ES7)

    1). 使用static的字段初始化器，添加的是静态成员
    2). 没有使用static的字段初始化器，添加的成员位于对象上
    3). 箭头函数在字段初始化器位置上，指向当前对象

    ```js
    class Test {
        //静态成员，在类的身上，构造函数本身
        static a = 1;
        //他们就相当于写在了constructor构造函数里面
        b = 2;
        c = 3;
    }
    const t = new Test();
    console.log(t)  // b=2 c=3
    console.log(Test.a);  // 1
    ```
    为了把this绑定在构造函数身上：

    ```js
    class Test {
        static a = 1;
        b = 2;
        c = 3;
        constructor() {
            this.d = 4;
        }
        print() {
            console.log(this.d);
        }
    }
    const t = new Test();
    const p = t.print;
    p();  //undefined
    /**
     *因为我们写在类里面的代码相当于是严格模式，p函数调用，this指向的是window，但是在严格模式下不指向window，直接报错。
    **/

    class Test {
        static a = 1;
        b = 2;
        c = 3;
        constructor() {
            this.d = 4;
        }
        print = () => {
            console.log(this.d);
        }
    }
    const t = new Test();
    const p = t.print;
    p();  // 4

    /**
     *字段初始化器就相当于在构造函数里面，和以前的区别，以前的print是在原型上，而这个就在我们的实例化对象身上。
    **/
    ```

    - 类表达式

    ```js
    const A = class { //匿名类，类表达式
        a = 1;
        b = 2;
    }
    
    const a = new A();
    console.log(a)
    ```

3. 类的继承

    - 复习一下以前的方法

    ```js
    function Factory(name, age, wheel) {
        this.name = name;
        this.age = age;
        this.wheel = wheel;
    }
    Factory.prototype.fix = '父类的原型';

    function Car(uname, uage, uwheel, type) {
        Factory.call(this, uname, uage, uwheel);
        this.type = type;
    }
    // 下面这个将子类的原型的__proto__指向了父类的原型
    Object.setPrototypeOf(Car.prototype, Factory.prototype);
    var car = new Car('玛莎拉蒂', '1', '圆的', '敞篷');
    console.log(car);
    console.log(car.fix); //可以通过子类调用父类的原型
    //通过孩子去调用父亲的属性
    ```

    - 类的继承
    如果两个类A和B，如果可以描述为：B 是 A，则，A和B形成继承关系
    如果A是B的父类，则B会自动拥有A中的所有实例成员。也就是实例化对象里面的所有属性方法，以及原型上的所有属性方法。

        - 新的关键词
        1. extends:继承，用于类的定义。
        2. super
            - 直接当作函数调用，表示父类构造函数
            - 如果当作对象使用，则表示父类的原型
        3.注意：ES6要求，如果定义了constructor，并且该类是子类，则必须在constructor的第一行手动调用父类的构造函数。
        如果子类不写constructor，则会有默认的构造器，该构造器需要的参数和父类一致，并且自动调用父类构造器。

    ![原型链](assets/原型链.png)

    ```js
    class Animal {
        constructor(type, name, age, sex) {
            this.type = type;
            this.name = name;
            this.age = age;
            this.sex = sex;
        }
        print() {
            console.log(`【种类】：${this.type}`);
            console.log(`【名字】：${this.name}`);
            console.log(`【年龄】：${this.age}`);
            console.log(`【性别】：${this.sex}`);
        }
    }

    class Dog extends Animal {
    
    }

    const dog = new Dog('旺财', 3, '公');
    dog.print();
    /**
    【种类】：旺财
    【名字】：3
    【年龄】：公
    【性别】：undefined 
    这是子类没有写constructor的时候，自动创建了一个跟父类一样的，参数传的错位啦
    **/
    ```

    ```js
    class Animal {
        constructor(type, name, age, sex) {
            this.type = type;
            this.name = name;
            this.age = age;
            this.sex = sex;
        }
        print() {
            console.log(`【种类】：${this.type}`);
            console.log(`【名字】：${this.name}`);
            console.log(`【年龄】：${this.age}`);
            console.log(`【性别】：${this.sex}`);
        }
    }
    class Dog extends Animal {
        constructor(name, age, sex) {
            super("犬类", name, age, sex);
            // 子类特有的属性
            this.loves = "吃骨头";
        }
        print() {
            //调用父类的print
            super.print();
            //自己特有的代码
            console.log(`【爱好】：${this.loves}`);
        }
    }
    const dog = new Dog('旺财', 3, '公');
    dog.print(); 
    /**
    【种类】：犬类
    【名字】：旺财
    【年龄】：3
    【性别】：公
    【爱好】：吃骨头 
    继承了父类的属性，还有父类的原型上的方法
    **/  
    ```

    ```js
    class Animal {
        constructor(type, name, age, sex) {
            this.type = type;
            this.name = name;
            this.age = age;
            this.sex = sex;
        }

        print() {
            console.log(`【种类】：${this.type}`);
            console.log(`【名字】：${this.name}`);
            console.log(`【年龄】：${this.age}`);
            console.log(`【性别】：${this.sex}`);
        }

        jiao(){
            throw new Error("动物怎么叫的？");
        }
    }

    class Dog extends Animal {
        constructor(name, age, sex) {
            super("犬类", name, age, sex);
            // 子类特有的属性
            this.loves = "吃骨头";
        }

        print(){
            //调用父类的print
            super.print();
            //自己特有的代码
            console.log(`【爱好】：${this.loves}`);
        }


        //同名方法，会覆盖父类
        jiao(){
            console.log("旺旺！");
        }
    }

    const d = new Dog("旺财", 3, "公");
    d.print();
    console.log(d)
    d.jiao();
    //同名的方法会重写
    ```
    [冷知识]
    - 用JS制作抽象类
    - 抽象类：一般是父类，不能通过该类创建对象
    - 正常情况下，this的指向，this始终指向具体的类的对象

## 八.解构
### 8-1对象解构
1. 什么是解构?
>使用ES6的一种语法规则，将一个对象或数组的某个属性提取到某个变量中。

```js
const obj = {
    uname: 'chenjie',
    age: 18,
    sex: '男',
    loves: {
        hobby: 'nv'
    }
}
// 首先是声明和属性同名的变量
// 我们还可以进一步的结构
let unmae, age, sex, hobby;
({ uname, age, sex, loves: { hobby } } = obj);
console.log(uname, age, sex, hobby);  //chenjie 18 男 nv
```

2. 在结构中使用默认值

```js
{同名变量=默认值}
```
```js
const obj = {
    uname: 'chenjie',
    age: 18,
    sex: '男',
    loves: {
        hobby: 'nv'
    }
}
let unmae, age, sex, cj;
({ uname, age, sex='女, cj='xiaochen'} = obj);
console.log(uname, age, sex, cj);  //chenjie 18 男 xiaochen
//我们的属性要是有值的话，我们的默认值不起作用，和函数的默认值似的
```

3. 非同名属性结构

```js
{同名属性：变量名}
```
```js
const obj = {
    uname: 'chenjie',
    age: 18,
    sex: '男',
    loves: {
        hobby: 'nv'
    }
}
let { uname, age, sex:gender, cj='xiaochen'} = obj;
console.log(uname, age, gender, cj);  //chenjie 18 男 xiaochen
```

### 8-2数组解构

```js
const arr = [1, 23, { uname: 'chenjie' }, 86];
let [, a, { uname: name }, b] = arr;
console.log(a, name, b);  //23 "chenjie" 86
```

说剩余属性：
```js
const obj = {
    uname: 'chenjie',
    age: 18,
    sex: '男',
    loves: {
        hobby: 'nv'
    }
}
// 我们把uname属性解构出来，然后把剩下的其他属性解构到一个对象里
let { uname: name, ...obj1} = obj;
console.log(name,obj1);
```

- 练习题
```js
const article = {
    title: '文章标题',
    cotent: '文章内容',
    comments: [{
        content: '评论1',
        user: {
            id: 1,
            uname: '用户名1'
        }
    }, {
        content: '评论2',
        user: {
            id: 2,
            uname: '用户名2'
        }
    }]
}
// 解构出第二条评论的用户名和评论内容
let { comments: [, { user: { id, uname } }] } = article;
console.log(id, uname);
```
### 8-3参数解构

```js
// 以前的方法
// function print(user) {
//     console.log(`姓名：${user.name}`)
//     console.log(`年龄：${user.age}`)
//     console.log(`性别：${user.sex}`)
//     console.log(`身份：${user.address.province}`)
//     console.log(`城市：${user.address.city}`)
// }

function print({ name, age, sex, address: { province, city } }) {
    console.log(`姓名：${name}`)
    console.log(`年龄：${age}`)
    console.log(`性别：${sex}`)
    console.log(`身份：${province}`)
    console.log(`城市：${city}`)
}

const user = {
    name: "kevin",
    age: 11,
    sex: "男",
    address: {
        province: "四川",
        city: "成都"
    }
}
print(user)
```

## 九.符号
### 9-1 普通符号(symbol)

>符号是ES6新增的一个数据类型，它通过使用函数 ```Symbol(符号描述)``` 来创建

>符号设计的初衷，是为了给对象设置私有属性

>私有属性：只能在对象内部使用，外面无法使用

符号具有以下特点：

- 没有字面量
- 使用 typeof 得到的类型是 symbol
- **每次调用 Symbol 函数得到的符号永远不相等，无论符号名是否相同**
- 符号可以作为对象的属性名存在，这种属性称之为符号属性
  - 开发者可以通过精心的设计，让这些属性无法通过常规方式被外界访问
  - 符号属性是不能枚举的，因此在 for-in 循环中无法读取到符号属性，Object.keys 方法也无法读取到符号属性
  - Object.getOwnPropertyNames 尽管可以得到所有无法枚举的属性，但是仍然无法读取到符号属性
  - ES6 新增 Object.getOwnPropertySymbols 方法，可以读取符号
- 符号无法被隐式转换，因此不能被用于数学运算、字符串拼接或其他隐式转换的场景，但符号可以显式的转换为字符串，通过 String 构造函数进行转换即可，console.log 之所以可以输出符号，是它在内部进行了显式转换

```js
// 因为里面的属性是唯一的
const obj = {
    a: 1,
    b: 2,
    [Symbol('chenjie')]: 5
}
console.log(obj[Symbol('chenjie')]);
```

```js
// 创建普通符号
const syb = Symbol('chenjie');
const syb1 = Symbol(123);
console.log(syb,syb1);//Symbol(chenjie) Symbol(123)
console.log(typeof syb === "symbol", typeof syb1 === "symbol") //true true
```
```js
// 符号属性
const syb1 = Symbol("这是用于对象的一个属性");
const obj = {
    a: 1,
    b: 2,
    [syb1]: 3  //符号属性
}
console.log(obj);
/* 
a: 1
b: 2
Symbol(这是用于对象的一个属性): 3
*/
```

```js
const hero = (function () {
    const getRandom = Symbol();
    return {
        attack: 30,
        hp: 300,
        defence: 10,
        gongji() { //攻击
            //伤害：攻击力*随机数（0.8~1.1)
            const dmg = this.attack * this[getRandom](0.8, 1.1);
            console.log(dmg);
        },
        [getRandom](min, max) { //根据最小值和最大值产生一个随机数
            return Math.random() * (max - min) + min;
        }
    }
})()
console.log(hero);
console.log(hero.getRandom); //undeifned
console.log(hero[getRandom]); //报错
/* 
我们的getRandom属性不想让外面的访问，因为，没有什么用。我们的符号属性是个字符串，所以不能用点的方式获取，又因为是局部变量，所以在函数外面访问不到。
*/
```

```js
// 符号属性是不可以枚举的。因此在forin循环中或者其他方法中是不可以枚举的。
const obj = {
    [syb]: 1,
    a: 2,
    b: 3
}

for (const prop in obj) {
    console.log(prop)
}

console.log(Object.keys(obj))
console.log(Object.getOwnPropertyNames(obj))
//得到的是一个符号属性的数组
const sybs = Object.getOwnPropertySymbols(obj);
console.log(sybs, sybs[0] === syb)    
```

### 9-2共享符号
>根据某个符号名称（符号描述）能够得到同一个符号.

```js
const syb = Symbol.for('chenjie');
const syb1 = Symbol.for('chenjie');

console.log(syb === syb1); //true
//他们是相等的。
```
```js
const syb = Symbol.for('chenjie');
const syb1 = Symbol.for('chenjie');
const obj = {
    a:1,
    b:2,
    [syb]:3
}
const obj1 = {
    a:2,
    b:3,
    [syb1]:'c'
}
console.log(obj,obj1);
//他们都是这个属性Symbol.for('chenjie')，因为他们不是特殊的。
```

Symbol.for的实现原理：

```js
// Symbol.for 的内部实现原理
const SymbolFor = (() => {
    const symbol = {};
    return function (name) {
        if (!symbol[name]) {
            symbol[name] = Symbol();
        }
        return symbol[name];
    }
})()
const syb1 = SymbolFor('abc');
const syb2 = SymbolFor('abc');
console.log(syb1 === syb2); //true
```

### 9-3知名符号

>知名符号是一些具有特殊含义的共享符号，通过 Symbol 的静态属性得到

>ES6 延续了 ES5 的思想：减少魔法，暴露内部实现！

>因此，ES6 用知名符号暴露了某些场景的内部实现(就是js某些功能的内部实现，我们可以通过这些Symbol静态属性更改)


1. Symbol.hasInstance
>该符号用于定义构造函数的静态成员，影响**instanceof**的判定。

```js

obj instanceof A

//等效于

A[Symbol.hasInstance](obj) // Function.prototype[Symbol.hasInstance]

```
```js
function A() {

}

const obj = new A();
Object.defineProperty(A, Symbol.hasInstance, {
    value: function (obj) {
        return false;
    }
})
//直接影响到了instanceof的值，也就是改变了js的内部实现
console.log(obj instanceof A);  //fasle
console.log(A[Symbol.hasInstance](obj)); //false
```

2. [扩展] Symbol.isConcatSpreadable

    该知名符号会影响数组的 concat 方法

```js
const arr = [3];
const arr2 = [5, 6, 7, 8];
arr2[Symbol.isConcatSpreadable] = false;
const result = arr.concat(56, arr2)

//  [3, 56, [5,6,7,8]]
//  [3, 56, 5, 6, 7, 8]
console.log(result)
// 数组和对象合并
const arr = [1];
const obj = {
    0: 3,
    1: 4,
    length: 2,
    [Symbol.isConcatSpreadable]: true
}

const result = arr.concat(2, obj)

console.log(result)
```

3. [扩展] Symbol.toPrimitive

    该知名符号会影响类型转换的结果

```js
class Temperature {
    constructor(degree) {
        this.degree = degree;
    }

    [Symbol.toPrimitive](type) {
        if (type === "default") {
            return this.degree + "摄氏度";
        }
        else if (type === "number") {
            return this.degree;
        }
        else if (type === "string") {
            return this.degree + "℃";
        }
    }
}

const t = new Temperature(30);

console.log(t + "!");
console.log(t / 2);
console.log(String(t));
```

4. [扩展] Symbol.toStringTag

    该知名符号会影响 Object.prototype.toString 的返回值

```js
class Person {

    [Symbol.toStringTag] = "Person"
}

const p = new Person();

const arr = [32424, 45654, 32]

console.log(Object.prototype.toString.apply(p));[object Person]
console.log(Object.prototype.toString.apply(arr))[object Array]
```

5. 其他知名符号

## 十.异步处理
### 10-1[回顾]事件循环


1. **执行栈**：call stack，一个数据结构，用于存放各种函数的执行环境，每一个函数执行之前，它的相关信息会加入到执行栈。函数调用之前，创建执行环境，然后加入到执行栈；函数调用之后，销毁执行环境。

>JS引擎永远执行的是执行栈的最顶部。

*通俗的说：*我们的js的执行过程，首先会在我们的栈中放一个全局上下文，然后，没遇到函数的执行，就会在栈中生成一个函数上下文，上下文执行完就被销毁。**注意，只能执行栈中的最顶层的上下文，也就是说只有在顶层的上下文执行完被销毁之后，才能执行前一个被放进来的上下文。**

2. **异步函数**：某些函数不会立即执行，需要等到某个时机到达后才会执行，这样的函数称之为异步函数。比如*事件处理函数*，*定时器函数*，*ajax回调函数*。异步函数的执行时机，会被宿主环境控制。

>浏览器宿主环境中包括5个线程：
    1.JS引擎：负责执行栈的最顶部的代码。
    2.GUI引擎：负责渲染页面。
    3.事件监听线程：负责监听各种事件。
    4.计时线程：负责计时。
    5.网络线程：负责网络通信。
**事件队列：当上面的线程发生了某些事请，如果该线程发现，这件事情有处理程序，它会将该处理程序加入一个叫做事件队列的内存。当JS引擎发现，执行栈中已经没有了任何内容后，会将事件队列中的第一个函数加入到执行栈中执行。**

3. **事件队列**：在不同的宿主环境(浏览器，node等执行环境)中有所差异，大部分宿主环境会将事件队列进行细分。在浏览器中，事件队列分为两种：

- 宏任务(队列)：macrToask,计时器结束的回调，事件回调，http回调等等绝大部分异步函数进入宏任务队列。
- 微任务（队列）：MutationObserver(H5的)，Promise产生的回调进入微队列。
**执行顺序**：首先我们的代码正常执行 *栈的最顶端*，如果有*异步函数的时候*，会将异步函数放到另一个线程当中。等到执行栈当中的所有代码都执行完的时候，会执行微队列的代码，等到微队列执行完，在执行宏队列的代码。
>MutationObserver用于监听某个DOM对象的变化。

```js
//监听ul
const observer = new MutationObserver(function () {
    //当监听的dom元素发生变化时运行的回调函数
    console.log("ul元素发生了变化")
})
//监听ul
observer.observe(ul, {
    attributes: true, //监听属性的变化
    childList: true, //监听子元素的变化
    subtree: true //监听子树的变化
})
//取消监听
// observer.disconnect();
```


### 10-2. 事件和回调函数的缺陷

我们习惯于使用传统的回调或事件处理来解决异步问题(**自己的理解，就是我们需要在我们的异步函数执行完之后，要做的事情**)

**事件**：某个对象的属性是一个函数，当发生某一件事时，运行该函数

```js
dom.onclick = function(){

}
```

**回调**：运行某个函数以实现某个功能的时候，传入一个函数作为参数，当发生某件事的时候，会运行该函数。

```js
dom.addEventListener("click", function(){

})
```

本质上，事件和回调并没有本质的区别，只是把函数放置的位置不同而已。

一直以来，该模式都运作良好。

直到前端工程越来越复杂...

目前，该模式主要面临以下两个问题：

1. 回调地狱：某个异步操作需要等待之前的异步操作完成，无论用回调还是事件，都会陷入不断的嵌套
2. 异步之间的联系：某个异步操作要等待多个异步操作的结果，对这种联系的处理，会让代码的复杂度剧增(就是我们要做的事情也是异步的（我们要做的事情是需要在异步完成之后做的），需要等待我们前面的异步执行完)
*举个栗子：我们的ajax请求是异步的，我们处理ajax的数据需要在异步请求之后处理数据，但是我们有个要求，在处理数据的时候，还有一个异步任务。此时的异步要想完成，需要等待前面的ajax异步请求结束。*

```html
    <p>
        <button id="btn1">按钮1：给按钮2注册点击事件</button>
        <button id="btn2">按钮2：给按钮3注册点击事件</button>
        <button id="btn3">按钮3：点击后弹出hello</button>
    </p>
```
```js
    // 回调地狱，点击按钮1给按钮2注册事件，点击按钮2给按钮3注册事件
    const btn1 = document.getElementById("btn1"),
            btn2 = document.getElementById("btn2"),
            btn3 = document.getElementById("btn3");
        btn1.addEventListener("click", function() {
            //按钮1的其他事情
            btn2.addEventListener("click", function() {
                //按钮2的其他事情
                btn3.addEventListener("click", function() {
                    alert("hello");
                })
            })
        })
```

```js
        /*
            邓哥心中有三个女神
            有一天，邓哥决定向第一个女神表白，如果女神拒绝，则向第二个女神表白，直到所有的女神都拒绝，或有一个女神同意为止
            用代码模拟上面的场景
        */
        function biaobai(god, callback) {
            console.log(`邓哥向女神【${god}】发出了表白短信`);
            setTimeout(() => {
                if (Math.random() < 0.1) {
                    //女神同意拉
                    //resolve
                    callback(true);
                } else {
                    //resolve
                    callback(false);
                }
            }, 1000);
        }
        biaobai("女神1", function (result) {
            if (result) {
                console.log("女神1答应了，邓哥很开心!")
            } else {
                console.log("女神1拒绝了，邓哥表示无压力，然后向女神2表白");
                biaobai("女神2", function (result) {
                    if (result) {
                        console.log("女神2答应了，邓哥很开心!")
                    } else {
                        console.log("女神2十分感动，然后拒绝了邓哥，邓哥向女神3表白");
                        biaobai("女神3", function (result) {
                            if (result) {
                                console.log("女神3答应了，邓哥很开心!")
                            } else {
                                console.log("邓哥表示生无可恋!!");
                            }
                        })
                    }
                })
            }
        })

/* 
小陈在这里隆重的解释一下：邓哥并是不花心，只是好色罢了。解释完邓哥，下面就是代码，第一次调用biaobai函数，等到函数里面的定时器异步任务执行完的时候，会调用我们的回调函数。也就是说，调用函数->输出邓哥向女神发送短信->定时器异步任务（执行）->执行的时候会调用回调函数。
*/
```

```js
// 异步之间的联系：要记录日志，就得等上一个异步，所有的女神回复完，还得等上上个异步，以此类推
        /*
            邓哥心中有二十个女神，他决定用更加高效的办法
            他同时给二十个女神表白，如果有女神同意，就拒绝其他的女神
            并且，当所有的女神回复完成后，他要把所有的回复都记录到日志进行分析
            用代码模拟上面的场景
        */
        function biaobai(god, callback) {
            console.log(`邓哥向女神【${god}】发出了表白短信`);
            setTimeout(() => {
                if (Math.random() < 0.05) {
                    //女神同意拉
                    callback(true);
                } else {
                    callback(false);
                }
            }, Math.floor(Math.random() * (3000 - 1000) + 1000));
        }
        let agreeGod = null; //同意邓哥的第一个女神
        const results = []; //用于记录回复结果的数组
        for (let i = 1; i <= 20; i++) {
            biaobai(`女神${i}`, result => {
                results.push(result);

                if (result) {
                    console.log(`女神${i}同意了`)
                    if (agreeGod) {
                        console.log(`邓哥回复女神${i}: 不好意思，刚才朋友用我手机，乱发的`)
                    } else {
                        agreeGod = `女神${i}`;
                        console.log(`邓哥终于找到了真爱`);
                    }
                } else {
                    console.log(`女神${i}拒绝了`)
                }
                

                if (results.length === 20) {
                    console.log("日志记录", results)
                }
            })
        }
```

### 10-3. 异步处理的通用模型

ES官方参考了大量的异步场景，总结出了一套异步的通用模型，该模型可以覆盖几乎所有的异步场景，甚至是同步场景。

值得注意的是，为了兼容旧系统，ES6 并不打算抛弃掉过去的做法，只是基于该模型推出一个全新的 API，使用该API，会让异步处理更加的简洁优雅。

理解该 API，最重要的，是理解它的异步模型

1. ES6 将某一件可能发生异步操作的事情，分为两个阶段：**unsettled** 和 **settled**

![](assets/2019-10-18-17-28-30.png)

- unsettled： 未决阶段，表示事情还在进行前期的处理，并没有发生通向结果的那件事
- settled：已决阶段，事情已经有了一个结果，不管这个结果是好是坏，整件事情无法逆转

事情总是从 未决阶段 逐步发展到 已决阶段的。并且，未决阶段拥有控制何时通向已决阶段的能力。

2. ES6将事情划分为三种状态： pending、resolved、rejected

- pending: 挂起，处于未决阶段，则表示这件事情还在挂起（最终的结果还没出来）
- resolved：已处理，已决阶段的一种状态，表示整件事情已经出现结果，并是一个可以按照正常逻辑进行下去的结果
- rejected：已拒绝，已决阶段的一种状态，表示整件事情已经出现结果，并是一个无法按照正常逻辑进行下去的结果，通常用于表示有一个错误

既然未决阶段有权力决定事情的走向，因此，未决阶段可以决定事情最终的状态！

我们将 把事情变为resolved状态的过程叫做：**resolve**，推向该状态时，可能会传递一些数据

我们将 把事情变为rejected状态的过程叫做：**reject**，推向该状态时，同样可能会传递一些数据，通常为错误信息

**始终记住，无论是阶段，还是状态，是不可逆的！**

![](assets/2019-10-18-18-10-18.png)

3. 当事情达到已决阶段后，通常需要进行后续处理，不同的已决状态，决定了不同的后续处理。

- resolved状态：这是一个正常的已决状态，后续处理表示为 thenable
- rejected状态：这是一个非正常的已决状态，后续处理表示为 catchable

后续处理可能有多个，因此会形成作业队列，这些后续处理会按照顺序，当状态到达后依次执行

![](assets/2019-10-18-18-10-38.png)

4. 整件事称之为Promise

![](assets/2019-10-18-18-15-52.png)

**理解上面的概念，对学习Promise至关重要！**

### 10-4Promise的基本使用

```js
const pro = new Promise((resolve, reject)=>{
    // 未决阶段的处理
    // 通过调用resolve函数将Promise推向已决阶段的resolved状态
    // 通过调用reject函数将Promise推向已决阶段的rejected状态
    // resolve和reject均可以传递最多一个参数，表示推向状态的数据
})

pro.then(data=>{
    //这是thenable函数，如果当前的Promise已经是resolved状态，该函数会立即执行
    //如果当前是未决阶段，则会加入到作业队列，等待到达resolved状态后执行
    //data为状态数据
}, err=>{
    //这是catchable函数，如果当前的Promise已经是rejected状态，该函数会立即执行
    //如果当前是未决阶段，则会加入到作业队列，等待到达rejected状态后执行
    //err为状态数据
})
```

举个栗子：
```js
/*
    邓哥心中有三个女神
    有一天，邓哥决定向第一个女神表白，如果女神拒绝，则向个女神表白，直到所有的女神都拒绝，或有一个女神同意为止
    用代码模拟上面的场景
*/
function fn(god) {
    return new Promise((resolve, reject) => {
        // 在这个里面执行的都是未决状态
        console.log(`邓哥向${god}女神发出表白短信`);
        setTimeout(() => {
            if (Math.random() > 0.5) {
                // 成功把他推向已决
                resolve('女神同意啦');
            } else {
                resolve('女神拒绝啦')
            }
        }, 1000);
    })
}
// 推向已决以后，会执行then处理（它是异步执行的代码，放队列）
fn('女神1').then(result => {
    console.log(result);
})
```

在来个栗子(ajax)：

```js
function ajax(options) {
            return new Promise((resolve, reject) => {
                var url = options.url || '';
                var type = options.type.toUpperCase() || 'GET';
                var data = options.data || '';
                var success = options.success || function () { };
                var error = options.error || function () { };
                var async = typeof options.async == 'boolean' ? options.async : 'true';
                // 请求拦截
                var beforeSend = options.beforeSend || function (xhr) { return xhr };

                var xhr = null;
                if (window.XMLHttpRequest) {
                    xhr = new XMLHttpRequest();
                } else if (window.ActiveXObject) {
                    xhr = new ActiveXObject("Microsoft.XMLHttp");
                } else {
                    return alert('当前浏览器不支持XMLHTTPRequest');
                }

                // xhr.readyState 0 - 4
                // 0 建立连接之前
                // 1 建立连接，调用open方法
                // 2 open调用完了，等待发送数据
                // 3 正在发送数据
                // 4 整个请求已经完成，对方已经给予回应

                xhr.onreadystatechange = function () {
                    if (xhr.readyState === 4) {
                        if (xhr.status === 200) {
                            // success(JSON.parse(xhr.responseText));
                            resolve(JSON.parse(xhr.responseText))
                        }
                    }
                }
                // 监听到错误之后 调用error回调函数
                xhr.onerror = function (err) {
                    // error(new Error(err));
                    reject(new Error(err));
                }

                // 默认情况下不执行，如果我们传入 false就拦截，不执行下面       的代码了
                if (!beforeSend(xhr)) {
                    return false;
                }

                if (type === 'GET') {
                    xhr.open(type, url + '?' + data, async);
                    xhr.send();
                } else {
                    xhr.open(type, url, async);
                    xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
                    xhr.send(data);
                }

            })
        }


        ajax({
            url: 'http://open.duyieu.com/api/student/findAll',
            type: 'get'
        }).then(result => {
            console.log(result);
        }, err => {
            console.log(err);
        })
```

再吃个栗子：
```js
const pro = new Promise((resolve, reject) => {
            console.log("未决阶段")
            setTimeout(() => {
                if (Math.random < 0.5) {
                    resolve(123)
                } else {
                    reject(new Error("asdfasdf"));
                }
            }, 3000);
        })
        pro.then(data => {
            console.log(data);
        }, err => {
            console.log(err)
        })
```

**需要注意的细节**：

1. 未决阶段的处理函数是同步的，会立即执行
2. thenable和catchable函数是异步的，就算是立即执行，也会加入到事件队列中等待执行，并且，加入的队列是微队列
3. pro.then可以只添加thenable函数，pro.catch可以单独添加catchable函数
4. 在未决阶段的处理函数中，如果发生未捕获的错误，会将状态推向rejected，并会被catchable捕获
5. 一旦状态推向了已决阶段，无法再对状态做任何更改
6. **Promise并没有消除回调，只是让回调变得可控**

```js
const pro = new Promise((resolve, reject) => {
            console.log("a")
            resolve(1);
            setTimeout(() => {
                console.log("b")
            }, 0);
        })
        //pro: resolved
        pro.then(data => {
            console.log(data)
        })
        pro.catch(err => {
            console.log(err)
        })
        console.log("c")

/* 
首先，未决阶段是同步的，a,c。同步的执行完毕之后，推到了成功已决状态(在微队列中)，而定时器在宏队列中。所以先1后b。
*/
```

```js
const pro = new Promise((resolve, reject) => {
        // 未决阶段的错误，而且不是捕获（捕获就是try）的错误，将结果推向错误的已决
            console.logs(1); //错误，推向错误已决
            resolve('a');
        })
        pro.then(data => {
            console.log(data);
        })
        pro.catch(err => { 
            console.log(err);
        })
```

```js
// 状态一旦确定就不能更改
const pro = new Promise((resolve, reject) => {
            throw new Error("abc");  // 发生错误，状态是错误已决，所以以下更改状态的代码无效
            resolve(1); //无效
            reject(2); //无效
            resolve(3); //无效
            reject(4); //无效
        })
        pro.then(data => {
            console.log(data)
        })
        pro.catch(err => {
            console.log(err)
        })
// 状态一旦确定，后面更改状态的代码无效。
```
### 10-5. Promise的串联（推荐去看代码demo）

当后续的Promise需要用到之前的Promise的处理结果时，需要Promise的串联

Promise对象中，无论是then方法还是catch方法，它们都具有返回值，返回的是一个全新的Promise对象，它的状态满足下面的规则：

1. 如果当前的Promise是未决的，得到的新的Promise是挂起状态
2. 如果当前的Promise是已决的，会运行响应的后续处理函数，并将后续处理函数的结果（返回值）作为resolved状态数据，应用到新的Promise中；如果后续处理函数发生错误，则把返回值作为rejected状态数据，应用到新的Promise中。

**后续的Promise一定会等到前面的Promise有了后续处理结果后，才会变成已决状态**

如果前面的Promise后续处理的，返回的是一个Promise，则返回的新的Promise状态和后续处理返回的Promise状态保持一致。

### 10-6Promise的其他api
1. 原型成员(实例成员)
- then：注册一个后续处理函数，当Promise为resolved状态时运行该函数

- catch：注册一个后续处理函数，当Promise为rejected状态时运行该函数
- finally：[ES2018]注册一个后续处理函数（无参），当Promise为已决时运行该函数
2. 构造函数成员(静态成员)

- resolve(数据)：该方法返回一个resolved状态的Promise，传递的数据作为状态数据
  - 特殊情况：如果传递的数据是Promise，则直接返回传递的Promise对象
```js
const pro = new Promise((resolve, reject) => {
        resolve(1);
    })
    // 等效于：
    const pro = Promise.resolve(1);
```
```js
// 特殊情况
 const p = new Promise((resolve, reject) => {
        resolve(3);
    })
const pro = Promise.resolve(p);
//等效于
const pro = p;
console.log(pro === p) // true
```
- reject(数据)：该方法返回一个rejected状态的Promise，传递的数据作为状态数据
```js
const pro = new Promise((resolve, reject) => {
        reject(1);
    })
    // 等效于：
    const pro = Promise.reject(1);
```

- all(iterable)：这个方法返回一个新的promise对象，该promise对象在iterable参数对象里所有的promise对象都成功的时候才会触发成功，一旦有任何一个iterable里面的promise对象失败则立即触发该promise对象的失败。这个新的promise对象在触发成功状态以后，会把一个包含iterable里所有promise返回值的数组作为成功回调的返回值，顺序跟iterable的顺序保持一致；如果这个新的promise对象触发了失败状态，它会把iterable里第一个触发失败的promise对象的错误信息作为它的失败错误信息。Promise.all方法常被用于处理多个promise对象的状态集合。
```js
function getRandom(min, max) {
            return Math.floor(Math.random() * (max - min)) + min;
        }
        const proms = [];
        for (let i = 0; i < 10; i++) {
            proms.push(new Promise((resolve, reject) => {
                setTimeout(() => {
                    if (Math.random() < 0.5) {
                        console.log(i, "完成");
                        resolve(i);
                    } else {
                        console.log(i, "失败")
                        reject(i);
                    }
                }, getRandom(1000, 5000));
            }))
        }
        //等到所有的promise变成resolved状态后输出: 全部完成(要是有失败的就不会触发)
        const pro = Promise.all(proms)
        pro.then(datas => {
            console.log("全部完成", datas);
        })
        pro.catch(err => {
            console.log("有失败的", err);
        })
        console.log(proms);
```

- race(iterable)：当iterable参数里的任意一个子promise被成功或失败后，父promise马上也会用子promise的成功返回值或失败详情作为参数调用父promise绑定的相应句柄，并返回该promise对象
```js
function getRandom(min, max) {
            return Math.floor(Math.random() * (max - min)) + min;
        }
        const proms = [];
        for (let i = 0; i < 10; i++) {
            proms.push(new Promise((resolve, reject) => {
                setTimeout(() => {
                    if (Math.random() < 0.5) {
                        console.log(i, "完成");
                        resolve(i);
                    } else {
                        console.log(i, "失败")
                        reject(i);
                    }
                }, getRandom(1000, 5000));
            }))
        }
        //有人完成就触发
        const pro = Promise.race(proms)
        pro.then(data => {
            console.log("有人完成了", data);
        })
        pro.catch(err => {
            console.log("有人失败了", err);
        })
        console.log(proms);
```

总结：
```js
 /*
            邓哥心中有二十个女神，他决定用更加高效的办法
            他同时给二十个女神表白，如果有女神同意，就拒绝其他的女神
            并且，当所有的女神回复完成后，他要把所有的回复都记录到日志进行分析
            用代码模拟上面的场景
        */
        function biaobai(god) {
            return new Promise((resolve, reject) => {
                console.log(`邓哥向女神【${god}】发出了表白短信`);
                setTimeout(() => {
                    if (Math.random() < 0.05) {
                        //女神同意拉
                        console.log(god, "同意")
                        resolve(true);
                    } else {
                        console.log(god, "拒绝")
                        resolve(false);
                    }
                }, Math.floor(Math.random() * (3000 - 1000) + 1000));
            })
        }
        const proms = [];
        let hasAgree = false; //是否有女神同意

        for (let i = 1; i <= 20; i++) {//给20个女神发送短信
            const pro = biaobai(`女神${i}`).then(resp => {
                if (resp) {//收到回复的信息以后
                    if (hasAgree) {
                        console.log("发错了短信，邓哥很机智的拒绝了")
                    } else {
                        hasAgree = true;
                        console.log("邓哥很开心，终于成功了！");
                    }
                }
                return resp;
            })
            proms.push(pro);
        }

        Promise.all(proms).then(results => {
            console.log("日志记录", results);
        })
```

### 10-7[扩展]手写Promise(没两年工作经验还是不要尝试了)

```js
const MyPromise = (() => {
    const PENDING = "pending",
        RESOLVED = "resolved",
        REJECTED = "rejected",
        PromiveValue = Symbol("PromiseValue"), //状态数据
        PromiseStatus = Symbol("PromiseStatus"),
        thenables = Symbol("thenables"), //thenable
        catchables = Symbol("catchbles"), //catchables
        changeStatus = Symbol("changeStatus"),//当前状态
        settleHandle = Symbol("settleHandle"), //后续处理的通用函数
        linkPromise = Symbol("linkPromise");  //创建串联的Promise

    return class MyPromise {

        /**
         * 改变当前Promise的状态
         * @param {*} newStatus 
         * @param {*} newValue 
         * @param {*} queue 执行的作业队列
         */
        [changeStatus](newStatus, newValue, queue) {
            if (this[PromiseStatus] !== PENDING) {
                //状态无法变更
                return;
            }
            this[PromiseStatus] = newStatus;
            this[PromiveValue] = newValue;
            //执行相应队列中的函数
            queue.forEach(handler => handler(newValue));
        }

        /**
         * 
         * @param {*} executor 未决阶段（pending状态）下的处理函数
         */
        constructor(executor) {
            this[PromiseStatus] = PENDING;
            this[PromiveValue] = undefined;
            this[thenables] = []; //后续处理函数的数组 -> resolved
            this[catchables] = []; //后续处理函数的数组 -> rejected

            const resolve = data => {
                this[changeStatus](RESOLVED, data, this[thenables]);
            }

            const reject = reason => {
                this[changeStatus](REJECTED, reason, this[catchables]);
            }
            try {
                executor(resolve, reject)
            }
            catch (err) {
                reject(err);
            }
        }

        /**
         * 处理 后续处理函数
         * @param {*} handler 后续处理函数
         * @param {*} immediatelyStatus 需要立即执行的状态
         * @param {*} queue 作业队列
         */
        [settleHandle](handler, immediatelyStatus, queue) {
            if (typeof handler !== "function") {
                return;
            }
            if (this[PromiseStatus] === immediatelyStatus) {
                //直接运行
                setTimeout(() => {
                    handler(this[PromiveValue]);
                }, 0);
            }
            else {
                queue.push(handler);
            }
        }

        [linkPromise](thenalbe, catchable) {
            function exec(data, handler, resolve, reject) {
                try {
                    const result = handler(data); //得到当前Promise的处理结果
                    if (result instanceof MyPromise) {
                        result.then(d => {
                            resolve(d)
                        }, err => {
                            reject(err);
                        })
                    }
                    else {
                        resolve(result);
                    }
                }
                catch (err) {
                    reject(err);
                }
            }

            return new MyPromise((resolve, reject) => {
                this[settleHandle](data => {
                    if(typeof thenable !== 'function'){
                        resolve(data);
                        return;
                    }
                    exec(data, thenalbe, resolve, reject);
                }, RESOLVED, this[thenables])

                this[settleHandle](reason => {
                    if(typeof catchable !== 'function'){
                        resolve(reason);
                        return;
                    }
                    exec(reason, catchable, resolve, reject);
                }, REJECTED, this[catchables])
            })
        }

        then(thenable, catchable) {
            return this[linkPromise](thenable, catchable);
        }

        catch(catchable) {

            return this[linkPromise](undefined, catchable);
        }


        static all(proms) {
            return new MyPromise((resolve, reject) => {
                const results = proms.map(p => {
                    const obj = {
                        result: undefined,
                        isResolved: false
                    }
                    p.then(data => {
                        obj.result = data;
                        obj.isResolved = true;
                        //判断是否所有的全部完成
                        const unResolved = results.filter(r => !r.isResolved)
                        if (unResolved.length === 0) {
                            //全部完成
                            resolve(results.map(r => r.result));
                        }
                    }, reason => {
                        reject(reason);
                    })
                    return obj;
                })
            })
        }

        static race(proms) {
            return new MyPromise((resolve, reject) => {
                proms.forEach(p => {
                    p.then(data => {
                        resolve(data);
                    }, err => {
                        reject(err);
                    })
                })
            })
        }

        static resolve(data) {
            if (data instanceof MyPromise) {
                return data;
            }
            else {
                return new MyPromise(resolve => {
                    resolve(data);
                })
            }
        }

        static reject(reason) {
            return new MyPromise((resolve, reject) => {
                reject(reason);
            })
        }
    }
})();
```

### 10-8async和await

async 和 await 是 ES2016 新增两个关键字，它们借鉴了 ES2015 中生成器在实际开发中的应用，**目的是简化 Promise api 的使用，并非是替代 Promise。**

1. async

**目的**：是要简化在函数的返回值中对Promise的创建。

async用于修饰函数(无论是函数表达式，还是函数声明)，放置在函数最开始的位置上，被修饰的函数的返回结构一定是Promise对象。

```js
async function test(){
    console.log(1);
    return 5;
}
// 等效于
function test(){
    return new Promise((resolve,reject)=>{
        concole.log(1);
        resolve(5);
    })
}
```

2. await

- **await关键字必须出现在async函数中！！！**

await是用在某个表达式之前，如果表达式是一个Promise，则得到的是thenable中的状态数据(也就是Promise执行后的结果)。

```js
async function test() {
        console.log(1);
        return 2;
    }
    const pro = test();
    async function test1() {
        const result = await pro;
        console.log(result);
    }
    test1();

// 等效于
function test(){
    return new Promise((resolve, reject)=>{
        console.log(1);
        resolve(2);
    })
}
function test2(){
    return new Promise((resolve, reject)=>{
        test().then(data => {
            const result = data;
            console.log(result);
            resolve();
        })
    })
}
```
- **如果await的表达式不是Promise，则会将其使用Promise.resolve包装后按照规则运行**

## 十一.FetchApi(H5新增api，简化ajax)

### 11-1FetchAPI基本使用

1. 使用```fetch```即可向服务器发送网络请求。
2. 参数
    - 必填， 字符串，请求地址
    - 选填， 对象，请求配置

3. **请求配置对象**

- method: 字符串，请求方法，默认值GET
- headers：对象，请求头信息
- body: 请求体的内容，必须匹配请求头中的 Content-Type
- mode：字符串，请求模式
  - cors：默认值，配置为该值，会在请求头中加入 origin 和 referer
  - no-cors：配置为该值，不会在请求头中加入 origin 和 referer，跨域的时候可能会出现问题
  - same-origin：指示请求必须在同一个域中发生，如果请求其他域，则会报错
- credentials: 如何携带凭据（cookie）
  - omit：默认值，不携带cookie
  - same-origin：请求同源地址时携带cookie
  - include：请求任何地址都携带cookie
- cache：配置缓存模式
  - default: 表示fetch请求之前将检查下http的缓存.
  - no-store: 表示fetch请求将完全忽略http缓存的存在. 这意味着请求之前将不再检查下http的缓存, 拿到响应后, 它也不会更新http缓存.
  - no-cache: 如果存在缓存, 那么fetch将发送一个条件查询request和一个正常的request, 拿到响应后, 它会更新http缓存.
  - reload: 表示fetch请求之前将忽略http缓存的存在, 但是请求拿到响应后, 它将主动更新http缓存.
  - force-cache: 表示fetch请求不顾一切的依赖缓存, 即使缓存过期了, 它依然从缓存中读取. 除非没有任何缓存, 那么它将发送一个正常的request.
  - only-if-cached: 表示fetch请求不顾一切的依赖缓存, 即使缓存过期了, 它依然从缓存中读取. 如果没有缓存, 它将抛出网络错误(该设置只在mode为”same-origin”时有效)

4. **返回值**

**fetch 函数返回一个 Promise 对象**

- 当收到服务器的返回结果后(不管返回结果是200，还是404等错误都会推向已决成功状态)，Promise 进入resolved状态，状态数据为 Response 对象
- 当网络发生错误（或其他导致无法完成交互的错误）时，Promise 进入 rejected 状态，状态数据为错误信息

**Response对象（返回的数据）**
- ok：boolean，当响应消息码在200~299之间时为true，其他为false
- status：number，响应的状态码
- text()：用于处理文本格式的 Ajax 响应。它从响应中获取文本流，将其读完，然后返回一个被解决为 string 对象的 Promise。
- blob()：用于处理二进制文件格式（比如图片或者电子表格）的 Ajax 响应。它读取文件的原始数据，一旦读取完整个文件，就返回一个被解决为 blob 对象的 Promise。
- json()：用于处理 JSON 格式的 Ajax 的响应。它将 JSON 数据流转换为一个被解决为 JavaScript 对象的promise。
- redirect()：可以用于重定向到另一个 URL。它会创建一个新的 Promise，以解决来自重定向的 URL 的响应。

举个栗子：
```html
<button>点击发送网络请求</button>
```

```js
    async function getStudent() {
        const url = 'http://open.duyiedu.com/api/student/findAll?appkey=chenjie_1606788134251';
        const pro = await fetch(url); //返回的是promise对象
        const result = await pro.json(); //数据返回的也是promise对象

        console.log(result);
    }
    document.getElementsByTagName('button')[0].onclick = ()=> {
        getStudent();
    }
```

### 11-2Request对象


>除了使用基本的fetch方法，还可以通过创建一个Request对象来完成请求（实际上，fetch的内部会帮你创建一个Request对象）

```js
new Request(url地址, 配置)
```

注意点：

尽量保证每次请求都是一个新的Request对象

```js
let req;

function getRequestInfo() {
    if (!req) {
        const url = "http://101.132.72.36:5100/api/local";
        req = new Request(url, {});
        console.log(req);
    }
    return req.clone(); //克隆一个全新的request对象，配置一致
}

async function getProvinces() {
    const resp = await fetch(getRequestInfo())
    const result = await resp.json();
    console.log(result)
}

document.querySelector("button").onclick = function () {
    getProvinces();
}
```

### 11-3Response对象

```js
let req;

function getRequestInfo() {
    if (!req) {
        const url = "http://101.132.72.36:5100/api/local";
        req = new Request(url, {});
        console.log(req);
    }
    return req.clone(); //克隆一个全新的request对象，配置一致
}

async function getProvinces() {
    // const resp = await fetch(getRequestInfo())
    //模拟一个返回的数据对象
    const resp = new Response(`[
        {"id":1, "name":"北京"},
        {"id":2, "name":"天津"}
    ]`, {
        ok: true,
        status: 200
    })
    const result = await getJSON(resp);
    console.log(result)
}

async function getJSON(resp) {
    const json = await resp.json();
    return json;
}

document.querySelector("button").onclick = function () {
    getProvinces();
}
```

### 11-4Headers

在Request和Response对象内部，会将传递的请求头对象，转换为Headers

Headers对象中的方法：

- has(key)：检查请求头中是否存在指定的key值
- get(key): 得到请求头中对应的key值
- set(key, value)：修改对应的键值对
- append(key, value)：添加对应的键值对
- keys(): 得到所有的请求头键的集合
- values(): 得到所有的请求头中的值的集合
- entries(): 得到所有请求头中的键值对的集合

### 11-5文件上传

流程：

1. 客户端将文件数据发送给服务器
2. 服务器保存上传的文件数据到服务器端
3. 服务器响应给客户端一个文件访问地址

> 测试地址：http://101.132.72.36:5100/api/upload
> 键的名称（表单域名称）：imagefile

请求方法：POST
请求的表单格式：multipart/form-data
请求体中必须包含一个键值对，键的名称是服务器要求的名称，值是文件数据

> HTML5中，JS仍然无法随意的获取文件数据，但是可以获取到input元素中，被用户选中的文件数据
> 可以利用HTML5提供的FormData构造函数来创建请求体


## 十二.迭代器和生成器

### 12-1迭代器

1. 背景知识

- 什么是迭代器？
从一个数据集合中按照一定的顺序，不断取出数据的过程。

- 迭代和遍历的区别？
迭代强调的是依次取数据，并不保证取多少，也不保证把所有的数据取完。

遍历强调的是要把整个数据依次全部取出。

- 迭代器    
对迭代过程的封装，在不同的语言中有不同的表现形式，通常为对象。

- 迭代模式
一种设计模式，用于统一迭代过程，并规范了迭代器规格：

    - 迭代器应该具有得到下一个数据的能力
    - 迭代器应该具有判断是否还有后续数据的能力

2. JS中的迭代器

JS规定，如果一个对象具有next方法，并且该方法返回一个对象，该对象的格式如下：

```js
{value: 值, done: 是否迭代完成}
```
则认为该对象是一个迭代器。

含义：

- next方法：用于得到下一个数据
- 返回的对象
  - value：下一个数据的值
  - done：boolean，是否迭代完成

举个栗子：
```js
// 迭代数组arr
const arr = [1, 2, 3, 4, 5];
function iterator() {
    return {
        i: 0,
        next() {
            let obj = {
                value: arr[this.i],
                done: this.i >= arr.length
            }
            this.i++;
            return obj;
        }
    }
}
let fn = iterator();
// 让迭代器不断的取出下一个数据，直到没有数据为止
let data = fn.next();
while (!data.done) {
    console.log(data.value);
    data = fn.next();
}
```

再吃一个栗子：
```js
const arr1 = [1, 2, 3, 4, 5];
const arr2 = [6, 7, 8, 9];

// 迭代器创建函数  iterator creator
function createIterator(arr) {
    let i = 0;//当前的数组下标
    return {
        next() {
            var result = {
                value: arr[i],
                done: i >= arr.length
            }
            i++;
            return result;
        }
    }
}

const iter1 = createIterator(arr1);
const iter2 = createIterator(arr2);
```

最后一个栗子啦：

```js
// 依次得到斐波拉契数列前面n位的值
// 1 1 2 3 5 8 13 .....
function createFeiboIteractor() {
    let prev1 = 1,  //当前数字的前一位
        prev2 = 1,  //当前数字的前两位
        n = 1;
    return {
        next() {
            let value;
            if (n >= 2) {
                value = prev1 + prev2;
            } else {
                value = 1;
            }
            const result = {
                value,
                done: false
            }
            // 每次调用完需要更改数据
            prev2 = prev1;
            prev1 = result.value;
            n++;
            return result;
        }
    }
}
const fn = createFeiboIteractor();
```

### 12-2可迭代协议与for...of...循环

1. 可迭代协议

**概念回顾**

- 迭代器(iterator)：一个具有next方法的对象，next方法返回下一个数据并且能指示是否迭代完成
- 迭代器创建函数（iterator creator）：一个返回迭代器的函数

**可迭代协议**

ES6规定，如果一个对象具有知名符号属性```Symbol.iterator```，并且属性值是一个迭代器创建函数，则该对象是可迭代的（iterable）

> 思考：如何知晓一个对象是否是可迭代的？
> 思考：如何遍历一个可迭代对象？
```js
// obj可迭代对象
const obj = {
    a: 1,
    b: 2,
    [Symbol.iterator]() {
        const keys = Object.keys(this);
        let i = 0;
        return {
            next: () => {
                const uname = keys[i];
                const value = this[uname];
                const result = {
                    value: { uname, value },
                    done: i >= keys.length
                }
                i++;
                return result;
            }
        }
    }
}

for (const item of obj) {
    console.log(item); //{uname: "a", value: 1} {uname: "b", value: 2}
}
```

2. for...of循环

for-of 循环用于遍历可迭代对象，格式如下

```js
//迭代完成后循环结束
for(const item in iterable){
    //iterable：可迭代对象
    //item：每次迭代得到的数据
}
```
```js
const arr = [5, 7, 2, 3, 6];

//以前的循环迭代
const iterator = arr[Symbol.iterator]();
let result = iterator.next();
while (!result.done) {
    const item = result.value; //取出数据
    console.log(item);
    //下一次迭代
    result = iterator.next();
}
// for循环
for (const item of arr) {
    console.log(item)
}
```

3. [扩展]展开运算符与可迭代对象

>展开运算符可以作用于可迭代对象，这样，就可以轻松的将可迭代对象转换成数组。

```js
var obj = {
    a: 1,
    b: 2,
    [Symbol.iterator]() {
        const keys = Object.keys(this);
        let i = 0;
        return {
            next: () => {
                const propName = keys[i];
                const propValue = this[propName];
                const result = {
                    value: {
                        propName,
                        propValue
                    },
                    done: i >= keys.length
                }
                i++;
                return result;
            }
        }
    }
}

var arr = [...obj];
console.log(arr);
```

### 12-3生成器

1. 什么是生成器？
生成器是一个通过构造函数Generator创建的对象，生成器既是一个迭代器，同时又是一个可迭代对象

2. 如何创建生成器？
生成器的创建，必须使用生成器函数（Generator Function）

3. 如何书写一个生成器函数呢？
```js
//这是一个生成器函数，该函数一定返回一个生成器
function* method(){

}
```

```js
function* test() {
    console.log(1);
    yield 1;
    console.log(2);
    yield 2;
    console.log(3);
    yield 3;
    console.log(4);
}
// 生成器函数最后产生的是一个生成器，并不会执行函数里面的代码
const generator = test();  
// 既然是生成器，也是迭代器，可迭代对象，要想使用需要调用next方法
generator.next(); //他会执行到yield处，把他的值作为value(返回值是一个生成器)
```
```js
//创建一个斐波拉契数列的迭代器
function* createFeiboIterator() {
    let prev1 = 1,
        prev2 = 1, //当前位置的前1位和前2位
        n = 1; //当前是第几位
    while (true) {
        if (n <= 2) {
            yield 1;
        } else {
            const newValue = prev1 + prev2
            yield newValue;
            prev2 = prev1;
            prev1 = newValue;
        }
        n++;
    }
}

const iterator = createFeiboIterator();
```


4. 生成器函数内部是如何执行的？
生成器函数内部是为了给生成器的每次迭代提供的数据

每次调用生成器的next方法，将导致生成器函数运行到下一个yield关键字位置

yield是一个关键字，该关键字只能在生成器函数内部使用，表达“产生”一个迭代数据。

5. 有哪些需要注意的细节？

1). 生成器函数可以有返回值，返回值出现在第一次done为true时的value属性中
```js
function* test() {
    console.log("第1次运行")
    yield 1;
    console.log("第2次运行")
    yield 2;
    console.log("第3次运行");
    return 10;
}

const generator = test();
```
2). 调用生成器的next方法时，可以传递参数，传递的参数会交给yield表达式的返回值
```js
function* test(){
    let info = yield 1;
    console.log(info);
    info = yield 2+info;
    console.log(info);
}
const generator = test();
// generator.next();
// 第一次执行，执行到yield 1结束，还没有赋值
// generator.next(6);
// 第二次执行，将参数6作为yield 1的数值 打印info==6
// generator.next();
// 第三次执行，undefined
```
3). 第一次调用next方法时，传参没有任何意义

4). 在生成器函数内部，可以调用其他生成器函数，但是要注意加上*号
```js
function* t1(){
        yield "a"
        yield "b"
    }

    function* test() {
        yield* t1();
        yield 1;
        yield 2;
        yield 3;
    }

    const generator = test()
```

6. 生成器的其他API

- return方法：调用该方法，可以提前结束生成器函数，从而提前让整个迭代过程结束
- throw方法：调用该方法，可以在生成器中产生一个错误

## 十三.更多的集合类型

### 13-1set集合
> 一直以来，JS只能使用数组和对象来保存多个数据，缺乏像其他语言那样拥有丰富的集合类型。因此，ES6新增了两种集合类型（set 和 map），用于在不同的场景中发挥作用。

**set用于存放不重复的数据**

1. 创建set集合
```js
new Set()  //创建一个空的set集合
new Set(iterable)  //创建一个具有初始内容的set集合，内容来自于可迭代对象每一次迭代的结果
```

```js
const a = new Set();
console.log(a);

const b = new Set([1, 2, 3]);
console.log(b);

// 会调用new String(),new String构造出来的对象是可以迭代的
const c = new Set('abcdefg');
console.log(c);
```

2. 对set集合进行后续操作

- add(数据)：添加一个数据到set集合末尾，如果数据已存在，则不进行任何操作。
    - set使用Object.is方法判断两个数据是否相同，但是针对+0和-0，set认为是相等的。
- has(数据)：判断set中是否存在对应的数据。
- delete(数据)： 删除匹配的数据，返回删除成功。
- clear(): 清空整个set集合。
- size: 获取set集合中元素的数量，只读属性，无法重新赋值。 

```js
const a = new Set();
        a.add(1);
        a.add(2);
        a.add(3);
        a.add(1);//无效的，前面有1了
        console.log(a);
```
```js
const a = new Set();
        a.add(1);
        a.add(2);
        a.add(3);
        a.add(1);//无效的，前面有1了
      
        console.log(a);
        console.log(a.has(2)); //true
```
```js
const a = new Set();
        a.add(1);
        a.add(2);
        a.add(3);
        a.add(1);//无效的，前面有1了
      
        console.log(a);
        console.log(a.has(2)); //true
        console.log(a.delete(3)); // true
        console.log(a.has(3));  //false
```

```js
const a = new Set();
        a.add(1);
        a.add(2);
        a.add(3);
        a.add(1);//无效的，前面有1了
      
        a.clear();
        console.log(a); //空的
```

```js
const a = new Set();
        a.add(1);
        a.add(2);
        a.add(3);
        a.add(1);//无效的，前面有1了
      
        console.log(a);
        console.log(a.has(2)); //true
        console.log(a.delete(3)); // true
        console.log(a.has(3));  //false
        console.log(a.size) // 数量
```

3. 与数组进行转换
>set集合返回的是一个对象
```js
const a = new Set([1, 2, 3, 4, 5, 6]);
        // set本身也是一个可迭代的对象，每次迭代的结果就是每一项的值
        const arr = [...a];
        console.log(arr); //[1, 2, 3, 4, 5, 6]
```


字符串：
```js
 const str = "asf23sdfgsdgfsafasdfasfasfasfsafsagfdsfg";
        const s = [...new Set(str)].join('');
        console.log(s);//asf23dg
```

4. 如何遍历

1). 使用for-of循环
2). 使用set中的实例方法forEach

注意：set集合中不存在下标，因此forEach中的回调的第二个参数和第一个参数是一致的，均表示set中的每一项


### 13-2set练习题

```js
// 两个数组的并集、交集、差集 （不能出现重复项），得到的结果是一个新数组
const arr1 = [33, 22, 55, 33, 11, 33, 5];
const arr2 = [22, 55, 77, 88, 88, 99, 99];

//并集
// const result = [...new Set(arr1.concat(arr2))];
console.log("并集", [...new Set([...arr1, ...arr2])]);

const cross = [...new Set(arr1)].filter(item => arr2.indexOf(item) >= 0);
//交集
console.log("交集", cross)

//差集
// console.log("差集", [...new Set([...arr1, ...arr2])].filter(item => arr1.indexOf(item) >= 0 && arr2.indexOf(item) < 0 || arr2.indexOf(item) >= 0 && arr1.indexOf(item) < 0))
```

### 13-3map集合

键值对（key value pair）数据集合的特点：键不可重复

map集合专门用于存储多个键值对数据。

在map出现之前，我们使用的是对象的方式来存储键值对，键是属性名，值是属性值。

使用对象存储有以下问题：

1. 键名只能是字符串
2. 获取数据的数量不方便
3. 键名容易跟原型上的名称冲突


1. 如何创建map

```js
new Map(); //创建一个空的map
new Map(iterable); //创建一个具有初始内容的map，初始内容来自于可迭代对象每一次迭代的结果，但是，它要求每一次迭代的结果必须是一个长度为2的数组，数组第一项表示键，数组的第二项表示值
```

2. 如何进行后续操作

- size：只读属性，获取当前map中键的数量
- set(键, 值)：设置一个键值对，键和值可以是任何类型
  - 如果键不存在，则添加一项
  - 如果键已存在，则修改它的值
  - 比较键的方式和set相同
- get(键): 根据一个键得到对应的值
- has(键)：判断某个键是否存在
- delete(键)：删除指定的键
- clear(): 清空map


3. 和数组互相转换

和set一样

4. 遍历

- for-of，每次迭代得到的是一个长度为2的数组
- forEach，通过回调函数遍历
  - 参数1：每一项的值
  - 参数2：每一项的键
  - 参数3：map本身

## 十四.代理与反射

### 14-1[回顾]属性描述符

1. 属性描述符
```js
// 通过```Object.getOwnPropertyDescriptor(对象, 属性名)```可以得到一个对象的某个属性的属性描述符
const obj = {
    a: 123,
    c: 456
}
const sx = Object.getOwnPropertyDescriptor(obj, 'a');
console.log(sx);
/* 
configurable: true  代表的是这个属性描述符里面的属性是否可以更改
enumerable: true 代表的是对象里面的属性是否可以枚举
value: 123 代表的是对象属性的值
writable: true 代表的是对象属性是否可以修改
*/

Object.defineProperty(obj, 'd', {
    value:10,
    configurable:false,
    enumerable:false,
    writable:false
})
obj.d = 30;
console.log(obj.d);  //10

for (const prop in obj) {  //属性d没有被枚举出来
    const element = obj[prop];
    console.log(element);
}
```

> ```Object.getOwnPropertyDescriptors(对象)```可以得到某个对象的所有属性描述符

如果需要为某个对象添加属性时 或 修改属性时， 配置其属性描述符，可以使用下面的代码:

```js
Object.defineProperty(对象, 属性名, 描述符);
Object.defineProperties(对象, 多个属性的描述符)
```

2. 存储器属性

>属性描述符中，如果配置了 get 和 set 中的任何一个，则该属性，不再是一个普通属性，而变成了存取器属性。

>get 和 set配置均为函数，如果一个属性是存取器属性，则读取该属性时，会运行get方法，将get方法得到的返回值作为属性值；如果给该属性赋值，则会运行set方法。

>存取器属性最大的意义，在于可以控制属性的读取和赋值。

```js
const obj = {
    a: 132
}
Object.defineProperty(obj, 'b', {
    get() {
        console.log('运行get方法');
        return obj._b;
    },
    set(val) {
        console.log('运行set方法', val);
        obj._b = val;
    }
})

// 该属性不是普通的属性，如果要给该属性赋值，会调用set方法，要是获取该属性的话，调用get方法,取到的是get的返回值
// obj.b = 10; //运行get方法
// console.log(obj.b); //运行set方法

obj.b = obj.b + 1;  //set(obj._b + 1)  set(get() + 1)
console.log(obj.b);  
```

### 14-2Reflect

1. Reflect是什么？

Reflect是一个内置的JS对象，它提供了一系列方法，可以让开发者通过调用这些方法，访问一些JS底层功能

由于它类似于其他语言的**反射**，因此取名为Reflect

2. 它可以做什么？

使用Reflect可以实现诸如 属性的赋值与取值、调用普通函数、调用构造函数、判断属性是否存在与对象中  等等功能

3. 这些功能不是已经存在了吗？为什么还需要用Reflect实现一次？

有一个重要的理念，在ES5就被提出：减少魔法、让代码更加纯粹

这种理念很大程度上是受到函数式编程的影响

ES6进一步贯彻了这种理念，它认为，对属性内存的控制、原型链的修改、函数的调用等等，这些都属于底层实现，属于一种魔法，因此，需要将它们提取出来，形成一个正常的API，并高度聚合到某个对象中，于是，就造就了Reflect对象

因此，你可以看到Reflect对象中有很多的API都可以使用过去的某种语法或其他API实现。

4. 它里面到底提供了哪些API呢？

- Reflect.set(target, propertyKey, value): 设置对象target的属性propertyKey的值为value，等同于给对象的属性赋值
- Reflect.get(target, propertyKey): 读取对象target的属性propertyKey，等同于读取对象的属性值
- Reflect.apply(target, thisArgument, argumentsList)：调用一个指定的函数，并绑定this和参数列表。等同于函数调用
- Reflect.deleteProperty(target, propertyKey)：删除一个对象的属性
- Reflect.defineProperty(target, propertyKey, attributes)：类似于Object.defineProperty，不同的是如果配置出现问题，返回false而不是报错
- Reflect.construct(target, argumentsList)：用构造函数的方式创建一个对象
- Reflect.has(target, propertyKey): 判断一个对象是否拥有一个属性
- 其他API：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

### 14-3Proxy 代理

代理：提供了修改底层实现的方式

```js

//代理一个目标对象
//target：目标对象
//handler：是一个普通对象，其中可以重写底层实现
//返回一个代理对象
new Proxy(target, handler)
```

## 十五.增强的数组功能

### 15-1新增的数组API

1. 静态方法：(就是函数对象身上的方法)

    - Array.of(...args)：使用指定的数组项创建一个新的数组。
    ```js
    const arr = Array.of(1, 2, 3);
    console.log(arr);//[1,2,3]

    // 我们以前也可以使用new Array()创建数组，但是里面要是只有一个参数的话，代表的是数组的长度
    ```

    - Array.from(arg)：通过给定的类数组，或者是可迭代对象，创建一个新的数组(可以将伪数组转化成数组)

    ```html
        <div></div>
        <div></div>
    ```
    ```js
        const divs = document.querySelectorAll('div');
        const arr = Array.from('abcd');
        const arr2 = Array.from(divs);
        console.log(arr); //["a", "b", "c", "d"]
        console.log(arr2); //[div, div]
    ```

2. 实例方法：(就是一些写在原型链上的方法)

    - find(callback):用于查找满足条件的第一个元素
    ```js
    // 我想要查找到id= 5的数组里面的元素
        const result = arr.find(item => item.id == 5);
        console.log(result);
    ```
    - findIndex(callback):用于查找满足条件的第一个元素的下标
    ```js
        const resultIndex = arr.findIndex(item=>item.id == 5);
        console.log(resultIndex); //4
    ```
    - fill(data):用指定的数据填充满数组的内容
    ```js
    //创建一个长度为100的数组，里面值是1
        const arr = new Array(100);
        arr.fill(1);
        console.log(arr);//[1,1,1,1,......1]
    ```
    - copyWithin(target, start?, end?): 在数组内部完成复制
    ```js
        const arr = [1, 2, 3, 4, 5, 6];
        // 从下标2开始改变原数组的数据，数据来源于下标0开始
        // arr.copyWithin(2)//[1,2,1,2,3,4]
        // 从下标2开始，改变原数组的数据，数据来源于下标1的位置
        // arr.copyWithin(2, 1)//[1,2,2,3,4,5]
        // 从下标2开始，改变原数组数据，数据来源于下标1，截止到下标3前
        arr.copyWithin(2, 1, 3) //[1,2,2,3,5,6]
        console.log(arr);
    ```
    - includes(data)：判断数组中是否包含某个值，使用Object.is匹配
    ```js
        const arr = [45, 21, 356, 66 , 6, NaN, 723, 54];
        // 以前的indexOf()只能判断数组中是否有普通的数值，并返回的是索引号
        console.log(arr.indexOf(66) >= 0)
        console.log(arr.includes(NaN));
    ```
    ```js
    const arr = [{
                name: "a",
                id: 1
            },
            {
                name: "b",
                id: 2
            },
            {
                name: "c",
                id: 3
            },
            {
                name: "d",
                id: 4
            },
            {
                name: "e",
                id: 5
            },
            {
                name: "f",
                id: 6
            },
            {
                name: "g",
                id: 7
            }
            ]
    ```

### 15-2[扩展]类型化数组

1. 计算机必须使用固定的位数来存储数字，无论存储的数字是大是小，在内存中占用的空间是固定的。

2. n位的无符号整数能表示的数字是2^n个，取值范围是：0 ~ 2^n - 1

3. n位的有符号整数能表示的数字是2^n个，取值范围是：-2^(n-1) ~ 2^(n-1) - 1

4. 浮点数表示法可以用于表示整数和小数，目前分为两种标准：
   1. 32位浮点数：又称为单精度浮点数，它用1位表示符号，8位表示阶码，23位表示尾数
   2. 64位浮点数：又称为双精度浮点数，它用1位表示符号，11位表示阶码，52位表示尾数

5. JS中的所有数字，均使用双精度浮点数保存


类型化数组：用于优化多个数字的存储

具体分为：

- Int8Array： 8位有符号整数（-128 ~ 127）
- Uint8Array： 8位无符号整数（0 ~ 255）
- Int16Array: ...
- Uint16Array: ...
- Int32Array: ...
- Uint32Array: ...
- Float32Array:
- Float64Array

1. 如何创建数组

```js

new 数组构造函数(长度)

数组构造函数.of(元素...)

数组构造函数.from(可迭代对象)

new 数组构造函数(其他类型化数组)

```


2. 得到长度

```js
数组.length   //得到元素数量
数组.byteLength //得到占用的字节数
```

3. 其他的用法跟普通数组一致，但是：

- 不能增加和删除数据，类型化数组的长度固定
- 一些返回数组的方法，返回的数组是同类型化的新数组

### [扩展]15-3ArrayBuffer

1. ArrayBuffer

ArrayBuffer：一个对象，用于存储一块固定内存大小的数据。

```js

new ArrayBuffer(字节数)

```

可以通过属性```byteLength```得到字节数，可以通过方法```slice```得到新的ArrayBuffer

2. 读写ArrayBuffer

-  使用DataView

通常会在需要混用多种存储格式时使用DataView

-  使用类型化数组

实际上，每一个类型化数组都对应一个ArrayBuffer，如果没有手动指定ArrayBuffer，类型化数组创建时，会新建一个ArrayBuffer

