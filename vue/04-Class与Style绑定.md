#Class与Style绑定

数据绑定一个常见需求是操作元素的class列表和他的内联样式,因为它们都是属性,我们可以用v-bind处理他们:只需要计算出表达式最后的字符串,不过.字符串拼接麻烦又易错,因此,在v-bind用于class和style时,Vue.js专门增强了它.

##绑定HTML Class

###对象语法

我么可以传给v-bind:class一个对象,以动态地切换class

```
<div v-bind:class="{ active: isActive }"></div>
```
也可以设置多个值

```
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```

```
data: {
  isActive: true,
  hasError: false
}
```
当 isActive 或者 hasError 变化时，class 列表将相应地更新。例如，如果 hasError 的值为 true ， class列表将变为 "static active text-danger"。

你也可以直接绑定数据里的一个对象：

```
<div v-bind:class="classObject"></div>
```
```
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

###用在组件上

当你在一个定制的组件上用到class属性的时候,这些类将被添加到根元素上面,这个元素已经存在的类不会被覆盖

例如,如果你声明了这个组件:

```
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```
然后在使用它的时候添加了一些class:

```
<my-component class="baz boo"></my-component>
```

最终将被渲染成为:

```
<p class="foo bar baz boo">Hi</p>
```

同样的适用与绑定HTML class

```
<my-component v-bind:class="{ active: isActive }"></my-component>
```
当 isActive 为 true 的时候，HTML 将被渲染成为:

```
<p class="foo bar active">Hi</p>
```
###对象语法

v-bind:style的对象语法十分直观--看着非常像CSS,其实它是一个JavaScript对象.css属性名可以用驼峰式活短横分割命名

```
<div :style="{color:activeColor,fontSize:fontSize+'px'}">1</div>

data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

直接绑定到一个样式对象通常更好,让模板更清晰

```
<div :style="styleObject">1</div>

data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}

```

###数组语法

v-bind:style的数组语法可以将多个样式应用到一个元素上:

```
 <div :style="[baseStyle,overridingStyles]">1</div>
 
 
  data: function() {
        return {
            baseStyle :{
                fontSize: '40px'
            },
            overridingStyles:{
                color: 'red'
            }
        }
    }
    
```
  
###自动添加前缀

当v-bind:style 使用需要特定前缀的css属性时候,如transform,vue.js会自动侦查并添加相应的前缀
