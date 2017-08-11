# Javascript 面试题
#javascript/面试

## const
`const a = []`  a 能改变么，能为什么？

在ES6中另外一个定义变量的关键字是const, 但是它经常被误会为一个“常量”。在ES6中，const代表一个值的常量索引（事实上绝大多数语言都提供这样的功能）。换句话说，变量名字在内存中的指针不能够改变，但是指向这个变量的值可能改变。

## Object.assign
Object.assign 针对深度拷贝，需要使用其他方法，因为 Object.assign() 拷贝的是属性值。假如源对象的属性值是一个指向对象的引用，它也只拷贝那个引用值。 所以深拷贝 用Object 

## let 和 var 的区别

1. let 声明不会提升
2. 块级作用域
3. let 不能重复声明

## Object.create() 和 new 的区别
1. new Computer() 将调用构造函数 Computer() { } 一次，而Object.create(Computer.prototype)不会。

MDN：Object.create() 方法会使用指定的原型对象及其属性去创建一个新的对象。

2. 有时您无法使用NEW创建对象，但仍然可以调用CREATE方法。例如：如果要定义自定义元素，则必须从HTMLElement派生。

```js
proto = new HTMLElement  //fail :(
proto = Object.create( HTMLElement.prototype )  //OK :)
document.registerElement( "custom-element", { prototype: proto } )
```

## bind 和 return function 的区别

它们之间的区别是  bind 会改变 this 指向

## 用 apply call 实现 bind

```js
// 第一版 不带参数
Function.prototype.bind2 = function (context) {
    var self = this;
    return function () {
        self.apply(context);
    }
}

// 第二版 带参数
Function.prototype.bind2 = function (context) {
    var self = this;
    // 获取bind2函数从第二个参数到最后一个参数
    var args = Array.prototype.slice.call(arguments, 1);

    return function () {
        // 这个时候的arguments是指bind返回的函数传入的参数
        var bindArgs = Array.prototype.slice.call(arguments);
        self.apply(context, args.concat(bindArgs));
    }

}
```


实现 call

```js
// 第一版
Function.prototype.call2 = function(context) {
    // 首先要获取调用call的函数，用this可以获取
    context.fn = this;
	  // 利用 js 中 this 会指向调用它的对象，改变指向
    context.fn(); 
    delete context.fn;
}

// 测试一下
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

bar.call2(foo); // 1

// 第二版 使用 eval 直接执行
Function.prototype.call2 = function(context) {
    context.fn = this;
    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
    eval('context.fn(' + args +')'); // 因为浏览器安全问题，改成 join
    delete context.fn;
}

```


## 基于 es5 的继承
```js
function superClass(name) {
	this.name = name
}

superClass.prototype.getSuperName = function() {
	return this.name
}

function childClass(name) {
	superClass.call(this, name)
}

childClass.prototype = new superClass()
childClass.construct = superClass
```

## 数组二维变一维

1. for 循环
2. es6 扩展
```js
const multiDimensionalArray = [ [, ], [, ], [, ] ];  
const flattenedArray = [].concat(...multiDimensionalArray);  
console.log(flattenedArray) // [1, 2, 3, 4, 5, 6]  

// 不用es6，如果只对二维处理
Array.prototype.concat.apply([], multiDimensionalArray)
```

## 数组去重

```js
let a = [1, 2 ,3, 4,4,4, 5]
let uniqueArray = a.filter((item, pos) => {
    return a.indexOf(item) === pos;
})
console.log(uniqueArray) // [ 1, 2, 3, 4, 5 ]​​​​​
```

## Throttle & Debounce [dɪ'baʊns]

Debounce 在一段时间没有输入（或者其他操作）， 就执行一次函数；如果期间有输入，就会继续等待一段时间，实在没有了就去执行函数，一般用于input输入。
Throttle 不做等待，直接在一段时间后执行函数，一般用于滚动操作 scroll

_.throttle 使用示例
```js
function log( event ) {
  console.log( $(window).scrollTop(), event.timeStamp );
};

// 控制台记录窗口滚动事件，触发频率比你想象的要快
$(window).scroll( log );

// 控制台记录窗口滚动事件，每250ms最多触发一次
$(window).scroll( _.throttle( log, 250 ) );
```
￼
_.debounce 使用示例
```js
function ajax_lookup( event ) {
  // 对输入的内容$(this).val()执行 Ajax 查询
};

// 字符输入的频率比你预想的要快，Ajax 请求来不及回复。
$('input:text').keyup( ajax_lookup );

// 当用户停顿250毫秒以后才开始查找
$('input:text').keyup( _.debounce( ajax_lookup, 250 ) );
```

## import 和 require 的区别
如果是第三方，import 其实都是编译成 require
1. require会将脚本执行，生成对象存储到内存中。以后需要用到这个模块的时候，就会到缓存取值。即使再次执行require命令，也不会再次执行该模块。
2. import 则是引用该模块，类似把脚本文件放入到堆，脚本 export 的变化
3. 其他都是语法区别

参考 [JavaScript 模块的循环加载 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2015/11/circular-dependency.html)


## 单页应用除了hash模式还有什么可以实现
答案是 html5 的 history api，可以只改变 url，不改变内容
```js
window.history.pushState(null, null, "/profile/");
```

拓展延伸，曾经有个叫pjax的东西，利用ajax和history api达到局部刷新的效果。不过是利用 innerHTML 做的替换，会有点粗暴。

## 词法作用域和动态作用域的区别
词法作用域的函数中遇到既不是形参也不是函数内部定义的局部变量的变量时，去函数定义时的环境中查询。

动态域的函数中遇到既不是形参也不是函数内部定义的局部变量的变量时，到函数调用时的环境中查。

## Web安全

### SQL 注入

主要是 利用  sql  的规则进行攻击，例如先在 url 末尾添加 or true 测试该网站是否有相应的反 sql 注入 对策。如果没有，后续可以根据改变 sql 语句慢慢找出漏洞

### XSS (代码注入)

Cross-site scripting

例如 在表单里填写 <script > 标签，关键是过滤所有的‘<’和‘>’字符

### CSRF（跨站请求伪造）

CSRF（Cross-site request forgery），中文名称：跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。

csrf当然不能这么测，csrf问题的根源在于，浏览器可以在一个域A中的页面，用伪造的数据向域B发起请求，这时候，如果用户在B域有cookie的话，就会被带上。于是，便成功伪造了用户的请求。解决方法就是加动态变化的token，因为对方伪造的请求虽然可以带上cookie，但是在A域永远无法获取B的页面，于是在页面上种的token就没法被伪造，这时候在服务端验证一下token和cookie是否匹配就能判断是否伪造的请求。题目描述中的测试方法，连token都抓到了再重放，当然不行，因为跨域是没法获得token的。

防御方式
1. 验证码
2. 增加动态 token 作为表单提交项

#### 攻击方式 — GET请求

```html
<!-- 示例，通过 img src 伪造请求 -->
<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>
```

#### 攻击方式 - 模拟表单请求

```html
<html>
　　<head>
　　　　<script type="text/javascript">
　　　　　　function steal()
　　　　　　{
          　　　　 iframe = document.frames["steal"];
　　     　　      iframe.document.Submit("transfer");
　　　　　　}
　　　　</script>
　　</head>

　　<body onload="steal()">
　　　　<iframe name="steal" display="none">
　　　　　　<form method="POST" name="transfer"　action="http://www.myBank.com/Transfer.php">
　　　　　　　　<input type="hidden" name="toBankId" value="11">
　　　　　　　　<input type="hidden" name="money" value="1000">
　　　　　　</form>
　　　　</iframe>
　　</body>
</html>
```

### 控制台注入

Firefox 打开控制台会有提示你不要复制任何不信任的代码


## 浏览器中输入URL后经历了哪些过程
1. 域名通过 DNS 查找到相应 IP
2. 进行 HTTP 请求(缓存阶段)
3. 服务器处理请求并返回 HTTP 报文
4. 浏览器解析  渲染(DOM Tree & Style Rules)

## 缓存

### 本地缓存阶段

先在本地查找该资源，如果有发现该资源，而且该资源还没有过期，就使用这一个资源，完全不会发送http请求到服务器

相关头设置 
* cache-control http1.1新增， max-age 是距离请求发起的时间的秒数
* expire 可以遗忘了，cache-control 优先级高于 expire。expire不够灵活，只能每次设置固定时间，例如，Mon, 22 Jul 2002 11:12:01 GMT
* Pragma 遗留物，忘记吧

### 协商缓存阶段

如果在本地缓存找到对应的资源，但是不知道该资源是否过期或者已经过期，则发一个http请求到服务器，然后服务器判断这个请求，如果请求的资源在服务器上没有改动过，则返回304，让浏览器使用本地找到的那个资源

相关头设置
* last-modified 根据文件修改时间。Last-Modified 存在一定问题，如果在服务器上，一个资源被修改了，但其实际内容根本没发生改变，会因为Last-Modified时间匹配不上而返回了整个实体给客户端（即使客户端缓存里有个一模一样的资源）。
	* If-Modified-Since
* etag 自定义校验令牌，一般是文件 md5。如果是 cdn，每个服务的文件算法需要保持一致，才能保证cdn缓存
	*  If-None-Match: ETag-value ， 这个字段表明是否有相应etag，则304，如果

### Ctrl+F5

那么Ctrl+F5呢？ Ctrl+F5要的是彻底的从Server拿一份新的资源过来，不会有If-Modified-Since/If-None-Match，就是不会有etag和last-modified 校验。

### 缓存失败阶段

当服务器发现请求的资源已经修改过，或者这是一个新的请求(在本来没有找到资源)，服务器则返回该资源的数据，并且返回200， 当然这个是指找到资源的情况下，如果服务器上没有这个资源，则返回404

## CSS 重绘 和 重排
重排和重绘的区别是，重排必然会导致重绘，同时会计算 dom 上下文的 位置。重绘则不会，它只是改变了可见位置（Render Treee，类似上面有一层遮罩在移动），dom 树是没有变化的

`position`  会脱离文档流，也就是 dom 树，在脱离的那一刻，会重排一次，所以没有 transform 性能那么好

1. 问题一：一个 position absolute 里面有一个 position absolute，重排了几次
答： 两次，因为外面的 position 也会维护着一个 dom 树，里面再有 position absolute 还是会继续重排 



Chrome 渲染过程
![](Javascript%20%E9%9D%A2%E8%AF%95%E9%A2%98/687474703a2f2f67772e616c6963646e2e636f6d2f6d742f5442315055416e4e705858585861355856585858585858585858582d3632342d3238392e706e67.png)


通过图其实也不难发现，为什么重排必然会导致重绘。另外， 一般影响布局的都会引起重排

##  部署
jenkins

## 设计模式

### 观察者模式

### 消息订阅模式

vue computed 用的就是消息订阅模式

## Vue 相关

### 生命周期

巴拉巴拉…唯一需要去关注的 updated 和 beforeUpdate

### v-if 和 v-show 的区别

v-show always compiles and renders everything - it simply adds the "display: none" style to the element. It has a higher initial load cost, but toggling is very cheap.Incomparison, v-if is truely conditional: it is lazy, so if its initial condition is false, it won't even do anything. This can be good for initial load time. When the condition is true, v-if will then compile and render its content. Toggling a v-if block actually tearsdown everything inside it, e.g. Components inside v-if are acually destroyed and re-created when toggled, so toggling a huge v-if block can be more expensive than v-show.So when to use which really depends on the scenario.

### computed 和 watch 的区别

computed 是消息订阅模式
watch 还是只用到 `Object.defineProperty()`，即存取器

## 业务

### 占位图

前端是不可能提前获取到图片的宽高，

## 函数式编程

## node 相关

### 超线程

我觉得多线程是concurrent，是在同一时间段内的“并行”，在单cpu下是一种交替执行；多cpu下可能会是真正的并行运算吧。 并行是parallel，是真正的空间并行，是同一时刻的“并行”。
