#DOM扩展
##选择API
###querySelect()方法
简单来说就是css选择器，但是需要注意一点的就是，css元素有多个，就默认选中第一个而已

```
// 取得body元素
var body = document.querySelector("body");

// 取得ID为"myDiv"的元素
var myDiv = document.querySelector("#myDiv");

// 取得类为"selected"的第一个元素
var selected = document.querySelector("selected");

// 取得类为"button"的一个图像元素
var img = document.querySelector("img.button");
```
###querySelectAll()方法

其实和querySelect差不多，只不过，这个是一个nodeList对象。意思就是这是数组


```
// 取得div中所有<em>的元素
var ems = document.getElementById('myDiv').querySelectorAll("em");

// 取得类为"selected"的所有元素
var selecteds = document.querySelectorAll(".selected");

// 取得所有<p>元素中的所有<string>元素
var strong = document.querySelectorAll("p strong");
```

##元素遍历

对于元素间的空格，IE9及之前的版本不会返回文本节点，而其它浏览器都会返回文本节点。这样就会导致在使用childnodes和firstChild的时候不一致，所以后面又定义了一系列属性

* childElementCount: 返回子元素(不包含文本节点和注释)
* firstElementChild: 指向第一个子元素；firstChild的元素版
* lastElementChild: 指向最后一个子元素；lastChild元素版
* previousElementSibling: 指向后一个同辈元素；previousSibling的元素版
* nextElementSibling: 指向后一个同辈元素；nextSibling的元素版

##HTML5

HTML4和之前在操作class的时候是十分麻烦，所以H5就加入了一些方法,但是必须要操作他的classList

* add(value): 将给定的字符串值添加对列表中。如果值已经存在，就不添加了
* contain(value): 查询是否存在这个class
* remove(value): 移除class
* toggle(valu): 切换class

```
// 取得div中所有<em>的元素
var myDiv = document.getElementById('myDiv');
var divClass = myDiv.firstElementChild.classList;
divClass.add('test'); //添加test
divClass.remove('test1'); //删除test1
console.log(divClass.contains('test1'));  //false
console.log(divClass.contains('test'));   //true
divClass.toggle('test'); //切换test
```
###焦点管理

```
<body>
	<button id="myButton">呵呵</button>
	<input type="text" id="myText">
</body>

</html>
<script>
// 取得div中所有<em>的元素
var button = document.getElementById("myText");
button.focus();
console.log(document.activeElement === button);
console.log(document.hasFocus());  //只能检测整个文档有没focus
</script>
```
###自定义数据属性

H5中可以为元素添加非标准的属性，但要添加data-的前缀

```
<body>
	<div id="myDiv" data-appId="12345" data-app-id="1212" data-myname="Nicholas"></div>
</body>

</html>
<script>
var div = document.getElementById("myDiv");

var appId = div.dataset.appId;
var myName = div.dataset.myname;
console.log(appId);
console.log(myName);
div.dataset.appId = 'hehe';

</script>
```
可以看到我们可以设置这个data-的属性，但是有一个地方需要注意，就是假如我们在dataset后面用的有大写，会自动在前面加-并且转为小写

###插入标记

看了上面那么多的方法，还是觉得操作html十分麻烦，但是我们可以这样

1. innerHTML属性
	在读模式下，innerHTML属性返回调用元素的所以子节点（包含元素、注释和文本节点）

```
<body>
	<div id="content">
		<p>This is a <strong>paragraph</strong> with a list following it.</p>
		<ul>
			<li>Item 1</li>
			<li>Item 2</li>
			<li>Item 3</li>
		</ul>
	</div>
</body>

</html>
<script>
var content = document.getElementById("content");
console.log(content.innerHTML);
content.innerHTML = "Hello & wecome,<b>\"reader\"!</b>";
</script>
```
我是在chrome下面做的这个操作，发现打印出来的content是包含空格的内容的

2. outHTML属性
	和innerHTML布艺的是，这个返回的会包含本身的节点，也就是#content也会返回，所以一旦是对这个属性重新赋值，也就会把之前的#content覆盖了

3. insertAdjacentHTML
	这个函数比较奇怪，可以在指定的节点前后插入HTML，它有两个参数，第一个是4选1，也就是选定的位置，后面就是HTML了

```
<body>
	<div id="content">
		<p>？？</p>
	</div>
</body>

</html>
<script>
var content = document.getElementById("content");
var p = document.createElement('p');
p.innerHTML = "hehe";
<!--在content前插入一个和content同级的元素-->
content.insertAdjacentHTML('beforebegin','beforebegin');
<!--在content里面插入一个和content子级的元素作为firstChild-->
content.insertAdjacentHTML('afterbegin','afterbegin');
<!--在content后插入一个和content同级的元素-->
content.insertAdjacentHTML('beforeend','beforeend');
<!--在content里面插入一个和content子级的元素作为lastChild-->
content.insertAdjacentHTML('afterend','afterend');
</script>
```
4. 内存以及性能的问题
	我们在这样操作HTML的时候是相对销毁内存的，所以尽量不要频繁的操作，可以把多个操作合成一个HTML字符串再进行操作

###scrollIntoView

这个方法其实就是让某个节点出现在页面的头或者尾巴(必须页面要够长出现滚动条)

##滚动