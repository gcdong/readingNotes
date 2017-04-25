#Yii模型

```

<!--循环数据N条数据-->
foreach (News::find()->asArray()->batch(2) as $key => $value) {
	a($value);
}

<!--利用占位符删除数据-->
Test::deleteAll(('id>:id',array(':id'=>0)));
 ```
 
 ##关联查询
 
 ```
 
 $news  = News::find()->where(['id' => 1])->one();
 
$comment = $news->hasMany('sks\models\NewsComment', ['news_id' => 'id'])->asArray()->all();

<!--可以改造为-->

$comment = $news->hasMany(NewsComment::className(), ['news_id' => 'id'])->asArray()->all();

```

我们还可以直接放在model里面


```
public function getComment() {
	return 	$this->hasMany(NewsComment::className(), ['news_id' => 'id'])->asArray()->all();
}
```

我们就可以直接在controller里面操作
```
$model->getComment()

$model->comment;<!--这里会自动去找getComment()函数-->

##类的映射

很多时候,我们新建一个类的时候,Yii虽然说是延迟加载,但是它还是得去找,但是如果我们给它指定了路径的话,就会快不少

```

Yii::$classMap['sks\models\News'] = "D:\www\BFLMobileMall\sks\models\News.php";
```
这样就会快很多,但是建议把常用的加进去,不常用的不要加,要不数组太大,这样也会降低效率


##组件的延时加载

Yii::$app里面的一些组件是延时加载的

##缓存

关于缓存,需要有一个要说明一下就是依赖,依赖是什么意思,就是这个缓存依赖某个东西,假如这个东西发生了改变,这个缓存就会读不出来,,依赖分很多种,下面一个一个介绍一下

###文件依赖

如果文件发生了改变(修改时间什么之类的),之前依赖这个文件的缓存都读不出来

```
$cache = Yii:$app->cache;
// 实例化缓存文件依赖
$dependency = new \yii\caching\FileDependency(['fileName'=>'xx.text']);
// 添加一个缓存
$cache->add('key1','hello world!',3000m,$dependency);
```

这个时候就保存了一个缓存了,如果这个时候我们去修改了xx.text这个文件,这个缓存就读不出来了

###表达式依赖

这个用代码说明吧

```
$cache = Yii:$app->cache;
// 实例化缓存文件依赖
$dependency = new \yii\caching\ExpressionDependency(
    ['expression'] => '\YII::$app->request->get("name")';
)
// 添加一个缓存
$cache->add('key1','hello world!',3000m,$dependency);
```

这个依赖的意思是,我这个是依赖于url里面的name的参数的值,假如设定这个缓存的时候传的name=zhangsan,那么你去读取这个缓存的时候,这个name就必须还是zhangsan,要不是读不出来的(失效)

##DB依赖

```
$cache = Yii:$app->cache;
$dependency = new \yii\caching\Dependency({
    'sql' => 'select count(*) FROM order';
});
// 添加一个缓存
$cache->add('key1','hello world!',3000m,$dependency);
```

和上面的原理差不多,这里是如果这个sql查询出来的结果和设置的时候查询的结果不一样,那么这个缓存也是会失效的

##页面缓存

```
<?php if ($this->beginCache('cacheDiv')) { ?>
    <div>1132132123123131111111</div>
<?php $this->endCache(); }?>
```

上面这段代码写在views里面,读起来有点麻烦,意思就是,假如没有缓存,我就把这一段缓存下来,假如已经缓存了,就不在执行下面的了,直接就读缓存的内容

同样的,这个缓存也可以用依赖的,这样的话,就可以去检测数据库有么有发生变化或者文件有没有发生变化而去读取缓存

```
<!--缓存保留15S-->
$this->beginCache('cacheDiv',['duration'=>'15'])
<!--缓存依赖-这里$dependency参考上面的依赖->
$this->beginCache('cacheDiv',['dependency'=>$dependency])
```