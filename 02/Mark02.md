#1.MVC
- 通用的东西作为一个 Server
- 全都借助 $scope 实现
- 数据模型绑定在 $scope 下

##Controller
- 控制视图

##View
- 需要指令实现
- 变量向上查找，一直到根/找到

##$scope
- 一个 JS 对象
- 提供了一些方法 $watch()/$apply()
- 表达式的执行环境/作用域
- 树形结构，与 DOM 平行
- 根 $scope 只有一个，位于 ng-app 上
- 可以传播事件，向上或向下
- MVC 的基础，双向数据绑定的基础
- 可以用 angular.element($0).scope() 调试

#2.路由，模块，依赖注入

##路由
###ngRoute
- 根据地址栏不同处理不同链接

```javascript
$routeProvider.when('/hello', {
    templateUrl: 'tpls/hello.html',
    controller: 'HelloCtrl'
}).when(
).otherwise({
    redirectTo: '/hello'
});
```


###模块
```javascript
var helloModule=angular.module('HelloAngular', []);
//新建控制器
helloModule.controller('helloNgCtrl', ['$scope', function(#scope){
    $scope.greeting={
        text: 'Hello'
    };
}]);
```

- 推荐用总模块作为入口，挂上各个模块

```javascript
var bookStoreApp=angular.module('bookStroeApp',[
    'ngRoute', 'ngAnimate', 'bookStoreCtrls', 'bookStoreFilters',
    'bookStoreServices', 'bookStoreDirectives'
]);
```

###依赖注入
- 如上段代码， [] 里的都是依赖


#3.双向数据绑定
##表达式和 ng-bind
    首页加载用，这个不会显示出花括号
    <span ng-bind='value'></span>
    
    内页用这个，因为Angular已经下载好了
    {{value}}

##例子：表单-双向绑定
```javascript
var Module=angular.module('Module', []);
Module.controller('Ctrl', ['$scope', function($scope){
    $scope.userInfo={
        email: 'sbzk@gmail.com',
        text: 'this is text'
    }
    $scope.getData=function(){
        console.log($scope.userInfo);
    }
    $scope.setData=function(){
        $scope.userInfo={
            email: 'ttt@163.com',
            text: 'has been reset'
        };
        $scope.color='green'
    }
    
    $scope.color='red';
    
}]);
```

```html
<div ng-app='Module'>
    <form ng-controller='Ctrl'>
        <input type='email' ng-model='userInfo.email'></input>
        <input type='text' ng-model='userInfo.text'></input>
    </form>
    <button ng-click='getData()'></button>
    <button ng-click='setData()'></button>
    <p class='text-{{color}}'>css test</p>
</div>
```


##动态切换标签样式 ng-show 和 ng-hide
```html
<div ng-show='isShow'>
    ...
</div>
```

##ng-class
```html
<script>
    ...
    $scope.isError=false;
    $scope.isWarning=true;
    ...
</script>
<style>
    .error{}
    .warning()
</style>
<div ng-class='{error: isError, warning: isWarning}'></div>
```

##ngAnimate
- 要导入 anAnimate 这个模块

```html
<style>
    .class.ng-enter{}
</style>
```

#4.路由
##Ajax 问题
- 不能留下 History 记录
- 没有 url
-  SEO

##解决
- angular 本身提供的路由，不能嵌套
```html
<script source='./angular-route.js'/>
<script>
    module.config(function($routeProvider){
        $routeProvider.when('/hello', {
            templateUrl: 'tpls/hello.html',
            controller: 'HelloCtrl'
        }).when(
            templateUrl: 'tpls/list.html',
            controller: 'listCtrl'
        ).otherwise({
            redirectTo: '/hello'
        });
    })
</script>
```
- UI-Router 深层次嵌套路由
```html
<script src='angular-ui-router.js'/>
<script>    
</script>
<div ui-view></div>
```

- 原理
    - 哈希#
    - HTML5 的 history API   
    - 路由的核心是给应用定义状态
    - 路由机制会影响整体编码方式（需预先预定状态）
    - 兼容性和优雅降级
    
