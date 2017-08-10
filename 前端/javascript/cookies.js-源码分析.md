# cookies.js-源码分析

这次分享的是一个前端操作cookie的库（cookies.js）
`cookies.js` 的源码，整个库只有 80 余行，小巧精致，没有任何冗余的代码，里面的设计和实现有很多值得学习的地方。

点击可查看`cookies.js`的最新 [GIT源码地址](https://github.com/franciscop/cookies.js)

```js
// cookies 是库的主函数，接收两个参数
// @param data String/Object
// 如果传入的是 String 则用于查询页面的 document.cookie 有无该字段
// 如果传入的是 Object 则用于序列化地将其键值加入 document.cookie
// @param opt Object: 自定义配置对象
var cookies = function (data, opt) {
  // 一个合并对象属性的方法，和Object.assign有些类似
  // default 函数是的功能是扩展（extend）对象
  // 使用 defs 对象来扩展 obj 对象
  // 返回的是扩展后 obj
  function defaults (obj, defs) {
    // 检测参数，设置无传入时的默认值，防止报错
    obj = obj || {};
    // 遍历 defs 对象的属性，检测属性在 obj 上的重复性，扩展之
    for (var key in defs) {
      if (obj[key] === undefined) {
        obj[key] = defs[key];
      }
    }
    return obj;
  }
  
  // 调用扩展函数，为 cookies 函数对象添加这些属性
  // cookies 是一个函数对象(并且是外部函数)
  // 第二个参数对象是默认配置对象
  // 默认配置对象内属性会被扩展到 cookies 函数对象上
  defaults(cookies, {
    // Basic Configuration
    expires: 365 * 24 * 3600,
    path: '/',
    secure: window.location.protocol === 'https:',
    // Advanced Configuration
    nulltoremove: true,
    autojson: true,
    autoencode: true,
    encode: function (val) {
      return encodeURIComponent(val);
    },
    decode: function (val) {
      return decodeURIComponent(val);
    }
  });
  // 将默认配置对象与传入的自定义配置对象 opt 扩展合并
  opt = defaults(opt, cookies);
  // 调整 expires 的工具函数
  function expires (time) {
    var expires = time;
    // 传入的不是 Date 实例对象而是数字时
    if (!(expires instanceof Date)) {
      expires = new Date();
      expires.setTime(expires.getTime() + (time * 1000));
    }
    return expires.toUTCString();
  }
  // 当调用 cookies 函数时传入的第一个参数是字符串时，也就是在 document.cookie 内进行字段查询时的处理
  // 思路肯定是先对 document.cookie 字符串进行对象化，再进行属性的查找
  if (typeof data === 'string') {
    // 处理查找过程：
    // 1. 以 ; 分割转为数组
    // 2. 核对配置，是否对数组的每项进行 decode
    // 3. 将数组的每一项继续以 = 分割，变为二维数组
    // 4. 将二维数组 reduce 成为一个对象
    // 5. 在对象中查找我们目标 data 属性
    var value = document.cookie.split(/;\s*/)
      .map(opt.autoencode ? opt.decode : function (d) { return d; })
      .map(function (part) { return part.split('='); })
      .reduce(function (parts, part) {
        parts[part[0]] = part[1];
        return parts;
      }, {})[data];
    // 如果不需要 Json 序列化，直接返回查找到的结果
    if (!opt.autojson) return value;
    // 当 value 值为 NaN 或 undifined 时，JSON.parse() 会报错，所以 try-catch 一下
    try {
      return JSON.parse(value);
    } catch (e) {
      return value;
    }
  }
  // Set each of the cookies
  // 当我们传入的 data 是一个对象时，现在就是将这个 Data 对象的数据写入 document.cookie 了
  for (var key in data) {
    // 当 data 对象的某个 key 的 value 是 
    // 1. undefined
    // 2. null, 且配置里设定了当属性为 null 时移除的规则
    // 时就说明 document.cookie 已经过期（expired === true），我们的 document.cookie 需要更新了
    var expired = data[key] === undefined || (opt.nulltoremove && data[key] === null);
    // 三目：查看配置，是否对自动 data[key] 进行 JSON 序列化
    var str = opt.autojson ? JSON.stringify(data[key]) : data[key];
    var encoded = opt.autoencode ? opt.encode(str) : str;
    // 将等下要写入的 encode 设为 ''，等下写入 document.cookie 就是对该字段执行删除操作
    if (expired) encoded = '';
    var res = opt.encode(key) + '=' + encoded +
      (opt.expires ? (';expires=' + expires(expired ? -10000 : opt.expires)) : '') +
      ';path=' + opt.path +
      (opt.domain ? (';domain=' + opt.domain) : '') +
      (opt.secure ? ';secure' : '');
    if (opt.test) opt.test(res);
    // document.cookie 是追加写入的，不要担心被覆盖
    // for 循环，依此更新(追加或删除) document.cookie
    document.cookie = res;
  }
  // 返回的又是 cookies 函数
  // 所以我们就可以进行链式调用来连续操作了
  return cookies;
};
// 兼容各个标准的模块加载器
// 将 cookies 函数暴露出去
(function webpackUniversalModuleDefinition (root) {
  if (typeof exports === 'object' && typeof module === 'object') {
    module.exports = cookies;
  } else if (typeof define === 'function' && define.amd) {
    define('cookies', [], cookies);
  } else if (typeof exports === 'object') {
    exports['cookies'] = cookies;
  } else {
    root['cookies'] = cookies;
  }
})(this);
```

