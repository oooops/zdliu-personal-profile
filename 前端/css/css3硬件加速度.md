# css3硬件加速度

硬件加速的工作原理

浏览器接收到页面文档后，会将文档中的标记语言解析为DOM树。DOM树和CSS结合后形成浏览器构建页面的渲染树。
渲染树中包含了大量的渲染元素，每一个渲染元素会被分到一个图层中，每个图层又会被加载到GPU形成渲染纹理，
而图层在GPU中 transform 是不会触发 repaint 的，这一点非常类似3D绘图功能，
最终这些使用 transform 的图层都会由独立的合成器进程进行处理。


3D 和 2D transform 的区别就在于，浏览器在页面渲染前为3D动画创建独立的复合图层，
而在运行期间为2D动画创建。动画开始时，生成新的复合图层并加载为GPU的纹理用于初始化 repaint。
然后由GPU的复合器操纵整个动画的执行。最后当动画结束时，再次执行 repaint 操作删除复合图层。

#### 使用 GPU 渲染元素
并不是所有的CSS属性都能触发GPU的硬件加速，实际上只有少数属性可以，比如下面的这些：
* transform
* opacity
* filter

为了避免 2D transform 动画在开始和结束时发生的 repaint 操作，我们可以使用黑科技：
```css
.example1 { transform: translateZ(0); } 
.example2 { transform: rotateZ(360deg); }
```

或 `will-change` 该属性允许开发者告知浏览器哪一个属性即将发生变化，从而为浏览器对该属性进行优化
提供了时间著作权归作者所有。

```css
*{
/* 关键字值 */
will-change: auto;
will-change: scroll-position;
will-change: contents;
will-change: transform;        /* <custom-ident>示例 */
will-change: opacity;          /* <custom-ident>示例 */
will-change: left, top;        /* 两个<animateable-feature>示例 */

/* 全局值 */
will-change: inherit;
will-change: initial;
will-change: unset;
}
```