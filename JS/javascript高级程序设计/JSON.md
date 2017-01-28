#JSON

##JSON对象

json对象有两个方法:stringfy()和parse(),在最简单的情况下,把这两个方法分别用于把JavaScript对象序列化为JSON字符串和吧JSON字符串解析为原生JavaScript.

```
var book = {
    title: "标题",
    authors: [
        "作者"
    ],
    edition: 3,
    year: 2011
}

<!--这是js对象-->
console.log(book);
<!--这是json-->
var JSONbook JSON.stringify(book);
```

默认的情况下,一个JavaScript对象序列化为一个JSON字符串以后,是不包含任何的空格字符和缩进的,在序列化JavaScript对象时,所有函数及原型诚衣都会被有意忽略,不体现在结果中,此外,值为undefined的任何属性性也都会被跳过,结果中最终都是值为有效JSON数据类型的实例属性

当然转了过去还是可以转回来的

```
JSON.parse(JSONbook);
```


###序列化选项

实际上,JSON.stringify()除了要序列化的JavaScript对象外,还可以接收另外两个参数,第一个参数是一个过滤器,可以是数组,也可以是一个函数,第二个参数一个选项,表示是否在JSON字符串中保留缩进

1. 过滤结果
	
	```
	var book = {
	    title: "标题",
	    authors: [
	        "作者"
	    ],
	    edition: 3,
	    year: 2011
	}
	var jsonBook = JSON.stringify(book,["title","edition"]);
	
	//结果是这样的了{"title":"标题","edition":3},也就是只要title和edition
	console.log(jsonBook);
	
	```	
	
	为了改变序列化的结果,函数返回的值就是响应键的值,不过一要注意,如果函数返回了undefined,那么响应的属性就会被忽略,可以看下面的代码,我们第二个参数用一个函数来处理,里面有两个参数,第一个是键值,第二个是值,我们去判断这个key,根据不一样的东西来返回不一样的数据,可以看到,里面可以直接把值来处理了,十分方便,还有如果是undefined的时候,结果就没有了这个属性了
	
	```
	var book = {
	    title: "标题",
	    authors: [
	        "作者"
	    ],
	    edition: 3,
	    year: 2011
	}
	var jsonBook = JSON.stringify(book, function(key, value) {
	    switch (key) {
	        case "authors":
	            return value.join(',');
	            break;
	        case "year":
	            return 5000;
	            break;
	        case "edition":
	            return undefined;
	        default:
	            return value;
	            break;
	    }
	});
	// {"title":"标题","authors":"作者1,作者2","year":5000}
	console.log(jsonBook);
	```
	
2. 字符串缩进

	JSON.stringify()的方法的第三个参数用于控制结果中的缩进和空白符,如果这个参数是一个数值,那它表示的是每个级别缩进的空格书,假如要在每个级别缩进4个空格,可以这样写代码,这样其实是为了阅读好看,但是需要注意一个地方,就是如果这个数字大于10,都会自动转回10的
	
	```
	var jsonBook = JSON.stringify(book,null,4);
	```
	
	但是如果我们不用数字呢,会怎样
	
	```
	var jsonBook = JSON.stringify(book,null,"- -");
	
	<!--变成了这样,用你的参数作为缩进,同样不能超过10个字符,要不就会只出现前10个-->
	{
	- -"title": "标题",
	- -"authors": [
	- -- -"作者1",
	- -- -"作者2"
	- -],
	- -"edition": 3,
	- -"year": 2011
	}
	```
3. toJSON方法

	有时候JSON.stringify不能满足我们的需求,我们可以给对象队医toJSON方法,返回其自身的JSON的数据格式,如果有这个函数的对象,去调用JSON.stringify的时候,都会直接返回toJSON方法里面的内容?
	
	```
	var book = {
	    title: "标题",
	    authors: [
	        "作者1",
	        "作者2"
	    ],
	    edition: 3,
	    year: 2011,
	    toJSON : function(){
	        return this.title;
	    }
	}
	//"标题"
	var jsonBook = JSON.stringify(book);
	```
	
	那么我们来看一下,在调用JSON.stringify()的时候,顺序是这样的
	
	1. 如果有toJSON,那么就直接用了toJSON,但是必须要有有效的值啊,要不就只能undefined了
	2. 如果有提供第二个参数,应用这个过滤器.传入函数过滤器的值是第一步返回的值
	3. 对第二步返回的每个值进行相对的序列化
	4. 如果提供第三个参数,则执行相应的格式化

###解析选项

JSON.parse方法也可以接收另一个参数,该参数是一个函数,讲每个键值对儿上调用,其实用起来和JSON.stringify差不多

```
var book = {
    title: "标题",
    authors: [
        "作者1",
        "作者2"
    ],
    edition: 3,
    year: 2011,
    releaseDate: new Date(2011, 11, 1)
}
var jsonBook = JSON.stringify(book);
console.log(jsonBook);//{"title":"标题","authors":["作者1","作者2"],"edition":3,"year":2011,"releaseDate":"2011-11-30T16:00:00.000Z"}
var bookCopy = JSON.parse(jsonBook,function(key,value){
    if (key == 'releaseDate') {
        return new Date(value)
    } else {
        return value;
    }
});

console.log(bookCopy.releaseDate.getFullYear()); //2011
```

上面对于book里面的releaseDate的处理,先是变为了字符串,接下来在还原的过程中,直接又用new Date格式了一下,就变成了一个合法的时间对象了


