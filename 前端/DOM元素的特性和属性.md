# DOM元素的特性和属性

1.特性

    每个DOM元素都有一或多个特性，特性用来给出相应元素或其内容的附加信息，特性名称不区分大小写。

    特性设置：通过在HTML文档标签中设置`attributeName='value'`，或者通过`element.setAttribute('attributeName','value')`设置；
    特性访问：通过`element.getAttribute(attributeName)`取得特性值；
    删除特性：通过方法`element.removeAttribute()`。

    特性有公认特性（如id,class,title,lang,dir）和自定义特性。需要注意的是，根据HTML5规范，自定义特性要加上data-前缀以便验证。
    此外，通过`element.attributes`可以访问元素的所有特性，包括公认特性和自定义特性，结果是一个NameNodeMap，类似NodeList，是
一个“动态”集合。
2.属性

    属性，说成是DOM对象的属性更容易理解，即通过`element.propertyName`进行设置和访问。通过`delete element.propertyName`进行删除。
3.属性与特性的联系

    对于元素公认特性，元素也有相应的属性与其一一对应，即只有公认特性会以属性的形式添加到DOM对象中。
    可以通过`element.propertyName`访问到与元素特性值相同的元素属性的值，在这里，特性和属性是双向的，即通过`element.getAttribute()`
改变了元素特性，那么相应的属性值也会跟着变化，同样，通过`element.propertyName`改变元素属性，那么相应的特性也会跟着变化。
需注意，特性名不总是与属性名相同，例如特性class对应的属性名为className。

    元素自定义特性，在元素中不存在相应属性，即不能以`element.propertyName`的形式访问到其相应属性。在这里，特性和属性没有关系，即通
过`element.setAttribute()`设置的元素自定义特性，没有属性与之相对应，同样，通过`element.propertyName`设置的自定义属性，也不能通过
`element.getAttribute()`访问到。
4.特例

    有两类特殊的公认特性，虽然有对应的属性名，但是属性的值与通过`getAttribute()`返回的值不同：
    (1)style：通过属性访问，即`element.style`,返回一个对象；通过`element.getAttribute('style')`访问，返回CSS文本。
    (2)onclick这样的事件处理程序：通过属性访问，即`element.onclick`,返回一个JavaScript函数或者null；通过`element.getAttribute('onclick')`
访问，返回相应代码的字符串。

    因此，在通过JavaScript以编程方式操作DOM时，不经常使用`getAttribute()`，只是用对象的属性。只在需要访问自定义特性值的情况下才
使用`getAttribute()`。
5.测试代码

```html
HTML

<div id="myDiv" dir="ltr" myAttribute="myattribute" class="helloClass">Hello</div>

js

//取得HTML元素
var div = document.getElementById('myDiv');
//访问属性
console.log(div.dir);//ltr
console.log(div.myAttribute);//undefined
console.log(div.className);//helloClass
//访问特性
console.log(div.getAttribute('id'));//myDiv
console.log(div.getAttribute('myAttribute'));//myattribute

//设置自定义特性
div.setAttribute('myTestAttribute1','myTestAttribute1');
//设置公认特性
div.setAttribute('title','divTitle');
//设置自定义属性
div.myTestAttribute2 = 'myTestAttribute2';
//设置公认特性对应的属性
div.dir = 'rtl';

//访问公认特性对应的属性
console.log(div.title);
console.log(div.dir);
//访问公认特性
console.log(div.getAttribute('title'));
//访问自定义属性
console.log(div.myTestAttribute2);//myTestAttribute2
//访问自定义属性对应的特性
console.log(div.getAttribute('myTestAttribute2'));//null
//访问通过属性设置的公认特性
console.log(div.getAttribute('dir'));//rtl
//访问元素所有特性，包括公认特性和自定义特性
console.log(Array.prototype.slice.call(div.attributes,0));//[id, dir, myattribute, class, mytestattribute1, title]

```