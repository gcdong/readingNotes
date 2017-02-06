#Global对象
##URL编码方法
###encodeURI()  encodeURIComponent()
encodeURI不会对那些属于IRI本身的的特殊字符进行编码，而encodeURIComponent则是会对所有非标准的进行编码

```
var uri = "http://www.wrox.com/illegal value.htm#start";
alert(encodeURI(uri));//"http://www.wrox.com/illegal%20value.htm#start"
alert(encodeURIComponent(uri))//"http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.htm%23start" 

```
相对应有编码自然就会有解码

encodeURI() => decodeURI() , encodeURIComponent() => decodeURIComponent()

```
var uri = "http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.htm%23start";
alert(decodeURI(uri));//http%3A%2F%2Fwww.wrox.com%2Fillegal value.htm%23start

alert(decodeURIComponent(uri));
//http://www.wrox.com/illegal value.htm#start```

##eval()方法

简单理解为执行参数里面的代码，即使里面是一个方法，也是在执行eval的时候才创建

```
var msg = "hello 
eval("alert(msg)");    //"hello world"

eval("var msg = 'hello world'; ");alert(msg);     //"hello world"
```


##Global对象的属性
还有很多的方法是属于global的，在ES5的严格模式下面，这些都是不能被赋值的，也就是你不可以这样

```
var undefined = 'xxx';

```


##window对象

其实window对象就包含了golbal对象

```
var color = "red";function sayColor(){    alert(window.color);}window.sayColor();  //"red"
```

##Math对象
###min()和max()

```
var max = Math.max(3, 54, 32, 16);alert(max);    //54var min = Math.min(3, 54, 32, 16);alert(min);    //3
```

###舍入方法

```
alert(Math.ceil(25.9));     //26alert(Math.ceil(25.5));     //26alert(Math.ceil(25.1));     //26alert(Math.round(25.9));    //26alert(Math.round(25.5));    //26alert(Math.round(25.1));    //25alert(Math.floor(25.9));    //25alert(Math.floor(25.5));    //25alert(Math.floor(25.1));    //25
```

###random()方法

返回大于等于0小于1的一个随机数

```
返回1-10之间的随机数
var num = Math.floor(Math.random() * 10 + 1);

我们可以利用这个小技巧用来做一个方法专门返回N到M之间的随机数

function selectFrom(lowerValue, upperValue) {	var choices = upperValue - lowerValue + 1;	return Math.floor(Math.random() * choices + lowerValue);}

甚至可以用来做一些随机的数组选择

var colors = ["red", "green", "blue", "yellow", "black", "purple", "brown"]; var color = colors[selectFrom(0, colors.length-1)];alert(color); //                 

```


  
  
  




















