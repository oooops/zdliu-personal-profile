# CSS粘住固定底部的5种方法

* 方法一：全局增加一个负值下边距等于底部高度
```html
<html>
<head>
<style>
html, body {
  height: 100%;
  margin: 0;
}
.wrapper {
  min-height: 100%;

  /* Equal to height of footer */
  /* But also accounting for potential margin-bottom of last child */
  margin-bottom: -50px;
}
.footer,
.push {
  height: 50px;
}
</style>
</head>
<body>
  <div class="wrapper">

      content

    <div class="push"></div>
  </div>
  <footer class="footer"></footer>
</body>
</html>

```

* 方法二：底部元素增加负值上边距
虽然这个代码减少了一个.push的元素，但还是需要增加多一层的元素包裹内容，
并给他一个内边距使其等于底部的高度，防止内容覆盖到底部的内容。

```html
<html>
<head>
<style>
html, body {
  height: 100%;
  margin: 0;
}
.content {
  min-height: 100%;
}
.content-inside {
  padding: 20px;
  padding-bottom: 50px;
}
.footer {
  height: 50px;
  margin-top: -50px;
}
</style>
</head>
<body>
  <div class="content">
    <div class="content-inside">
      content
    </div>
  </div>
  <footer class="footer"></footer>
</body>
</html>
```

* 方法三：使用calc()计算内容的高度

```html
<html>
<head>
<style>
.content {
  min-height: calc(100vh - 70px);
}
.footer {
  height: 50px;
}
</style>
</head>
<body>
  <div class="content">
    content
  </div>
  <footer class="footer"></footer>
</body>

</html>
```
* 方法四：使用flexbox
  
```html
<html>
<head>
<style>
html {
  height: 100%;
}
body {
  min-height: 100%;
  display: flex;
  flex-direction: column;
}
.content {
    flex: 1 0 auto;
    padding: 20px;
}
.footer {
    flex-shrink: 0;
    padding: 20px;
}
</style>
</head>
<body>
  <div class="content">
    content
  </div>
  <footer class="footer"></footer>
</body>

</html>
```
* 方法五：使用grid布局
  
```html
<html>
<head>
<style>
html {
  height: 100%;
}
body {
  min-height: 100%;
  display: grid;
  grid-template-rows: 1fr auto;
}
.footer {
  grid-row-start: 2;
  grid-row-end: 3;
}
</style>
</head>
<body>
  <div class="content">
    content
  </div>
  <footer class="footer"></footer>
</body>

</html>
```





