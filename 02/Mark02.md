#MVC
- 通用的东西作为一个 Server
- 全都借助 $scope 实现
- 数据模型绑定在 $scope 下

##Controller
- 控制视图

##View
- 需要指令实现
- 变量向上查找，一直到根/找到

## $scope
- 一个 JS 对象
- 提供了一些方法 $watch()/$apply()
- 表达式的执行环境/作用域
- 树形结构，与 DOM 平行
- 根 $scope 只有一个，位于 ng-app 上
- 可以传播事件，向上或向下
- MVC 的基础，双向数据绑定的基础
- 可以用 angular.element($0).scope() 调试

#路由，模块，依赖注入

##路由
###ngRoute
- 根据地址栏不同处理不同链接

`
$routeProvider.when('/hello', {
    templateUrl: 'tpls/hello.html',
    controller: 'HelloCtrl'
}).when(
).otherwise({
    redirectTo: '/hello'
});
`


###模块
`
var helloModule=angular.module('HelloAngular', []);
//新建控制器
helloModule.controller('helloNgCtrl', ['$scope', function(#scope){
    $scope.greeting={
        text: 'Hello'
    };
}]);
`
- 推荐用总模块作为入口，挂上各个模块

`
var bookStoreApp=angular.module('bookStroeApp',[
    'ngRoute', 'ngAnimate', 'bookStoreCtrls', 'bookStoreFilters',
    'bookStoreServices', 'bookStoreDirectives'
]);
`

###依赖注入
- 如上段代码， [] 里的都是