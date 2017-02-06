#AJAX和Comet

##XMLHttpRequest对象

 现在的浏览器已经基本不用担心这个问题了
 
###XHR的用法

在使用XHR对象时候,要调用的第一个方法是open(),它接受3个参数,要发送的请求的类型,url和是否异步请求,但是open不是实际上发送数据,只是打开而已,需要发送数据的使用得使用send()方法

```
var xhr = createXHR();
xhr.open("get","test.php",false);
xhr.send(null);
```
这里send方法接收一个参数,也就是要送的数据,如果没有,就给null,在调用send以后,请求就会被分派到服务器,在收到数据以后,响应的数据会自动填充XHR对象的属性

* resopnseText:作为响应主题被返回的文本
* resopnXML:如果响应的内容是"text/xml"或"application/xml",这个属性中将保存包含着响应数据的XML DOM文档
* status:响应的HTTP状态
* statusText:HTTP状态的说明

我们可以凭借status先来判断是否已经请求成功了,再做接下了的事情

```
var xhr = createXHR();
xhr.open("post", "test.php", false);
xhr.send({
    data: "test"
});

if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    console.log(xhr.responseText);
} else {
    alert("请求失败了");
}
```

不过,很多时候我们用的是异步请求的,这个时候我们可以去检测XHR对象的readyState属性,该属性标识请求/响应过程的当前活动阶段.这个属性可取的值如下

* 0:未初始化,尚未调用open方法
* 1:启动,已经调用opend方法,但是还没有调用send方法
* 2:发送:已经调用send方法,但是还没有收到响应
* 3:接收:已经接收到部分的响应数据了
* 4:完成:已经接收到所有的数据了

一般我们是这样做的

```
var xhr = createXHR();
xhr.onreadystatechange = function(){
    if (xhr.readyState == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            console.log(xhr.responseText);
        } else {
            alert("请求失败了");
        }
    }
}

xhr.open("post", "test.php", true);
xhr.send({
    data: "test"
});

```

###HTTP头信息

默认情况下,发送xhr请求的同时,还会发送下列头信息

* Accept:浏览器能够处理的内容类型
* Accept-Charset:浏览器能够显示的字符集
* Accept-Encoding:浏览器能够处理的压缩编码
* Accept-Language:浏览器当前设置的语言
* Connection:浏览器与服务器之间链接的类型
* Cookie:当前页面设置的任意Cookie
* Host:发出请求的页面所在的域
* Referer:发出请求的页面的URL
* User-Agent:浏览器的用户代理字符串

虽然不同浏览器实际发送的头部信息会有所不同,但是以上基本上是所有浏览器都会发送的,使用setRequestHeader()方法可以设置自定义的请求头部信息,两个参数,而且需要在open以后send之前调用

```
xhr.open("post", "test.php", true);
xhr.setRequestHeader("MyHeader","MyValue");
xhr.send({
    data: "test"
});
```

服务器在接收到这种自定义的头部信息之后,可以执行相对用得后续操作,不过不建议使用浏览器已经定义好的那些属性,这样会影响到数据的正常发送,而且有部分浏览器是不允许这样做的

使用getResponseHeader方法传入头部字段名称,可以取得响应的响应头部信息.而调用getAllResponseHeaders犯法可以取得一个包含所有头信息的长字符串

###GET请求

使用get请求常常会发生一个错误,就是查询字符串的格式有问题,所以可以专门写个函数来处理

```
function addURLParam(url, name, value) {
    url += (url.indexOf("?") == -1 ? "?" : "&");
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
    return url;
}

url = addURLParam(url, "name", "Nicholas");url = addURLParam(url, "book", "Professional JavaScript");

```

###POST请求

```
function submitData() {
    var xhr = createXHR();
    xhr.onreadystatechange = function() {
        if (xhr.readyState == 4) {
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                // console.log(xhr.responseText);
            } else {
                alert("请求失败了");
            }
        }
    }

    xhr.open("post","test.php",true);
    xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
    var form = document.getElementById("user-info");
    xhr.send(serialize(form));
}
```

##XMLHttpRequest 2级

###FromData

现代web应用中频繁使用的一项功能就是表单数据的序列化,XMLHttpRequest2级为此定义了Formdata,可以直接发动过去


```
xhr.send(new FormData(form));
```

###超时设定

```
xhr.open("get", "timeout.php", true);
xhr.timeout = 1000;
xhr.ontimeout = function() {
    alert("Request did not return in a second.");
};
xhr.send(null);
```

###overrideMimeType

将返回来的数据强制当做某种数据类型来处理

```
var xhr = createXHR();
xhr.open("get", "text.php", true);
xhr.overrideMimeType("text/xml");
xhr.send(null);
```
##进度事件

* loadstart:在接收到响应数据的第一个字节时触发
* progress:在接收响应期间持续不断地触发
* error:在请求发生错误时触发
* abort:在因为调用about方法而终止连接时触发
* load:在接收到完整的数据响应时触发
* loadend:在通讯完成或者触发error,abort,load事件后触发

每个请求都从触发loadstart事件开始,接下来是一或多个progress,然后触发error,abort或load事件中的一个,最后以触发loadend事件结束

###load事件

```
var xhr = createXHR();
xhr.onload = function() {
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
        console.log(xhr.responsetText);
    } else {
        console.log("请求失败" + xhr.status);
    }
}
xhr.open("get", "index.php", true);
xhr.send(null);
```

###progress事件

我发现我无聊怎么去调试,这个事件都没办法打印正确的百分比

```
window.onload = function() {
    var xhr = createXHR();
    xhr.onload = function() {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            console.log(xhr.responseText);
            console.log('呵呵');
        } else {
            console.log("Request was unsuccessful: " + xhr.status);
        }
    };
    xhr.onprogress = function(event) {
        var divStatus = document.getElementById("status");
        if (event.lengthComputable) {
            divStatus.innerHTML = "接收到 " + event.position + " of " + event.totalSize + " bytes";
        }
    };
    xhr.open("get", "index.php", true);
    xhr.send(null);
};
```
##跨资源资源共享

一般来说,我们的xhr请求只能访问包含它或者位于同一个域中的资源,这是基于安全性的考虑,但是有时候我们是需要跨域访问的,那么,W3C就有了这个CORS,跨源资源共享,定义了在必须跨访问资源的时候,要怎么请求,基本就是定义HTTP的头来实现

比如一个简单的get或者post请求,主体是text/plain,那么在发送该请求的时候,我们多给它添加一个Origin头部,其中包含请求页面的源信息(协议,域名和端口),一遍服务器根据这个头信息来决定是否给予响应,例如下面这个就是一个头部的示例


```
Origin:http://www.ncczonline.net
```

如果服务器认为这个请求可以接受,那么就在Access-Control-Allow-Origin头部中回发相同的源信息(如果是公共资源,可以回发"*"),例如

```
Access-Control-Allow-Origin:Origin:http://www.ncczonline.net
```
如果没有这个头部,或者有这个头部但源信息不匹配,浏览器就会驳回请求,正常情况下,浏览器会处理请求,但是要注意的时候,请求和响应都不包含cookie信息

###除了IE以外的主流浏览器对于这个支持

在除了IE以外的浏览器(WebKit内核的)都通过XMLHttpRequest对象实现了对CORS的支持,其实和非跨域的用法一样的

```
var xhr = new createXHR();
xhr.onreadystatechange = function() {
    if (xhr.readyStatus == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            console.log(xhr.responseText);
        } else {
            console.log("请求失败") + xhr.statchus;
        }
    }
}
xhr.open("get", "http://www.somewhere-else.com/page", true);
xhr.send(null);

```

只是有些需要注意的地方

* 不能使用setRequestHeader()
* 不能发送和接收cookie
* 调用getAllRequrstHeaders()方法总会返回空字符串

###Preflighted Requests

CORS通过一种叫Preflighted Requests的透明服务器验证机制支持开发人员使用自定义头部,get或post方法,以及不同类型的主题内容,在使用下列高级选项来发动请求时,就会向服务器发动一个Preflight请求,这种请求使用OPTIONS方法.发动下列头部

* Origin:与简单的请求相同
* Access-Control-Request-Method:请求自身使用的方法
* Access-Control-Request-Headers:()可选自定义头部信息,多个头部以逗号隔开

```
Origin:http://www.test.net
Access-Control-Request-Method:POST
Access-Control-Request-Headers:NCZ
```

发送上面这个请求以后,服务器可以决定是否允许这种类型的请求,服务器通过在响应中发送如下头部与浏览器进行沟通

* Access-Control-Allow-Origin:与简单的请求相同
* Access-Control-Allow-Methods:允许的方法,多个以逗号分隔
* Access-Control-Allow-Headers:允许的头部,多个以逗号分隔
* Access-Control-Allow-Max-Age:应该将这个Preflight请求缓存多长时间(以秒计算)

例如

```
Access-Control-Allow-Origin: http://www.nczonline.netAccess-Control-Allow-Methods: POST, GETAccess-Control-Allow-Headers: NCZAccess-Control-Max-Age: 1728000```

Preflight请求结束以后,结果将按照响应中指定的时间缓存起来,而为此付出的代价只是第一次发送这种请求时会多一次http请求

默认情况下,跨源请求不提供凭据.通过将withCreadtials属性设置为true,可以指定每个请求应该发送凭据,如果服务器接受带凭据的请求,会用下面的HTTP头部来响应

```
Access-Control-Allow-Credentials:true
```

如果发送的是带凭据的请求,但服务器的响应中没有包含这个头部,那么浏览器就不会把响应交给JavaScript(那么js那边就会报错,调用了哦弄error),另外,服务器还可以在Preflight响应中发送这个http头部,表示允许源发送带凭证的请求

###跨浏览器的CORS

鉴于IE和其它浏览器的不一样,我们得写一个方法来统一

```
    var xhr = new XMLHttpRequest();
    if ("withCredentials" in xhr) {
        xhr.open(method, url, true);
    } else if (typeof XDomainRequest != "undefined") {
        vxhr = new XDomainRequest();
        xhr.open(method, url);
    } else {
        xhr = null;
    }

    return xhr;

}

var request = createCORSRequest("get", "http://www.somewhere-else.com/page/");
if (request) {
    request.onload = function() {
        // 对结果进行处理
    }
    requset.send();
}
```

###JSONP

JSONP是JSON with padding(填充式json或参数式json)的简写,看起来和json差不多,就是外面多了一个函数,就像下面这样

```
callback({"name":"dong"})
```

JSONP由两部分组成,回调函数和数据,回调函数是当响应到来时应该在页面中调用的函数,回调函数的名字一般是在请求中指定的,而数据就是传入回调函数中的JSON函数,下面是一个典型的JSONP请求

```
http://freegeoip.net/json/?callback=handleResponse
```

JSONP是通过动态加载script来使用的,使用时可以为src属性指定一个跨域URL,这里的\<script>和\<img>元素类似,都有能力不受限制地从其他域加载资源,因为JSONP是有效的JavaScript代码,所以在请求完成后,即在JSONP响应加载到页面中以后,就会立即执行

怎么去理解这个事情呢,其实就是我们的我们的标签元素,只有有scr的情况下,就是可以去进行跨域的请求的,例如img这种,那么,如果我们把script里面的src里面添加js的路径,去做请求,而且请求的数据的格式是一个函数里面包含了json,并且我们有定义这个函数,那么就可以请求了数据并且执行我们的函数了

```

function handleResponse(response) {
    alert("You re at IP address " + response.ip + ", which is in " +
        response.city + ", " + response.region_name);
}

var script = document.createElement("script");
script.src = "test.js?callback=handleResponse";<!--其实在这里只是单纯请求js不带参数也是可以的-->
document.body.insertBefore(script, document.body.firstChild);
```

```
test.js文件

handleResponse({"ip":"127.0.0.1","region_name":"湛江","city":"吴川"});
```

###Comet

简单来说,服务器推送,有两种方式实现,长轮询和流,长轮询就是定时的向服务器发动请求看数据有没有更新,大概就是发动一个请求到服务器,服务器就一直打开,直到有数据可发送,发送完数据以后,客户端关闭连接,随即又发起一个请求.这个过程在页面打开期间一直不断

而流就是发动了一次请求以后,服务器一直不断的向浏览器发动数据,然后去监听readystatechange一直为3.下面这个函数

```
function createStreamingClient(url, progress, finished) {
    var xhr = new XMLHttpRequest(),
        received = 0;

    xhr.open("get", url, true);
    xhr.onreadystatechange = function() {
        var result;
        if (xhr.readyState == 3) {

            // 只取得最新数据并调整计数器
            result = xhr.responseText.substring(received);
            received += result.length;

            // 调用progress回调函数
            progress(result);
        } else if (xhr.readyState == 4) {
            finished(xhr.responseText);
        }
    }
    xhr.send(null);
    return xhr;
}

var client = createStreamingClient("index.php", function(data) {
    console.log("Received" + data);
}, function(data) {
    console.log("Done!");
})

```
