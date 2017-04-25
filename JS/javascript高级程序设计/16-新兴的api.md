#新兴的API

##Geolocation API

地理定位这个API,其实在移动端才使用比较多

```
navigator.geolocation.getCurrentPosition(function(position) {
    console.log(position);
}, function(error) {
    console.log(error);
}, {
    enableHighAccuracy: true,
    timeout: 5000,
    maximumAge: 2500
});
```

getCurrentPosition这个函数有三个参数,第一个是一个回调的function,里面有位置的信息,第二个也是一个回调的function,调用错误的时候的信息,第三个是一个对象,用于设定信息的类型,可以设置的选项有三个

* enableHighAccuracy:布尔值,标识尽可能使用最精确的位置信息
* timeout:是以毫秒数表示的等待时间位置信息的最长事件
* maximumAge:表示上一次取得的坐标信息的有效时间,以毫秒来表示,如果时间到则重新获取新坐标信息

##File API

之前我们处理文件,都是这样

```
<input type="file" name="" value="" placeholder="">
```

但是现在有了这个File的api,就方便了不少,H5在DOM中为文件输入元素添加了一个files集合,在通过文件输入字段选择了一或多个文件的时候,files集合中将包含一组file对象,每个file对象对应着一个文件,每个file对象都有下列只读属性

* name:本地文件系统中的文件名
* size:文件的字节大小
* type:字符串,文件的MIME类型
* lastModifiedDate:字符串,文件上一次被修改的时间(只有chrome实现了这个属性)

举个例子

```
html

<input type="file" name="" value="" id="files-list" placeholder="">


js

var filesList = document.getElementById('files-list');
EventUtil.addHandler(filesList, "change", function(event) {
    var files = EventUtil.getTarget(event).files,
        i = 0,
        leng = files.length;

    for (var i = 0; i < leng; i++) {
        console.log(files[i]);
    }
})

```

###FileReader类型

FileReader类型实现的是一种异步文件读取机制,可以把FileReader想象成XMLHttpRequest,区别只是他读取的是文件系统,而不是远程服务器,为了读取文件中的数据,FileReader提供了如下几个方法

* readAsText(file,encoding):以纯文本形式读取文件,将读取到的文本保存在result属性中,第二个参数用于指定编码类型,是可选的
* readAsDataURL(file):读取文件并将文件以数据URL的形式保存在result属性中
* readAsBinaryString(file):读取文件并将一个字符串保存在result属性中,字符串中的每个字符表示一字节
* readAdArrayBuffer(file):读取文件并将一个包含文件内容的ArrayBuffer保存在result属性中

由于读取过程是异步的,所以FileReader也提供了几个事件,其中最有用的三个事件是progress,error,和load,分别表示是否又读取了新数据,是否发生了错误以及是否已经读完了整个文件

没过50ms左右,就会触发一次progress事件,通过事件对象可以获得与XHR的progress事件相同的信息(属性):lengthComputable,loaded,和total,另外,尽管没有可能包含全部数据,但每次progress事件中都可以同庚FileReader的result属性读到文件内容

由于种种原因无法读取文件,就会触发error事件,触发error事件时,相关的信息将保存到FileReader的error属性中,这个属性中将保存一个对象,该对象只有一个属性code,即错误码,这个错误码

* 1:未找到文件
* 2:安全性错误
* 3:读取中断
* 4:表示文件不可读
* 5:编码错误

文件成功加载后会触发load事件,如果发生了error事件,如果发生了error事件,就不会发生load事件,以下是一个使用上述三个事件的例子

```
HTML

<input type="file" name="" value="" id="files-list" placeholder="">
<div id="progress"></div>
<div id="output"></div>

JavaScript

var filesList = document.getElementById('files-list');
EventUtil.addHandler(filesList, "change", function(event) {
    var files = EventUtil.getTarget(event).files,
        info = "",
        progress = document.getElementById('progress'),
        output = document.getElementById('output'),
        type = "default",
        reader = new FileReader();


    if (/image/.test(files[0].type)) {
        reader.readAsDataURL(files[0]);
        type = 'image';
    } else {
        reader.readAsText(files[0]);
        type = 'text';
    }

    reader.onerror = function() {
        output.innerHTML = "打开错误,错误代码" + reader.error.code;
    };

    reader.onprogress = function(event) {
        if (event.lengthComputable) {
            progress.innerHTML = event.loaded + "/" + event.total;
        }
    };

    reader.onload = function() {
        var html = "";

        switch (type) {
            case "image":
                html = '<img src="' + reader.result + '"  />';
                break;
            case "text":
                html = reader.result;
                break;
        }
        output.innerHTML = html;
    };
});
```

监听一个文件选择,然后根据不一样的文件类型来做不同的操作

```
HTML

    <input type="file" name="" value="" id="files-list" placeholder="">
    <div id="progress"></div>
    <div id="output"></div>
    
JavaScript

var filesList = document.getElementById('files-list');
EventUtil.addHandler(filesList, "change", function(event) {
    var files = EventUtil.getTarget(event).files,
        info = "",
        progress = document.getElementById('progress'),
        output = document.getElementById('output'),
        type = "default",
        reader = new FileReader();


    if (/image/.test(files[0].type)) {
    	  //是图片的时候.读取地址
        reader.readAsDataURL(files[0]);
        type = 'image';
    } else {
    	 //非图片,读取内容
        reader.readAsText(files[0]);
        type = 'text';
    }

    reader.onerror = function() {
        output.innerHTML = "打开错误,错误代码" + reader.error.code;
    };

    reader.onprogress = function(event) {
        if (event.lengthComputable) {
            progress.innerHTML = event.loaded + "/" + event.total;
        }
    };

    reader.onload = function() {
        var html = "";
			
        switch (type) {
        
            case "image":
                html = '<img src="' + reader.result + '"  />';
                break;
            case "text":
                html = reader.result;
                break;
        }
        output.innerHTML = html;
    };
});

```

###对象URL
	
对象URL也被称为blob URL,指的是引用保存在File或Blob中的数据URL,使用对象URL的好处是不必把文件读取到JavaScript中而直接使用文件内容.为此,只要在需要文件内容的地方提供对象URL即可.要创建对象URL,可以使用window.URL.createObjectURL()方法,并传入File或Blob对象,这个方法在chrome中的实现叫window.webkitURL.createObjectURL(),因此可以通过如下函数来消除命名的差异

```
function createObjectURL(blob) {
    if (window.URL) {
        return window.URL.createObjectURL(blob);
    } else if (window.webkitURL) {
        return widows.webkitURL.createObjectURL(blob);
    } else {
        return null;
    }
}
```

这个函数的返回值是一个字符串,指向一块内存的地址,因为这个字符串是URL,所以在dom中也能使用

```
var filesList = document.getElementById('files-list');
EventUtil.addHandler(filesList, "change", function(event) {
    var files = EventUtil.getTarget(event).files,
        info = "",
        progress = document.getElementById('progress'),
        output = document.getElementById('output'),
        type = "default",
        // reader = new FileReader();
        url = createObjectURL(files[0]);

    if (url) {
        if (/image/.test(files[0].type)) {
            output.innerHTML = '<img src="' + url + '" alt="" />';
        } else {
            output.innerHTML = '不是图片';
        }
    } else {
        output.innerHTML = "你的浏览器不支持";
    }

});
```

还有这个情况是占据内容的,如果移除的话,我们也是需要写一个兼容的方法的

```
function revokeObjectURL(url) {
    if (window.URL) {
        return window.URL.revokeObjectURL(url);
    } else if (window.webkitURL) {
        window.webkitURL.revokeObjectURL(url);
    }
}

```

###读取拖放的文件

围绕读取文件信息,结合HTML5拖放API和文件API,能够创造出令人瞩目的用户界面:在页面上创建了自定义的放置目标之后,你可以从桌面上把文件拖放在该目标,与拖放一张图片或者一个链接类似,从桌面上把文件拖放到浏览器这也会触发drop事件,而且可以在event.dataTransfer.files中读取到被放置的文件,当然此时它是一个file对象,与通过文件输入字段取得的file对象一样,并且可以和xhr结合

```
var droptarge = document.getElementById('droptarget');

function handleEvent(event) {
    var info = "",
        output = document.getElementById('output'),
        files, i, len;

    EventUtil.preventDefault(event);

    if (event.type == 'drop') {
        data = new FormData();
        files = event.dataTransfer.files;
        i = 0;
        len = files.length;

        while (i < len) {
            // 这里是关键
            data.append("file" + i, files[i]);
            i++;
        }
        xhr = new XMLHttpRequest();
        xhr.open("post", "index.php", true);
        xhr.onreadystatechange = function() {
            if (xhr.readyStatus == 4) {
                console.log('上传完毕');
            }
        }
        xhr.send(data);
        output.innerHTML = info;
    }
}

EventUtil.addHandler(droptarget, "dragenter", handleEvent);
EventUtil.addHandler(droptarget, "dragover", handleEvent);
EventUtil.addHandler(droptarget, "drop", handleEvent);

```





