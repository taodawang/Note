###html
1. Doctype作用？标准模式与兼容模式各有什么区别?
  - !DOCTYPE声明位于位于HTML文档中的第一行，处于html 标签之前。告知浏览器的解析器用什么文档标准解析这个文档。DOCTYPE不存在或格式不正确会导致文档以兼容模式呈现。
  - 标准模式的排版 和JS运作模式都是以该浏览器支持的最高标准运行。在兼容模式中，页面以宽松的向后兼容的方式显示,模拟老式浏览器的行为以防止站点无法工作。
2. html5有哪些新特性、移除了那些元素？如何处理HTML5新标签的浏览器兼容问题？如何区分 HTML 和HTML5？

    - HTML5 现在已经不是 SGML 的子集，主要是关于图像，位置，存储，多任务等功能的增加。
    - 绘画 canvas
    - 用于媒介回放的 video 和 audio 元素
    - 本地离线存储 localStorage 长期存储数据，浏览器关闭后数据不丢失；
    - sessionStorage 的数据在浏览器关闭后自动删除
    - 语意化更好的内容元素，比如 article、footer、header、nav、section
    - 表单控件，calendar、date、time、email、url、search
    - 新的技术webworker, websockt, Geolocation
    - 
    移除的元素
    - 纯表现的元素：basefont，big，center，font, s，strike，tt，u；
    - 对可用性产生负面影响的元素：frame，frameset，noframes；
    - 支持HTML5新标签：
    IE8/IE7/IE6支持通过document.createElement方法产生的标签，
    - 可以利用这一特性让这些浏览器支持HTML5新标签，
    - 浏览器支持新标签后，还需要添加标签默认的样式；

###CSS面试题


1. CSS隐藏元素的几种方法（至少说出三种）

Opacity:元素本身依然占据它自己的位置并对网页的布局起作用。它也将响应用户交互;
Visibility:与 opacity 唯一不同的是它不会响应任何用户交互。此外，元素在读屏软件中也会被隐藏;
Display:display 设为 none 任何对该元素直接打用户交互操作都不可能生效。此外，读屏软件也不会读到元素的内容。这种方式产生的效果就像元素完全不存在;
Position:不会影响布局，能让元素保持可以操作;
Clip-path:clip-path 属性还没有在 IE 或者 Edge 下被完全支持。如果要在你的 clip-path 中使用外部的 SVG 文件，浏览器支持度还要低;

2. CSS清除浮动的几种方法（至少两种）

使用带clear属性的空元素
使用CSS的overflow属性；
使用CSS的:after伪元素；
使用邻接元素处理；


3. 写出几种IE6 BUG的解决方法

双边距BUG float引起的 使用display
像素问题 使用float引起的 使用dislpay:inline -3px
超链接hover 点击后失效 使用正确的书写顺序 link visited hover active
Ie z-index问题 给父级添加position:relative
Png 透明 使用js代码 改
Min-height 最小高度 ！Important 解决’
select 在ie6下遮盖 使用iframe嵌套
为什么没有办法定义1px左右的宽度容器（IE6默认的行高造成的，使用over:hidden,zoom:0.08 line-height:1px）


4. 页面导入样式时，使用link和@import有什么区别？

link属于XHTML标签，除了加载CSS外，还能用于定义RSS, 定义rel连接属性等作用；而@import是CSS提供的，只能用于加载CSS;
页面被加载的时，link会同时被加载，而@import引用的CSS会等到页面被加载完再加载;
import是CSS2.1 提出的，只在IE5以上才能被识别，而link是XHTML标签，无兼容问题;

5. 介绍一下CSS的盒子模型？

有两种， IE 盒子模型、标准 W3C 盒子模型；IE的content部分包含了 border 和 pading;
盒模型： 内容(content)、填充(padding)、边界(margin)、 边框(border).


6. CSS3有哪些新特性？

CSS3实现圆角（border-radius:8px），阴影（box-shadow:10px），
对文字加特效（text-shadow、），线性渐变（gradient），旋转（transform）
transform:rotate(9deg) scale(0.85,0.90) translate(0px,-30px) skew(-9deg,0deg);//旋转,缩放,定位,倾斜
增加了更多的CSS选择器 多背景 rgba