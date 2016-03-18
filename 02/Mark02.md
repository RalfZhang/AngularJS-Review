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
    

#5.指令

```html
<hello></hello>
<script>
    var myModule=angular.module('MyModule', []);
    myModule.directive('hello', function(){
        return{
            restrict: 'E',
            template: '<div>Hi everyone!</div>',
            replace: true
        }
    })
</script>
```
##解析指令hello：匹配模式restrict
- A attribute（默认）
```html
<div hello></div>
```
- E element
```html
<hello></hello>
```
- M comment
```html
<!-- directive:hello -->
<div></div>
```
- C class
```html
<div class='hello'></div>
```

##解析指令hello：template、templateUrl、$templateCache
- template 后直接跟字符串的html
```javascript
template: '<div>Hi everyone!</div>'
```
- templateUrl 后跟 .html文件
```javascript
template: 'hello.html'
```
- $templateCache
```javascript
myModule.run(function($templateCache){
    $templateCache.put('hello.html','<div>content</div>')
});
myModule.directive('hello', function($templateCache){
    return{
        restrict:'AE',
        template: $templateCache.get('hello.html'),
        replace:true
    }
})
```


##解析指令hello：replace 和 transclude
- replace 定义标签后元素内嵌套的都将被替换
```html
<hello><div>不显示</div></hello>
```

- transclude 嵌套元素放入 ng-transclude 标签嵌套
```javascript
transclude: true,
template: '<div>hello!!<div ng-transclude></div></div>'
```

## comile 与 link（操作元素、添加 CSS、绑定事件）
- 加载（an-app）->编译（遍历找指令，调用方法转换DOM结构，调用compile）->链接阶段（调用link函数）

##指令与控制器之间的交互
```html
<div ng-app='MyModule'>
    <div ng-controller="MyCtrl1">
        <loader howToLoad='loadData1()'>Loading</loader>
    </div>
    <div ng-controller="MyCtrl2">
        <loader howToLoad='loadData2()'>Loading</loader>
    </div>
</div>
<script>
    var myModule=angular.module('MyModule', []);
    myModule.controller('MyCtrl1', ['$scope', function($scope){
        $scope.loadData1=function(){
            console.log('loading....1');
        }
    }]);
    myModule.controller(;'MyCtrl2', ['$scope', function($scope){
        $scope.loadData2=function(){;
            console.log('loading....2');
        }
    }]);
    myModule.directive('loader', function(){
        return {
            restrict:'AE',
            link: function(scope, element, attrs){
                element.bind('mouseenter', function(event){
                    //注意全小写
                    scope.$apply(attrs.howtoload);
                })
            }
        }
    });
</script>
```

##指令间的交互（指令3）
- 用内部 controller 暴露 public 的方法，给外部调用


## scope 的类型与独立 scope
- scope: {} 即可实现独立

## scope 的绑定策略（指令4）
- @ 字符串传递
- = 双向绑定
- & 传递父scope

## AngularJS 内置指令（指令5）


##实例解析 Explander（指令6）
##实例解析 Accordion（指令6）
##指令的源性原理：compile 和 link
##总结：ERP 类型的系统必备 UI 组件
重点
Form DatePicker FileUpload Tree DataGrid

##总结：互联网/电商系统必备 UI 组件
kissyui.com
ngnice.com

##第三方指令库的 angular-ui （指令6）
##Directive 思想起源与原理

#6.Service 与 Provider