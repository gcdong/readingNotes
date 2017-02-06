#基本类型
##string类型
###1.字符方法
charAt()和charCodeAt()

```
var stringValue = "hello world";

stringValue.charAt(1)      => e
stringValue.charCodeAt(1)  => 101  //这个是ASCII码
stringValue[1]             => e

```
###2.字符串操作方法
concat()

```
var stringValue = "hello ";
var result = stringValue.concat("world");
alert(result); //"hello world"

*其实用 + 也是可以的

```

slice()、substr()、substring()

```
var stringValue = "hello world";alert(stringValue.slice(3));        //"lo world" 
alert(stringValue.substring(3));    //"lo world"     
alert(stringValue.substr(3));       //"lo world"  
alert(stringValue.slice(3, 7));     //"lo w"    
alert(stringValue.substring(3,7));  //"lo w"       
alert(stringValue.substr(3, 7));    //"lo worl"   


alert(stringValue.slice(-3));         //"rld"  
alert(stringValue.substring(-3));     //"hello world"      
alert(stringValue.substr(-3));        //"rld"  
alert(stringValue.slice(3, -4));      //"lo w"     
alert(stringValue.substring(3, -4));  //"hel"         
alert(stringValue.substr(3, -4));     //""(空字符)

***

```
当两个都是整数的时候，slice和substring是一样的，都是从N到M，但是substring是会把两个参数里面小的截取到大的,而slice不会，也就是说，如果前面是大于后面的话，slice就会是空字符串,而substr就是从N开始截取M个

但是当出现出现负数的时候，情况就比较复杂一点了，slice会将它字符串的长度与对应的负数相加，结果作为参数；substr则仅仅是将第一个参数与字符串长度相加后的结果作为第一个参数；substring则干脆将负参数都直接转换为0

###3.字符串位置方法
indexOf()  lastIndexOf()

```
var stringValue = "hello world";
alert(stringValue.indexOf("o"));             //4
alert(stringValue.lastIndexOf("o"));         //7

```

indexOf是从一开始找字符，如果有第二个参数，就从第二个参数的位置开始找，找到就返回在整个字符串中的位置，找不到就返回-1，而lastIndexOf则是从后面开始找，

###4. trim()  

```
var stringValue = "   hello world   ";
var trimmedStringValue = stringValue.trim();
alert(stringValue);            //"   hello world   "
alert(trimmedStringValue);     //"hello world"
```

其实就是去掉两边的空格

###5.字符串大小转换方法
toLowerCase() toLocaleLowerCase() toUpperCase()  toLocaleUpperCase()

```
var stringValue = "hello world";
alert(stringValue.toLocaleUpperCase());  //"HELLO WORLD"
alert(stringValue.toUpperCase());        //"HELLO WORLD"
alert(stringValue.toLocaleLowerCase());  //"hello world"
alert(stringValue.toLowerCase());        //"hello world"
```

其实都是大小写的转化，加上locale会对一些特定语言支持更好

###6.字符串的模式匹配

match()

```
var text = "cat, bat, sat, fat";var pattern = /.at/;//  pattern.exec(text)  var matches = text.match(pattern); 
alert(matches.index);
//0 alert(matches[0]);
//"cat" 
alert(pattern.lastIndex); 
//0

```
---
search()

```
var text = "cat, bat, sat, fat";var pos = text.search(/at/);alert(pos);   //1

```

search就是找匹配的第一个字符串的位置，没有就返回-1


---
replace()

```
var text = "cat, bat, sat, fat";var result = text.replace("at", "ond");alert(result);    //"cond, bat, sat, fat"result = text.replace(/at/g, "ond");alert(result);    //"cond, bond, sond, fond"
```

```
这里比较复杂，看不是很懂
var text = "cat, bat, sat, fat";result = text.replace(/(.at)/g, "word ($1)");alert(result);    //word (cat), word (bat), word (sat), word (fat)

```

###7.localeCompare()

```
var stringValue = "yellow"; 
alert(stringValue.localeCompare("brick")); //1 
alert(stringValue.localeCompare("yellow")); //0 
alert(stringValue.localeCompare("zoo")); //-1
```
参数里面字符串如果在字母表里面相对靠后，则返回-1，相同就返回0，靠前就返回1，大写字母永远在小写前面，可以统一大小写以后再比较
###8.fromCharCode()  

```

alert(String.fromCharCode(104, 101, 108, 108, 111)); //"hello"

```

从ASICII码转回相对于的字符串