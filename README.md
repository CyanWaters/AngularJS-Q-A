ANGULARJS_QUESTIONS
==

1. What is data binding in AngularJS?
2. What is scope in AngularJS?
3. What are the controllers in AngularJS?
4. What are the services in AngularJS?
5. What are the filters in AngularJS?
6. Explain directives in AngularJS.
7. Explain templates in AngularJS.
8. What is routing in AngularJS? (+) What is ng-resource ?
9. What is deep linking in AngularJS?
10. Explain AngularJS boot process.
11. Which components can be injected as a dependency in AngularJS?Explain Dependency Inject 
12. Explain ng-app directive.
13. Explain ng-model directive.
14. Explain ng-bind directive.
15. Explain ng-controller directive.
16. How AngularJS integrates with HTML?
17. Explain ng-init directive.
18. Explain ng-repeat directive.
19. What are AngularJS expressions?
20. Explain filter filter.
21. Explain orderby filter.
22. Explain ng-click directive.
23. How angular.module works?
24. How to validate data in AngularJS?
25. Explain ng-include directive.
26. How to make an ajax call using Angular JS?
27. What is use of $routeProvider in AngularJS?
28. What is $rootScope?
29. What is scope hierarchy in AngularJS?
30. What is a service?
31. What is service method?
32. What is factory method?
33. What are the differences between service and factory methods?
34. What is provider?
35. What is constant?
36. On which types of component can we create a custom directive?
37. What are the advantages of AngularJS?
38. What are the disadvantages of AngularJS?
39. Which are the core directives of AngularJS?
40. What is promises in javascript ?
41. What is a promises in angularJS ?
42. How to use resolve in angularJS routes ?
43. Explain the useful function when customing a directive.

----

# 1. What is data binding in AngularJS?

一個網頁的前端可以分成三部分 : View (使用者看見的) , template(html語法) , model (js控制器)。

data binding 指的是讓這三個部分互相締結溝通，彼此傳遞需要的值。

angular 的 data binding 是 two-way，但必須先了解以前的 one-way是什麼。

<img src="http://4.bp.blogspot.com/-WlILRvypHMk/U61cxXUXSxI/AAAAAAAAAaM/XY4uDcwnLNw/s1600/One_Way_Data_Binding.png">

過往在讀取網頁，會將 View , Template , Model 先結合，才產生 View。

這樣的壞處是，當我們藉由 model 修改了一些值之後，view 上面是無法立刻更新的。

<img src="http://3.bp.blogspot.com/--pSZ_ooBXA8/U61gySFikgI/AAAAAAAAAaY/UBofNpKRWzI/s1600/Two_Way_Data_Binding.png">

angularJS 的 two-way data binding，則是先編譯 template 展現 view，之後只要model有修改到的部分，都可以直接改變 view 的內容。

至於如何 two-way binding ? 是根據三個概念而起 :

* Dirty Checking - angularJS 內部比較某個value現在的值跟之前的值，如果有改變，就會觸發change事件。
* Digest - 由 `$digest()` 觸發 Dirty Checking 的機制。
* Apply - 當 DOM 在 angularJS 的控制範圍外被觸發，就需要通知 angularJS 進行 `$digest()`。由 `$apply()` 觸發。

### $digest()

像是 angular的心跳，每次跳動都會觸發所有 scope 的 dirty checking，而 dirty checking 又會觸發 `$watch()`，讓頁面隨之更新。

*通常不會直接使用 digest 而是使用 apply 來觸發，因為 apply 跟 digest 中間還有 $eval 把關 apply 的表達式是否合法*

### $watch()

* 由 `$digest()` 觸發。
* digest 執行的時候，如果 `watch` 觀察的value有變動，就會被觸發。
* 當我們每創建一個 model，angularJS 都會在後台為這個 model 創建一個 watch 去監聽他的變化。
* 手動使用的方式
	* 參數一 : 想觀察的 value
	* 參數二 : value 改變的時候想執行的動作
		`function(newValue,oldValue){}`
	* 參數三 : 默認是 false。決定怎麼比較之前的value跟後來的value。如果我們變動value(但其實改變後的value是一樣的)，`$watch()` 還是會觸發參數二的function。如果不想針對相同的內容執行 watch，把參數三設為 true，這個時候就會調用 angular.equals 去比較。

### $apply()

$scope.apply() 會觸發 digest，如果有帶一個 function 參數，則 function 會先被執行，而後才 digest。

當 model 的某個值被改變， angularJS 會自動觸發 `$scope.$apply()`，而這個 apply 又會調用 `$rootScope.$digest`，開啟一個 dirty checking。

當 DOM 在 angularJS 的機制外被觸發(如 jquery)，就需要使用。

### Refrence

[angularJS教學-Data Binding](http://programer-learn.blogspot.tw/2014/06/angularjs-data-binding.html)

[上手angular js : 主題 - data binding](http://code-beginner.logdown.com/posts/301921)

[Two-way Data Binding in docs of angularJS](https://docs.angularjs.org/tutorial/step_04)

[AngularJS系列(3):View-Model雙向綁定背後的小故事](http://hellobug.github.io/blog/angularjs-two-ways-binding/)

[angularjs $watch old value and new value are the same](http://stackoverflow.com/questions/20751954/angularjs-watch-old-value-and-new-value-are-the-same)

----

# 2. What is scope in AngularJS?

$scope 是 controller 跟 view 之間的一個作用域，用來保存 angularJS model裡面的所有對象，是model們的家。(如下圖)

<img src="http://4.bp.blogspot.com/-oSJGIijni5E/U6aLYALmyRI/AAAAAAAAAZU/d9uuTHqVUvY/s1600/ng-$scope.png">

它關聯了這個 controller 跟他所對應的 view 資料。

在這裡會創建一個 root scope，所以controller可以透過 $rootScope呼叫他。

	<html ng-app="mainApp">
	</html>

在下面的範例中 :

	<html ng-app="mainApp">
		<body ng-controller="MainCtrl">
			<div ng-controller="SubCtrl" ng-include src=" 'template.html' ">
			</div>
			<ul>
				<li ng-repeat="item in items">	{{item.name}}</li>
			</ul>
		</body>
	</html>

每個 html 只能有一個 root scope，但是 root scope 可以擁有很多個 child scope (如 ng-controller,ng-repeat，而 ng-repeat是對每個重複的元素都建了新的child scope)。


所以被包含的 子scope 是可以使用 父scope 上的所有 model 跟 function。

<img src="http://hellobug.github.io/my-images/angularjs-scope-1-1.png">

這邊比較奇怪的是，ng-include 與 ng-controller="SubCtrl" 雖然都在同一層 div 內，但由於程式的先後順序，ng-include 的 scopeB 會被包含在 SubCtrl 的 scopeA 裡面。

所以，在 scopeB 所宣告的 ng-model，在 scopeA 會呼叫不到。

資料繫結的對象，在 Controller 中以 $scope 物件形式出現，當我們寫<span ng-bind="someProp">時，通常就是指 `$scope.someProp` (使用 ng-repeat 迴圈時則為集合中的逐一物件)，Scope 可以視為 NG 中 ViewModel 角色。Scope 可以放屬性，也可以加入方法。

指的是一個 obj ，可參考其所 data binding 的 model。

是 expressions 的 context。

模仿 DOM 架構的階層設計，可以監聽(watch) expressions 和其他的事件。

scope 提供了 API(`$watch`)可以觀察所有model的改變。 

API (`$apply`)可以把 controllers , services 或 angular event handlers 所做的 model 改變設定到 view 上。

### Refrence

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[scopes in docs of angularJS](https://docs.angularjs.org/guide/scope)

[AngularJS系列(2):Scope什麼的~](http://hellobug.github.io/blog/angularjs-scope/)

[AngularJS 教學 - Controller and View](http://programer-learn.blogspot.tw/2014/06/angularjs-controller-and-view.html)

----

# 3. What are the controllers in AngularJS?

就是MVC中的C，扮演Model 與 View 之間的連結角色，提供資料以及服務給 View，並且處理一些 user action。

HTML 中的 element (如: body , div) 若用 ng-controller 標明負責該 element 的 Controller，則該區塊內便可以使用這個 controller 的方法跟成員。

Html:

	<ANY ng-controller="{expression}">
   		...
	</ANY>

js:

	function MyController($scope) {
    	// Create a model in this scope
    	$scope.MyModel = ....;
    	// Create a function in this scope
    	$scope.MyFunction = function(parameter) {
        ....
    	}
	}

Controller 中的 $scope 代表該 Controller 只能在這個區塊作用。Controller 裡面的 model 跟 function 必須用 `$scope.{model/function}` 宣告，才可以讓 model 跟function 在這個區塊被識別。

當 html 裡面有定義 ng-model 時，controller 可以透過 $scope.{modelName} 來使用這個 model。若 Controller 對這個 model 宣告且賦予一個值，則 html 上的 model 就會有初始值。

一個網頁裡可以有多個 Controller 負責不同元素的 Binding。而兩個 Controller 若有父子的關係，其 Model 預設會出現繼承關係。

Controller 之間可以互相溝通，需要依賴 $rootScope 來達成。


### Refrence

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[AngularJS - 簡單應用01 ( ng-model, ng-controller, ng-click)](http://limitedcode.blogspot.tw/2013/11/angularjs-ng-model-ng-controller-ng.html)

[AngularJS 教學 - Controller 進階(1)](http://programer-learn.blogspot.tw/2014/06/angularjs-controller-1.html)

[AngularJS 教學 - Controller 進階(2):關於attribute的inherit的陷阱](http://programer-learn.blogspot.tw/2014/06/angularjs-controller-2.html)

----

# 4. What are the services in AngularJS?

Angular Services 是可以被置換的 objects，且彼此之間會藉由 dependency injection (DI) 互相牽連。也因此，在 app 中可以使用這些共享的 services。

一個 service 只有在一個 application component 需要時才會被創建成一個 object。

**Singletons** : 每一個 component 會使用同一個 service object，而這個 service object 是被 service factory 所創建。

service factory 會產生一個單獨的 object 或 function，擁有這個 service 的特性。 回傳的這個 ojbect 或 function 可以被注入任何需要這個 service (也就是它們依賴它)的 components ( controller , service , filter 或 directive )。

典型的做法是利用 Module factory API 來註冊一個 service :

	var myModule = angular.module('myModule', []);
	myModule.factory('serviceId', function() {
  		var shinyNewServiceInstance;
  		// factory function body that constructs shinyNewServiceInstance
  		return shinyNewServiceInstance;
	});

所以，並不是你註冊一個 service instance，而是當你需要的時候，factory function 才創建這個 instance。

當然，你也可以藉由 `$provide` 而不是 service service factory function 來註冊一個 service:

	angular.module('myModule', []).config(['$provide', function($provide) {
  		$provide.factory('serviceId', function() {
    		var shinyNewServiceInstance;
    		// factory function body that constructs shinyNewServiceInstance
    		return shinyNewServiceInstance;
  		});
	}]);

### Refrence

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[services in docs of angularJS](https://docs.angularjs.org/guide/services)

----

# 5. What are the filters in AngularJS?

可以將陣列中的元素進行過濾篩選，並回傳一個新的結果陣列。應用於 Directive ，對繫結的資料進行後置處理，NG允許開發者自訂 Filter ，讓各式資料轉換、處理邏輯能很容易被加入Binding過程。

**case1 : 如果只是單純在HTML轉化的話...**

[index.html]

	<div class="span6" ng-controller="FilterCtrl">
		<div class="alert alert-info">
			<input type="number" ng-model="num">
			<pre>{{ num | number }}</pre>
		</div>
	</div>

*number 這個 filter可以把字串轉為數字，數字格式化為三位一撇，且限制小數以下的位數為3*

[js/controller.js]

	var FilterCtrl = function($scope){
 		$scope.num = 1234.56789;
	};

**case 2 : 如果想要在 Controller 中先轉換再使用的話...**

建立 Controller 時，除了傳入 $scope 之外，得再多傳入 `$filter` 才行

	var FilterCtrl = function($scope, $filter){
		$scope.num = 1234.56789123456;
		console.log($filter('number')($scope.num, 5));
	};

**case3 : 如何自訂一個過濾器?**

(在這邊建立一個module=MyApp,controller=Ctrl)

並呼叫 .filter(name,filterFactory) 來定義過濾器

JS

	var myApp = angular.module('MyApp', []);
 
	myApp.controller('Ctrl', function($scope){
		$scope.text = 'Hello Kitty';
	});

	myApp.filter('clean', function(){
			return function(str){
			return str.toLowerCase().replace(/\s+/g, '-');
		};
	});

index.html

	<!doctype html>
	<html ng-app="MyApp">
	<head>
		<meta charset="UTF-8">
		<title>男丁格爾's 脫殼玩 - AngularJS</title>
		<link rel="stylesheet" href="http://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/2.3.2/css/bootstrap.min.css">
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.0-rc.2/angular.min.js"></script>
		<script src="js/controller.js"></script>
	</head>
	<body>
		<div class="well span4" style="margin-top:20px;" ng-controller="Ctrl">
		{{ text }}
		</div>
	</body>
	</html>



### Refrence

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[[AngularJS]AngularJS 入門教學 - Filters (二)](http://abgne.tw/angularjs/angularjs-getting-stared/filters-2.html)

[[AngularJS]AngularJS 入門教學 - 自訂過濾器](http://abgne.tw/angularjs/angularjs-getting-stared/create-a-custom-filter.html)

[[AngularJS]AngularJS 入門教學 - Filters (一)](http://abgne.tw/angularjs/angularjs-getting-stared/filters.html)

----

# 6. Explain directives in AngularJS.

Directives 這個功能是利用 HTML 的結構與特性所延伸出來的、專屬於 angularJS 的功能，串連 HTML 元素及 ViewModel。

我們對一個在 DOM 元素(ex: attribute , element name , comment 或 css class)上做一個 directive 標記。這個標記讓 angularJS 的 HTML compiler (`$compile`) 可以辨識，並將特定的行為附加在它上面，也可改變 DOM 元素和他的子元素。

如 : ng-controller, ng-model, ng-show 都是 angularJS 提供的 Directive。

在HTML裡面命名的 element 的 tag/attribue ，如果想跟 js 內定義 directive 互相 matched 的話，該 tag/attribute 正規化後的名稱必須與 js 內的directive 一致:

* 把 `x-` 和 `data-` 從 element/attribue 前面移除
* 利用 camelCase 分隔 `:` , `-` 或 `_` 。

		<div ng-controller="Controller">
			Hello <input ng-model='name'> <hr/>
			<span ng-bind="name"></span> <br/>
			<span ng:bind="name"></span> <br/>
			<span ng_bind="name"></span> <br/>
			<span data-ng-bind="name"></span> <br/>
			<span x-ng-bind="name"></span> <br/>
		</div>
		// 以上幾種寫法都可以跟 js 內的 directive('ngBind',...) matched

基本上，只要你創建 compilers 跟 services，你就可以自創 directives 為 angularJS 所使用。

註冊 directive 必須藉由 `module.directive` API，而這個 API 會藉由 **factory function** [見32.] 取得正規化後的 directive name。factory function 會回傳一個 obj，內含不同的選項，以告訴 `$compile` 當這個 directive matched後，會有什麼行為。

*factory function 只有 directive 第一次被 match 時才會invoke。*

[自己寫一個directive，讓固定的html段落可以用簡單的方式注入](http://hellobug.github.io/blog/angularjs-directive-1-basic-introduction/) *在docs也有描述*

[自己寫一個directive，輸入的用戶名後，可以去後台請求查詢該用戶是否已經存在](http://hellobug.github.io/blog/angularjs-directive-2-compile-vs-link/)

*docs 中還有一些創建 directives 的範例可以參考*

directive 跟 directive 之間也可以有 binding。在 `$compile` 做 compile 並連接 html 內某 element 的 tag/attribute 後，它會試著 match 這個 element 內子元素的 directives。所以 directives 可以跟其他的 directives 組合。

### Directive 如何在 angularJS 的手下生效 :

1. 標準瀏覽器 API 轉化 :
把 html 轉化成 dom ，所以自定義的 html 標籤要符合 html 的格式

2. Angular compile :
搜索符合的 directive，按照 `Priority` 排序，並執行 directive上的 `compile` 方法 (把 directives 附加到 HTML 使他啟用)。

		function compile(element, attrs, transclude) { ... }

	在compile階段執行的函數，所返回的function就是link要執行的function。

3. Angular link :
執行 directive 上的 link 方法，進行 scope 的綁定、事件綁定。

		function link(scope, element, attrs, controller) { ... }

	在link階段要執行的函數，只有compile屬性沒有設置才會生效。

### Refrence

[Directives in docs of angularJS](https://docs.angularjs.org/guide/directive)

[上手angular js : 主題 - data binding](http://code-beginner.logdown.com/posts/301921)

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[[AngularJS系列(5-2)] Directive - Compile vs. Link](http://hellobug.github.io/blog/angularjs-directive-2-compile-vs-link/)

----

# 7. Explain templates in AngularJS.

Template (樣版) 功能主要用於同一組輸出樣式，需反覆套用多次的情境。

AngularJS 的 Templates 則是包括了 AngularJS 特殊的屬性(ex: ng-model)或元素(Directives)。

以 ng-repeat 為例，在 `<tr>` 與 `</tr>` 間以 HTML 穿插 `ng-* Directive` 或 `{{ prop }}` 方式定義呈現樣式，即相當於內嵌 template 。

angularJS 也提供了 ng-include，可將 template 提取成獨立片段加以共用。

*還有 ng-switch ，在此不介紹*

### Refrence

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[NG筆記17-範本(Template)](http://blog.darkthread.net/post-2014-10-01-angular-notes-17-template.aspx)

[AngularJS 教學 - Templates](http://programer-learn.blogspot.tw/2014/06/angularjs-templates.html)

----

# 8. What is routing in AngularJS?

$route 的功能讓我們可以透過 routing 來切換不同的 view 。不過有一點要注意的是，AngularJS 中的 route 其實都是同一頁（SPA），所以在url的參數上會是帶在 # 之後。

*routing 的功能需要引用 `ngRoute` 這個 module，本身 angularJS 的 core framework 是不包含的。*

	index.html#/
	index.html#/edit
	index.html#/profile

routes 的應用需要藉由 `$routeProvider` 來宣告 ( 他是 `$route service` 的 provider)。藉此我們才能完成 deep linking，使用瀏覽器的瀏覽歷史(上一頁下一頁)、還有書籤。

以下面為例 :

### 靜態轉址(單純設定轉址,不帶變數)

如果我們想要完成這樣的一個網頁目錄...

<img src="http://user-image.logdown.io/user/6631/blog/6602/post/334029/g3b7ZfcYT1m6K87bHT40_%E7%9B%AE%E9%8C%84.png">

所以我們需要三個路徑:

1. 「首頁」檔案的路徑：index.html
2. 「關於我們」檔案的路徑：about.html
3. 「產品」檔案路徑：product.html

index.html

	<!DOCTYPE html>
		<html ng-app="routeModule">
  		<head>
    		<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.5.0-beta.2/angular.min.js"></script>
    		<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.5.0-beta.2/angular-route.js"></script>
				// 這裡需要加入routeJS的引用,因為本身angularJS的coreJS沒有包含
    		<script src="route.js"></script>    
    		<script type="text/javascript">
    			angular.element(document.getElementsByTagName('head')).append(angular.element('<base href="' + window.location.pathname + '" />'));
  			</script>
  		</head>
  		<body>
    			<a href="index">首頁</a>
    			&nbsp;&nbsp;
    			|
    			&nbsp;&nbsp;
    			<a href="about">關於我們</a>
    			&nbsp;&nbsp;
    			|
    			&nbsp;&nbsp;
    			<a href="product">產品</a>
    			<hr />
    			<div ng-view></div>    
  		</body>
	</html>

**注意需要引入 angular-route.js，才可以使用 routing 的服務。**

**ng-view** 所標示的 div 就是新頁面的載入地方。

在設計其他頁面時，不用設計完整的頁面，因為 angularJS 是在 ng-view 中標示的區域直接載入。

接下來我們需要用 `module.config()`，把 **$routeProvider** 注入到 config 裡面。

route.js

	routeModule.config(['$routeProvider', function($routeProvider)
  	{
    	$routeProvider
      	.when('/index',
      	{
        	templateUrl: 'welcome.html'
      	})
      	.when('/about',
      	{
        	templateUrl: 'about.html'
      	})
      	.when('/product',
      	{
        	templateUrl: 'product.html'
      	})
      	.otherwise(
      	{
        	templateUrl: 'welcome.html'
      	}) ;
  	}]);

.when() 的第一個參數是客製化的 URL，第二個參數則是一個物件，稱為 route。

route 的物件中有一個 templateUrl 的屬性，代表 html 檔案的路徑。

otherwise 是 default (通常放首頁或404頁面)。

除了 $rouePorvider，還有另外一個滿重要的 **$loactionProvider**。

$loactionProvider 提供了兩種對 URL 的設定 : hashPrefix 與 html5Mode。

* hashPrefix:

	是 angularJS 對 url 的預設設定。也可以藉由呼叫 `$loactionProvider.hashPrefix()`來設定。

	所有的 url 在 domain name 之後都要帶 #，例如 `domain/#/index`。

	小應用 - *`$locationProvider.hashPrefix('!')`: URL 必須是 `domain/#!/index` 才會連到正確的頁面。*

	**儘管 url 有#，但是在 .when() 的時候是不需要加入的**

* html5Mode :

	html5 是常規的 URL ，不會有任何的 prefix。對這個函數可以傳入一個 boolean 值，true 代表啟用，false 代表不啟用。

以下的範例 route.js 將改成啟用 html5Mode 的版本 :

route.js

	routeModule.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider)
  	{
    	$routeProvider
      	.when('/index',
      	{
        	templateUrl: 'welcome.html'
      	})
      	.when('/about',
      	{
        	templateUrl: 'about.html'
      	})
      	.when('/product',
      	{
        	templateUrl: 'product.html'
      	})
      	.otherwise(
      	{
        	templateUrl: 'welcome.html'
      	}) ;

		$locationProvider.html5Mode(true); // 用常規的url顯示
  	}]);

### 動態轉址(帶變數進URL使用)

如果我們想要在不同的網址中，動態呈現不同產品的資料。會需要使用 **$routeParams**。

先建立一個產品的陣列:

	var product =
	[{id: 1, name: '產品1', price: 1000}, 
     {id: 2, name: '產品2', price: 1200}] ;

如果想要在產品頁面陳列產品的資訊，我們需要 controller 來操作資料。

這裡使用兩個 controller

1. productController : 列出所有產品的名稱
2. productContentController	: 列出該產品的價錢

我們預設在 **/product_content** 會列出所有產品的名稱，而 **product_content/:id** 會列出該產品的價錢。

	//簡化架構
	routeModule.config( ... ).controller( ... );
	//其中
	config( [ ....... ] )
	config( [ '___','___',function(___){...} ] )
	controller( '___','___',function(___){...} )

	//稍微複雜一點
	routeModule
	.config(['$routeProvider', '$locationProvider',
		function($routeProvider, $locationProvider)
		{
			$routeProvider.when( ... ).otherwise( ... );
			$locationProvider.html5Mode(true) ;
		}
	])
	.controller('productController',function($scope)
		{
			...
		}
	)
	.controller('productContentController',
		['$scope', '$routeParams',function($scope, $routeParams)
			{
				...
			}
		]
	) ;

完整的見下 :

route.js

	var routeModule = angular.module('routeModule', ['ngRoute']) ;

	routeModule.config(['$routeProvider', '$locationProvider', function($routeProvider, $locationProvider)
  	{
    	$routeProvider
      		.when('/index',
      		{
        		templateUrl: 'welcome.html'
      		})
      		.when('/about',
      		{
        		templateUrl: 'about.html'
      		})
      		.when('/product',
      		{
        		templateUrl: 'product.html',
        		controller:  'productController'
      		})
      		.when('/product_content/:id',
      		{
        		templateUrl: 'product_content.html',
        		controller:  'productContentController'
      		})
      		.otherwise(
      		{
        		templateUrl: 'welcome.html'
      		}) ;
      		$locationProvider.html5Mode(true) ;
	}])
	.controller('productController', function($scope)
	{
		//顯示產品清單
		$scope.product = product ;
	})
	.controller('productContentController', ['$scope', '$routeParams', function($scope, $routeParams)
	{
    	//利用$routeParams.id找出符合ID的產品，並傳進$scope顯示在View上
    	var id = $routeParams.id ;
    	for (var i=0; i<product.length; i++)
    	{
      		if (product[i].id == id)
      		{
        		$scope.name  = product[i].name ;
        		$scope.price = product[i].price ;
        		break ;
      		}
    	}
	}]);

**productController** 把產品清單傳入 $scope，讓 view 可以直接使用。

product.html

	<ul ng-repeat="p in product">
  		<li>
			<a href="product_content/{{p.id}}"><h4>{{p.name}}</h4></a>
		</li>
	</ul>

**productContentController** 注入了 `$routeParams`。

`$routeParams` 是一個物件，裡面會有一個 id 的屬性，儲存先前透過 URL 傳遞的參數:id。如果要存取就使用 `$routeParams.id`。

productContentController 則會利用 `$routeParams.id` 在 product 陣列中找到符合的商品，在 view 中秀出產品資訊。

	<h1>{{name}}</h1>
	<h3>{{price|currency:'NT$':0}}</h3>


以上關於產品頁面的範例，詳見 :
[上手AngularJS - 主題：Routing](http://code-beginner.logdown.com/posts/334029-start-angularjs-theme-routing)

--分隔線--

index.html (使用 BallApp 這個 module)

	<!DOCTYPE HTML>
	<html ng-app="BallApp">
    	<head>
        	<meta charset="utf-8">
        	<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.17/angular.min.js"></script>
        	<!-- 使用 $route module 必須再引入 angulare-route.js -->
        	<script src="https://code.angularjs.org/1.2.17/angular-	route.js"></script>
        	<script src="module.js"></script>
    	</head>
    	<body>
        	<h2>View Render</h2>
        	<!-- 使用 ng-view 屬性決定 view 載入的位置 -->
        	<div ng-view></div>
    	</body>
	</html>

module.js

	// 要使用$route時必須帶入ngRoute module 及 route 設定
	var ballApp = angular.module('BallApp', ['ngRoute'],function($routeProvider) {
    	// 指定 route 及載入的 view
    	$routeProvider.when('/', {
        	templateUrl: 'view.html'
    	})
    	.when('/edit', {
        	templateUrl: 'edit.html'
    	})
    	// 不符合以上規則的利用 otherwise 統一導到固定的view
    	.otherwise({
        	redirectTo: '/'
    	});
	});
 
	// 新增 Controller
	ballApp.controller('NameCtrl', function($scope) {
    	$scope.persons = ['Johnson', 'Febr', 'Minnine'];
	});

view.html

	<ul>
    	<li ng-repeat="name in persons">{{name}}</li>
	</ul>

$route 帶入參數

	var ballApp = angular.module('BallApp', ['ngRoute'],function($routeProvider) {
    	$routeProvider.when('/', {
        	templateUrl: 'view.html'
    	})
    	// 使用冒號決定參數名稱
    	.when('/hello/:message', {
        	templateUrl: 'hello.html',
        	// 設定 view 的 controller
        	controller: 'HelloCtrl'
    	})
    	.otherwise({
        	redirectTo: '/'
    	});
	});
 
	// 透過 $routeParams 帶入參數
	ballApp.controller('HelloCtrl', function($scope, $routeParams) {
    	$scope.message = $routeParams.message;
	});

hello.html

	<div>Hello, {{message}}</div>

URL

	index.html#/hello/baby

實際應用(點擊姓名後進入修改畫面)

module.js


	var ballApp = angular.module('BallApp', ['ngRoute'],function($routeProvider) {
    	// Route
    	$routeProvider.when('/', {
        	templateUrl: 'view.html'
    	})
    	.when('/edit/:index', {
        	templateUrl: 'edit.html',
        	controller: 'EditCtrl'
    	})
    	.otherwise({
        	redirectTo: '/'
    	});
	});
 
	// Controller
	ballApp.controller('NameCtrl', function($scope) {
    	$scope.persons = ['Johnson', 'Febr', 'Minnine'];
	}).controller('EditCtrl', function($scope, $routeParams) {
    	$scope.index = $routeParams.index - 1;
	});

view.html

	<ul ng-controller="NameCtrl">
    	<li ng-repeat="name in persons">
        	<a ng-href="#/edit/{{$index + 1}}">{{name}}</a>
    	</li>
	</ul>

edit.html

	<!-- 因為需要使用到persons陣列,因此引用NameCtrl -->
	<form ng-controller="NameCtrl">
    	<input ng-model="persons[index]">
	</form>

### Refrence

[[AngularJS]route 與 view](http://blog.johnsonlu.org/angularjs-route-%E8%88%87-view/)

[routing & multiple views in docs of angularJS](https://docs.angularjs.org/tutorial/step_07)

[上手AngularJS - 主題：Routing](http://code-beginner.logdown.com/posts/334029-start-angularjs-theme-routing)

----

# 8+. What is ng-resource ?

ng-resource 讓使用者可以對後台產生 restful request。

restful api 在 http 的實證中，使用的是

+ POST : 新增
+ PUT : 修改
+ GET : 查詢
+ DELETE : 刪除

而 $resource 本身有五種方法可以使用

+ get()
+ query()
+ save
+ delete
+ remove()


// remove方法和delete()方法的作用是完全相同的，它存在的意义是因为delete是JavaScript的保留字，在IE浏览器中会导致额外的问题。

(標註見 : [AngularJS中使用$resource](http://blog.csdn.net/u010834071/article/details/46360377))

--分隔線--

大部分的頁面都需要包含 CRUD 處理 (create,read,update,delete) 。

而要利用 angularJS 實踐 CRUD，就必須借用 `$resource` 這個 service。

anglarJS 的 `$resource` 建立在 `$http` 服務上，他是一個 factory service，讓我們能夠跟 RESTful 後端溝通。

下面將用步驟示範如何使用 `$resource`。

1. **先決條件**

	`$resource` service 並沒有跟主要的 angularJS 綁再一起，所以要格外下載單獨的檔案 ( `angular-resource.js` )，並將其包入所需的 HTML 頁面。

	除此之外，主要的 app module 也需要加入 `ngResource` 這個 module，才可以使用 `$resource`。

		angular.module('mainApp',['ngResource']);
		//mainApp is our main module

2. **後端的設置**

	`$resource` 需要你有一個 RESTful 的後端配置，且符合下面的規則 :

	<img src="http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/06/1402203305REST_API__1.png">

	這個部分可以用 server 來創建。

3. **$resource 怎麼運作**

	要在 controller/service 中使用 `$resource`，還需要為 `$resource` 宣告一個 dependency。

	下面的例子為，如何使用 `$resource` 在你的 REST 後端。

	這個 function 會回傳一個 `$resource` class 的 object，而他可以被用來跟 REST 後端溝通。

		angular.module('myApp.services').factory('Entry', function($resource) {
  			return $resource('/api/entries/:id');
			// Note the full endpoint address
		});

	回傳的結果是 resource class 的 object，而他擁有下面五種方法可使用:

	1. `get()` : 只要一個
	2. `query()` : 全部都要
	3. `save()` : 儲存
	4. `remove()`
	5. `delete()`

	以下範例會在一個 controller 使用 `get()` , `query()` , `save()` :

	*以下的例子中，resource class指的就是 Entry*

		angular.module('myApp.controllers',[]);

		angular.module('myApp.controllers').controller('ResourceController',function($scope, Entry) {

			// get() 會回傳一個單獨的 entry
  			var entry = Entry.get({ id: $scope.id }, function() {
    			console.log(entry);
  			});

			// query() 會回傳全部的 entries
  			var entries = Entry.query(function() {
    			console.log(entries);
  			});

			// save() 會儲存一個 entry
			//$scope.entry 必須是一個 Entry object
  			$scope.entry = new Entry(); //建立一個新的 entry
  			$scope.entry.data = 'some data';
  			Entry.save($scope.entry, function() {
    			//data saved. do something here.
  			});
		});

	1. 上面的 `get()` function 會拿 `/api/entries/:id` 去發一個 GET request，URL 中的 `:id` 會被 `$scope.id` 取代。

		`get()` function 在 data 從 servers 回傳回來的時候，會自動產生一個空的 object 以供存取。

		第二個 argument 則是一個 callback，會等到 data 抵達本端才啟用。

		所以也可以對 $scope 設一個空的 object，讓他跟 view 對應。如果藉由他存取 `get()` 回傳的 Object ，基於 data-binding 的關係，view 的 $scope.object 也會馬上被更新。

	2. `query()` 對後端發一個 `/api/entries` 的 GET request，並產生一個空的 array。當 data 從 server 端回傳，該 array 就會存取 data。

		一樣的，你可以在 `$scope` 設一個 array，利用 `ng-repeat` 跟 view 做 data binding。

		也可以提交一個 callback，當 data 從 server 送達的時候，就會被啟動。

	3. `save()` 會拿 `/api/entries` 去發一個 POST request，第一個 argument 就是 post 的 message body。

		第二個 argument 是一個 callback，當data 被儲存的時候就會啟用。

		剛剛提過，既然呼叫 `$resource` function 所回傳的 value 會是一個 resource class 的 object，我們可以藉由呼叫一個 `new Entry` 來建立一個新的 object，對其設定不同的 properties，再將這個 object 存入 backend。

	上面提到的方法中，通常只會對 resource class (意即 `Entry`) 使用 `get()`、`query()`。

	其他非 GET 的方法，如 `save()`、`delete` 則可以藉由呼叫一個 `$resource` instance (意即 `new Entry()` ) 來使用。

	最大的差別在於，這些方法都還要加一個 prefix `$`。所以他們才可以對 `$resource` instance 使用。這些方法有...

	1. $save()
	2. $delete()
	3. $remove()

	舉例來說，`$save`就可以這麼用 :

		$scope.entry = new Entry();
		//這個object有一個 $save() method
		$scope.entry.$save(function() {
  			//data saved. $scope.entry 作為 post 的 message body 被送出
		});

	目前已經有了 create, read, delete 的部分。剩下 update。

	如果要支援 update，剛剛所用的、我們自製的 `Entity` 必須要再做修改 :

		//原本的
		angular.module('myApp.services').factory('Entry', function($resource) {
  			return $resource('/api/entries/:id');
		});

		//後來的
		angular.module('myApp.services').factory('Entry', function($resource) {
  			return $resource('/api/entries/:id', { id: '@_id' }, {
    			update: {
      			method: 'PUT' // this method issues a PUT request
    			}
  			});
		});

	可以發現，`$resource` 裡面除了第一個argument(後端的設置)，還多了第二、三個 argument。

	第二個 argument

		{ id: '@_id' }
	
	對 `$resource` 而言是一個 hash，儲存 URL 中的 `:id`。所以當我們對 resource instance 使用像是 `$update()` 或 `$delete()`，`:id` 會被存進這個 resource instance 的 `_id` property。

	這對 PUT 跟 DELETE 的 request 是非常有用的。

	第三個 argument

		{
			update: {method: 'PUT'}
			// 這個方法可以發出 PUT request
		}

	這個 hash 允許我們加任何客製化的 method 進 resource class。

	當這個 method 處理的是 non-GET request，他可以藉由加入 prefix `$` 在 `$resource` instance 使用。

	看看我們該怎麼使用這個 `$update` 方法。假設我們在controller寫下...

		/*
		原本的 get function，拿來跟下面比較而已
  		var entry = Entry.get({ id: $scope.id }, function() {
    			console.log(entry);
  		});
		*/

		//後來的 get function 與 update 搭配
		$scope.entry = Entry.get({ id: $scope.id }, function() {
  			// $scope.entry 是一個 instance ，會從 server 取得
  			$scope.entry.data = 'something else';
  			$scope.entry.$update(function() {
    			//後端更新
  			});
		});

	當 `$update()` 被呼叫，他會做以下的事情 :

	1. angularJS 知道 `$update()` 會用 URL `/api/entries/:id` 觸發一個 PUT request

	2. 他會去讀 `$scope.entry._id`，將這個值指派給 `:id` 並產生 URL。

	3. 對這個 URL 去送一個 PUT request，而 `$scope.entry` 本身會是 POST 的 message body

	相對的，你也可以用下面的方式刪除一個 entry :

		$scope.entry = Entry.get({ id: $scope.id }, function() {
			// $scope.entry 從 server 取得，是一個 instance
  			$scope.entry.data = 'something else';
  			$scope.entry.$delete(function() {
    			//成功刪除!
  			});
		});

	其實他跟 update 有點像，差別是 request 的 type 不是 PUT 而是 DELETE。

	其實 $resource 還有第四個參數 : `stripTrailingSlashes`。預設是true，代表他會把 trailing slashes 從你要給 $resource 的 URL 中移除。

	[Does it make a difference if your URL path ends in a trailing slash or not?](http://webmasters.stackexchange.com/questions/2498/does-it-make-a-difference-if-your-url-path-ends-in-a-trailing-slash-or-not)

	[什么是trailing slashes？？？](http://bbs.csdn.net/topics/90492623)

### Reference

[了解AngularJS $resource](http://www.cnblogs.com/irocker/p/understanding-angularjs-resource.html)

重要 : [Creating a CRUD App in Minutes with Angular's $resource](http://www.sitepoint.com/creating-crud-app-minutes-angulars-resource/)

----

# 9. What is deep linking in AngularJS?

為了那些不使用 html 的 http 來協助轉址服務的、對 URL 定位的一種描述。

舉例來說，一個網頁中如果有 "Home" , "About US" , "Contact" 的三個按鈕。你點擊它的時候，會定位到對應的 URL (ex: http://www.jack.com/AboutUs)。

這樣的好處是，可以把我想要使用的頁面直接用網址傳遞，不需要再一一點入。

如果在 Flash 的應用中，要不使用 Deep linking，就無法未指定的狀態添加書籤，也就沒有對應的 URL 可以使用。

所以 deep linking 是為了這個問題而存在，通常會透過 php 或 js 來實現。

angularJS 的 ng-route module 為 controller 與 view 提供了 deep linking 的 URL。 ng-route module 的 $routeService 監控 $loaction.url() 的變化，並將他對應到原先定義好的 controller。所以我們會在 client 端進行 URL 的 routing。 


### Refrence

[所谓深度链接（Deep linking）](http://www.cnblogs.com/skywind/archive/2013/05/15/3079229.html)

[Nested Views, Routing, And Deep Linking With AngularJS](http://www.bennadel.com/blog/2441-nested-views-routing-and-deep-linking-with-angularjs.htm)

[AngularJS 路由：ng-route 与 ui-router](http://harttle.com/2015/06/10/angular-route.html)

----

# 10. Explain AngularJS boot process.

當一個頁面開始載入，會有以下的事情發生 :

1. HTML 的 document 會被載入到瀏覽器。angularJS 的 js 檔案被載入，相關的 global object 開始被創造。接下來，javascript 中關於定義 controller 跟 function 的部分會開始執行。
2. 接著，angularJS 掃過 HTML 來尋找裡面的 angularJS apps 跟 views。當 view 被找到，它就會跟對應 controller function 中的 view 去對應。
3. angularJS 執行 controller function。他會從 controller 產生的 model 中傳遞 data 給 views。 一個介面才正式完成。 

### Reference

[Logic Guns : Explain AngularJS boot process.](http://www.logicguns.com/q/explain-angularjs-boot-process/564c5df02b78724573b3ca4f/)

----

# 11. Which components can be injected as a dependency in AngularJS? Explain Dependency Inject

Denpendency 是你達到某個目的的必要條件。

通常一個對象有三種方法，可以獲得對 Dependency 的控制權 :

1. 在內部創建 dependency : 但有些 dependency 會在不同的 controller 中使用，如果要在每個 controller 裡自己控管該 dependency ，會讓程式重複程式碼變多，也增加了測試時的複雜度。
2. 通過 global 來進行引用 : 但是會汙染 global，當 code 比較厚重的時候容易出問題。
3. 在需要的時候才藉由參數傳遞呼叫 dependency : 對測試很有用，也不會汙染 global。angularJS 使用的就是這種方法，提供了 DI engine 來替 App 控管這些功能和服務。

angularJS 可以將 app 分成很多個可重複使用的 dependency ，當需要這些零件的時候，就透過 DI 把這些 dependency 注入到 app 裡面。

例如說，$scope 怎麼來的，其實是 AngularJS 的 DI 所給予的。

angularJS 用了很多 DI 概念，所以會常看到不知道從哪冒出來的 $scope 、 $http 、 $sce 等變數。實際上在執行期間，angularJS 會根據預先註冊的 DI 設定將 $scope 、 $http 指向對應的服務。這麼做有兩個好處:

* Controller 不會綁死 dependency 的內容，必要時可偷偷換掉或調整，且 Controller 完全不用改(甚至不會察覺)。
* 第二項好處是做自動測試時，可換上測試用的版本，以快速模擬特定情境，單獨測試指定的 Controller 或 Service。

AngularJS DI system 的整理如下：

1. 為 AngularJS App 提供了一個抽象層，透過 DI engine 來控管服務的生命週期。
2. 所有服務都需要註冊 （Registering Service）。
3. 向 DI system 註冊服務有3種「方式」：Service, Factory, Provider。
4. 在 DI system 情境下提到 Service，就是一般在說的「服務」，其中包括service，factory，provider 等3種。
5. 所有向 DI 註冊的 Service 都是 Singleton !

### [關於service,factory,provider的詳細整理]

**DI engine 的服務都是 singleton**，也就是 AngularJS 只有在 App 一開始時 會初始化向 DI 註冊服務，之後調用服務的動作，都只是「取得」服務，不會再有 new 的動作。
以下有三種方法可以建立 服務:

**service:**
  
透過 service 所註冊的服務，在 controller 取得實體時，拿到的實體就是該傳進去的 function。DI engine 透過所提供的 function 「new」 出該 function 的實體，該 function 相當於是 JS constructor 的功能。

使用頻率：較少
使用時機：當在外部已經寫好了某個服務的 constructor，可透過 service 直接用該 constructor 向 DI system 註冊服務。

**factory:**

factory 註冊方式相較 service 註冊方式提供較高的彈性，因為在 function 裡面多了一個 return 區域。在一開始產生該服務實體時，factory 可以有先行的預設值和邏輯運算，而 return 區域則是在 controller 調用該 service 時，拿到的實體物件。因此大部分自製的服務要向 DI 註冊時，透過 factory 註冊比較適合。

使用頻率：較多
使用時機：自行製作該 「App」的服務時。

**provider:**

provider 提供彈性最大的方式讓 user 向 DI system 註冊服務。provider 裡面有兩個結構，一個是 return，另一個是 $get。

return 提供 provider 本身的 function。也就是提供更廣泛的客製化。User 可改變 provider 的設定，但是必須要在 AngularJS 呼叫此服務的 $get function 前。通常是透過可在 App 的 config 做設定。

而 $get function 可以視做和 factory 完全相同的功能，在$get 中的 return 才是 controller 在調用該服務時，取得的實體物件。

使用頻率：普通
使用時機：自行製作該 「App」的服務，且希望該服務能夠客製化，在注入前做一些設定和調整。

最後補充一點，DI system 所提供的服務，可以在 AngularJS app 中任何地方調用，不只限於 controller。

--分隔線--

[南丁格爾的介紹也要看一下....](http://abgne.tw/angularjs/angularjs-getting-stared/inject.html)

--分隔線--

angularJS 的 DI 主要有兩部分組成 :
+ 註冊 serivece 的 **provider** (見30.)
+ 提供注入功能的 **Injector**

`$injector` 是 angularJS 用來建立注入物件的內建 service。

他可以把註冊的 service 注入到 controller 的函式內。

可以透過 `$injector.get` 來取得要注入的 service 物件 :

	var greeting = $injector.get('greeting');
	greeting('Ford Prefect');

或者用 `injector.invoke` 來呼叫函式執行，注入對應的 service物件 :

	var myFunction = function(greeting) {
  		greeting('username');
	};
	$injector.invoke(myFunction);

`$injector` 會將建立後的 service 物件快取起來，下次要求此 service 的時候，就會直接回傳快取後的實體。

**注意!!!!**

`constants` 跟 `value` 所定義的 callback function 是不能被 `$injector` 注入的。

能被注入的只有 `controller`、 `directive` 、 `filter`  跟 `factory($get)` 所定義的 provider。

[Dependency Injection 原理](https://github.com/jaceju/book-angular-notes/blob/master/02.md)

### Refrence

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[AngularJS 教學 - Controller and View](http://programer-learn.blogspot.tw/2014/06/angularjs-controller-and-view.html)

[如来神掌 - 玩转 AngualrJS 的依赖注入](https://segmentfault.com/a/1190000002786133)

[Understanding-Dependency-Injection](https://github.com/angular/angular.js/wiki/Understanding-Dependency-Injection)

[AngularJS Service, Factory, Provider 差別](http://roxannera.blogspot.tw/2014/01/angularjs-service-factory-provider.html)

[[AngularJS]AngularJS 入門教學 - $inject](http://abgne.tw/angularjs/angularjs-getting-stared/inject.html)

[AngularJS Providers: Constant/Value/Service/Factory/Decorator/Provider](https://gist.github.com/demisx/9605099)

----

# 12. Explain ng-app directive.

要使用AngularJS之前，除了引用他的 js之外，必須要在你的HTML中設定ng-app屬性。

例如：<html ng-app>  (當然不一定要寫在html 標籤裡面)

AngularsJS 看到 ng-app 後就會自動初始化 <html> 標籤範圍內的程式。

ng-app 屬性告訴 Angular 應該管理頁面的哪一部分. 由於我們把它放在 <html>元 素中, 這將告訴 Angular 我們希望它管理整個頁面. 這往往也是你所希望的, 但是如果你是結合現有的 Angular 應用程式來使用其他方式管理頁面, 你可能希望把它放在應用程式中的某個 <div> 中。

正常來說一個頁面只能有一個 ng-app，而且可以在啟用時順便指定要使用哪個模組，而模組中可能還會有多個 controller。

例如 : <html ng-app="ModuleA">

上面指的就是要使用模組ModuleA。


### Reference

[AngularJS 初學者筆記與教學 (一) - 使用方式、Expression、Controller、Module](http://sweeteason.pixnet.net/blog/post/40589830-angularjs-%E5%88%9D%E5%AD%B8%E8%80%85%E7%AD%86%E8%A8%98%E8%88%87%E6%95%99%E5%AD%B8-(%E4%B8%80)---%E4%BD%BF%E7%94%A8%E6%96%B9%E5%BC%8F%E3%80%81ex)

[第一章 AngularJS簡介](https://github.com/MrOrz/angularjs-tw/blob/master/Chapter1.markdown)

----

# 13. Explain ng-model directive.

ng-model：View 對應 module 的名稱

要注意的是一個元素中只能有一個 ng-model

ngModel 通常跟 input , select , textarea 或 客製化的 form 做 binding。

ngModel 負責 ....

* 把 view 跟 model 互相 bind，但 directive 必須是 input , textarea 或 select。

* 提供 validation。 (required , number , email , url )

* 保存它的 state。( valid/invalid , dirty/pristine , touched/untouched , validation errors )

* 在該元素上設定相關的 css classes (ng-valid , ng-invalid , ng-dirty , ng-pristine , ng-touched , ng-untouched , ng-empty , ng-not-empty )。

* 向 parent `form` 註冊 control。

--

### 關於 ngModel.NgModelController

`NgModelController` 為 `ngModel` directive 提供了 API。

這個 controller 包含了 data-binding 的服務、 validation、 css 更、值的 formating 與 parsing。

#### Properties

+ `$viewValue`

	control's view 看到的實際的值。以 input 而言，是一個 strig。

+ `$viewValue`

	model 裡面與 control 綁定的值。

+ `$parsers` [ array < function > ]

	待執行的 function array。

	這些 functions 會按照 array 的順序呼叫，且會 pass 它的 return 值給下一個 function。

	最後一個 reutrn 值會傳給 `$validators` collection。 

	Parsers 用於 sanitize/convert `$viewValue`。

	如果 parser 回傳 `undefined` 代表有 parse error。則此時沒有 `$validators` 會運行，且 `ngModel` 會被設為 `undefined`。(例外: 如果 `ngModelOptions.allowInvalid` 設為 true) 

	parse error 會存在 `ngModel.$error.parse`。

+ `$formatters` [ array < function > ]

	待執行的 function array。

	當 model value 改變的時候，這些 function 會反向呼叫，一樣會 pass return 值給下一個。

	最後一個 return 的值會當成 DOM value。

	用於 format/convert 值，以顯示在 control。

+ `$validators` [ object < string , function > ]

	validators 的 collection，當 model value 改變的時候就會被使用。

	key: 代表 validator 的 name
	function: 代表 validation 的 operation

	validation operation 需要 model value 當參數餵入，必須回傳 true or false，取決於該 validation 的回應。

+ `$asyncValidators` [ object < string , function > ]

	validation 的 collection ，預期要處理 async 的 validation (ex: HTTP request)。

	這個 validation function 在 validation process 的期間會要求回傳一個 promise。

	當 promise 回傳後，則這個 validation status 會設為 true。

	當 async validators 被 triggered，每一個 validators 會同時 run ，且 model value 只有在所有 validators 都完成之後才會更新。

	當一個 async validator 無法完成，它的 key 會被加到 `$pending` 裡面。

+ `$viewChangeListeners` [ Array < function > ]

	一組 function array ，當 view value 被改變的時候會執行。

	用來替代額外的 `$watch` ( model value 已在使用)。

+ `$error` [ obj ]

	一個 object hash，其 keys 為所有失敗的 validator id 。

+ `$pending` [obj]

	一個 object hash，其 keys 為 所有 pending 的 validator id 。

+ `$untouched` [ boolean ]

	如果 control 還沒有失去 focus 則為 true。

+ `$touched` [ boolean ]

	如果 control 已經失去 focus 則為 true。

+ `$pristine` [ boolean ]

	如果使用者還沒有跟這個 control 互動則為 true。

+ `$dirty` [ boolean ]

	如果使用者已經跟這個 control 互動則為 false。

+ `valid` [ boolean ]

	如果沒有 error 則為 true。

+ `$invalid` [ boolean ]

	如果至少有一個 error 則為 true。

+ `$name` [ string ]

	control 的 name attribute。

#### Method 

+ `$render`

	當這個 view 需要更新時，會被呼叫。

	以下的情況會喚醒這個 `$render()`:

	+ `$rollbackViewValue` 被呼叫時。
	+ `ng-model` 參照的值有更改，且 `$modelValue` 與 `$viewValue` 都與最後一次的值不一樣。

	`$render()` 只有在 `$modelValue` 與 `$viewValue` 都跟目前的值不一樣的時候才會被呼叫。

	但如果 `$modelValue` 或 `$viewValue` 只是 obj 而不是 string 或 number，則 `$render()` 不會因為改變了物件的 property 而呼叫。

+ `$isEmpty(value)`

	當我們需要判斷它的值或 input 是不是空的('', undefined , null or NaN)，可以用。

	[ type : boolean] 如果是空的，則回傳 true。

+ `$setValidity(validationErrorKey, isValid)`

	改變 validity state，並通知這個 form。

	+ validationErrorKey [ type : string]:

		validator 的 name。

		`validationErrorKey` 會被指派給 `$error[validationErrorKey]` 或 `$pending[validationEerrorKey]，讓它可以被 data-binding。`

		`validationErrorKey` 通常是 camelCase，且在 class name 的時候會被轉成 dash-case。

		EX: myError -> ng-valid-my-error

	+ isValid [ type : boolean]:

		valid(true) , invalid(false) , pending ( undefined )

		Pending 用於未完成的 `$asyncValidators`。

+ `setPristine()`

	把 control 設為 pristine state。

	當 `ng-dirty` class 被移除的時候，此 method 會被呼叫。

	設定 `ng-pristine` class。

	prinstine : 如果 control 自 compiled 後未曾改變過

+ `setDirty()`

	把 control 設為 dirty state。

	當 `ng-pristine` class 被移除的時候會呼叫。

	設定 `ng-dirty` class。

	dirty : 如果 control 自 compiled 後有改變過

+ `$setUntouched`

	把 control 設為 untouched state。

	把 `ng-touched` class 移除的時候會呼叫。

	設定 `ng-untouched` class。

+ `$setTouched`

	把 control 設為 touched state。

	把 `ng-untouched` class 移除的時候會呼叫。

	設定為 `ng-touched` class。

	當 model 曾經 focus 在這個 control element 而後又移開，則視為 touched。

+ `$rollbackViewValue`

	取消 update，重置 input element 的值，避免對 `$modelValue` 的 update。這可能導致 pending debounced event ，因為 input 會等待 future event。

	如果我有一個 input 使用了 `ng-model-options` 來設定 debounced updates，或 update 本身是倚靠特殊的 event 來設定，就可能會造成 `$viewValue` 與 `$modelValue` 有一段時間的不同步。

	在這種狀況下，就可以使用 `$rollbackViewValue` 來手動取消這個 debounced/future update ，並重製 input 到上一次提交的 view value。

	回復 viewValue 的一個方法(簡而言之)。

+ `$validate()`

	重新跑過每一個註冊的 `validators`。

	如果 validity 變成 invalid，這個 model 會設為 undefined，除非 `ngModelOptions.allowInvalid` 被設為 true。

	如果 validity 變成 valid，它會將這個 model 設為最後有效的 `$modelValue`。

+ `$commitViewValue()`

	把 pending update commit 到 $modelValue。少用。

+ `$setViewValue(value,trigger)`

	更新 view value。

	input directive 會呼叫他，當 input 內部的值改變，而 select 也會呼叫它，當有 option 被選擇。

	當 `$setViewValue` 被呼叫時，一個新的 value 會等著被 commit (經過 `$parser` 跟 `$validators`) 。

	如果沒有特別指定 `ngModelOptions` ，這個等待的值會直接送去處理，最後應用到 `$modelValue`。

	最後，所有在 `$viewChangeListeners` list 內有註冊的，改變的 listeners 會被呼叫。

--

### ngModelOptions

可以指定一個客製化的 event list ，當 model update 或 debouncing delay 的時候會被 trigger，所以 update 只有在 timer 倒數結束的時候才會真的發生，且 timer 在其他 change 發生之後會被重設。

`ngModelOptions` 會讓 input 裡的 value 與實際 model 綁定的值可能不一樣。

所以若我們更新了 model，應該要呼叫 `$rollbackViewValue` 以確保它可以跟 model 同步，且結束任何的 debounced action。

要參照 `$rollbackvViewValue` ，必須確保 input 有 `name` attribute。

當一個 form 藉由 `submit` 關閉時，任何的 pending changes 會立刻發生。

##### Agruments

+ `updateOn`:

	是一個 string ( 或可以用空格隔開放多個 )。

	會指定這個 input 該在什麼 event 之後 bound 。

	如果指定 default，則除了指定的 event 之外，其他行為都會觸發 default。

+ `debounce`:

	是一個數字。(或可以對 updateOn 的 event 指定不同的數值)

	單位是 ms。

以下為範例 :

	ng-model-options="{ updateOn: 'default blur', debounce: { 'default': 500, 'blur': 0 } }"

+ allowInvalid:

	是 boolean。 如果這個 model 的值是 invalid，通常會被改成 undefined，但如果把 allowInvalid 設為 true，該 model 依然會使用 invalid 的值。

+ getterSetter:

	是 boolean。 決定是否把跟 `ngModel` bound 的 function 視為 getters/setters。

+ timezone:

	指定 `Date` 取用的時區。


### Reference

[AngularJS 初學者筆記與教學 (一) - 使用方式、Expression、Controller、Module](http://sweeteason.pixnet.net/blog/post/40589830-angularjs-%E5%88%9D%E5%AD%B8%E8%80%85%E7%AD%86%E8%A8%98%E8%88%87%E6%95%99%E5%AD%B8-(%E4%B8%80)---%E4%BD%BF%E7%94%A8%E6%96%B9%E5%BC%8F%E3%80%81ex)

[ngModel in docs of angularJS](https://docs.angularjs.org/api/ng/directive/ngModel)

[NgModelController](https://docs.angularjs.org/api/ng/type/ngModel.NgModelController)

[ngModelOptions](https://docs.angularjs.org/api/ng/directive/ngModelOptions)

----

# 14. Explain ng-bind directive.

習慣上我們會使用 `{{expression}}` 。

但是當 angular.js 未載入完成前，我們可能會先看到像是 `{{ expression }}` 的內容，雖然可能會立即的被替換成真實的內容，但一開始看到總是覺得怪又醜。

原本的 html :

	<div class="well span6" style="margin-top:20px;">
		<label>Name:</label>
		<input type="text" ng-model="name" placeholder="Enter a name here">
		<hr>
		<h1>Hello {{ name || "AngularJS" }}!!</h1>
	</div>

如果載入未完成會看到 ....

<img src="http://abgne.tw/wp-content/uploads/2013/09/ng-bind-0.png">

使用 `ng-bind` 來取代原本使用 {{expression}} 的方式 :

	<div class="well span6" style="margin-top:20px;">
		<label>Name:</label>
		<input type="text" ng-model="name" placeholder="Enter a name here">
		<hr>
		<h1>Hello <span ng-bind="name || 'AngularJS'">AngularJS</span>!!</h1>
	</div>

### Reference

[[AngularJS]AngularJS 入門教學 - ng-bind](http://abgne.tw/angularjs/angularjs-getting-stared/ng-bind.html)

[ngBind in docs of angularJS](https://docs.angularjs.org/api/ng/directive/ngBind)

----

# 15. Explain ng-controller directive.

MVC 包含三個元素 :

* Model

models 是 scope 的 properties; 每一個 DOM 都會有 scopes。

* View

template 經過 data binding 後顯示出來的畫面。

* Controller

`ngController` directive 指的是一個 controller class。


在 Angular 中, 你用於管理頁面區域的 JavaScript 被稱為 controller. 經由在 <body> 中包含一個 controller, 以下為例，我們聲明這個 CarController將管理 <body> 和 </body >之間的所有事物。

	<body ng-controller="CartController">

### Reference

[第一章 AngularJS簡介](https://github.com/MrOrz/angularjs-tw/blob/master/Chapter1.markdown)

[ngController in docs of angularJS](https://docs.angularjs.org/api/ng/directive/ngController)

----

# 16. How AngularJS integrates with HTML?

1. 把 angularjs javascript library 包含在 html 裡面

		<head>
			<script src = "http://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular.min.js"></script>
		</head>

2. 指向某個 angularJS APP

	我們要確定html的哪部分包含了這個 angularJS app。

		<body ng-app = "myapp">
		</body>

### Reference

[Logic Guns : How AngularJS integrates with HTML?](http://www.logicguns.com/q/how-angularjs-integrates-with-html/564c5dc62b78724573b3ca4a/)

----

# 17. Explain ng-init directive.

在 AngularJS ，若是想要設定變數的初始值。

我們可以有兩種做法。

1. 使用 ng-init ，將初始值寫在 html 裡。
2. 未使用 ng-init，將初始值寫在 js 中(利用 `$scope` )。

ng-init：指定初始值

	<div ng-app="">  
    	<div ng-init="greeting='Hello'; person='World'">  
     		 {{greeting}} {{person}}!  
    	</div>  
  	</div> 

由於這個 directive 可能會造成一些多餘的 logic 被寫在 templates，所以基本上只在少部分的情況下適用。

ex: ngRepeat, injecting data to server side scripting

除了這些狀況，應該使用 controller 來指定而非 ngInit。

### Reference

[AngularJS 初學者筆記與教學 (一) - 使用方式、Expression、Controller、Module](http://sweeteason.pixnet.net/blog/post/40589830-angularjs-%E5%88%9D%E5%AD%B8%E8%80%85%E7%AD%86%E8%A8%98%E8%88%87%E6%95%99%E5%AD%B8-(%E4%B8%80)---%E4%BD%BF%E7%94%A8%E6%96%B9%E5%BC%8F%E3%80%81ex)

[ngInit in docs of angularJS](https://docs.angularjs.org/api/ng/directive/ngInit)

----

# 18. Explain ng-repeat directive.

ng-repeat 幾乎是一定會用到的一個 AngularJS 標籤屬性，他目的是用來在畫面上將多筆資料的 Model 用迴圈的方式一個一個的建立到視圖上，如下範例所示：

	<script>
              var demoApp = angular.module("demoApp", []);
              demoApp.controller("demoCtrl", function($scope){
                  $scope.users = [
                     {uid:'1', name: 'Allen', phone: '12493215'},
                     {uid:'2', name: 'Alex', phone: '70314568'},
                     {uid:'3', name: 'Alice', phone: '345106847'}
                  ];
             });
     </script>
     <div ng-controller="demoCtrl">
              <ul>
                  <li ng-repeat="user in users">
                       Name: {{user.name}} <br/>
                       Phone: {{user.phone}} <br/>
                  </li>
              </ul>
     </div>

這個結果最後是 AngularJS 在網頁上呈現三個 li 元素，每筆資料各有每個 User 的資料。

一些 ng-repeat 的變數： $index、$first、$last、$even、$odd

* $index 是用來描述 ng-repeat 目前的索引值，跟陣列一樣都從 0 開始
* $first 是用來描述 ng-repeat 目前的 item 是否是第一個
* $last 是用來描述 ng-repeat 目前的 item 是否是最後一個
* $even 是用來描述 ng-repeat 目前的 item 是否是位在偶數個
* $odd  是用來描述 ng-repeat 目前的 item 是否是位在奇數個

		<li ng-repeat="user in users">
        	index: {{$index}} <br/>
        	Is First: {{$first}} <br/>
        	Is Last: {{$last}} <br/>
        	Is Even: {{$even}} <br/>
        	Is Odd: {{$odd}} <br/>
        	Name: {{user.name}} <br/>
        	Phone: {{user.phone}} <br/>
     	</li>

如果不是只想要重複單一個元素，而是一堆元素的話，可以使用 ng-repeat-start 和 ng-repeat-end。

	<header ng-repeat-start="item in items">
  		Header {{ item }}
	</header>
	<div class="body">
  		Body {{ item }}
	</div>
	<footer ng-repeat-end>
  		Footer {{ item }}
	</footer>

如果 input 是['A','B']，HTML 的輸出結果為:

	<header>
  		Header A
	</header>
	<div class="body">
  		Body A
	</div>
	<footer>
  		Footer A
	</footer>
	<header>
  		Header B
	</header>
	<div class="body">
  		Body B
	</div>
	<footer>
  		Footer B
	</footer>

### Refrence

[AngularJS 教學 - Templates](http://programer-learn.blogspot.tw/2014/06/angularjs-templates.html)

[ngRepeat in docs of angularJS](https://docs.angularjs.org/api/ng/directive/ngRepeat)

----

# 19. What are AngularJS expressions?

angularJS expressions 是一種 javascript-like 的小片段語法表現方式，通常會用內插的方式使用。

angularJS 的 expressions 與 javascript 的 expressions 很像但是略有不同 :

1. **context** : javascropt 是參照 global `window`，而 angularjs 是參照 `$scope` obj。
2. **Forgiving** : javascript 如果想要使用 undefined 的 properties 會產生 `ReferenceError` 或 `TypeError`。而 angularJS 允許 `undefined` 和 `null`。
3. **No Control Flow Statements** : angularJS 的 expression 不能使用 : conditions , loops , exceptions。
4. **No RegExp Creation With Literal Notation** : 在 expression 裡面不能創造正規表現式。
5. **No Object Creation With New Operator** : 不能使用 `new` 這個 operator。
6. **No Comma And Void Operators** : 不能使用 `,` 或 `void` 的 operators。
7. **Filters** : 可以在顯示資料前，在表現式用 filters 裡面來格式化資料。

[這裡有中文的詳細介紹](http://www.iteye.com/topic/1135190)

### Reference

[Angular Expressions in docs of angularJS](https://docs.angularjs.org/guide/expression)

----

# 20. Explain filter filter.

filter 可以從 `array` 挑出其中部分的項目，回傳一個新的array。

[HTML Template Binding]

	{{ filter_expression | filter : expression : comparator}}

[javascript]

	$filter('filter')(array, expression, comparator)

* **Param(array)** : 形式為 array。

	注入要做 filter 的array。

* **Param(expression)** : 形式為 string , Object 或 function()。

	* string

		用來跟 array 做matching。

		在 array 中，只要他的 strings /或有 string 特性的 obj 符合這個 string，這些 strings 就會被 return。

	* Object

		這個obj可以用來對array中的objects裡的特定properties去過濾，之後回傳array。

		ex: {name:"M",phone:"1"} 這個predicate,會return一個items的array,其擁有properties(name有M且phone包含1)。

		{$:"text"} -> $可以代表任何properties,不一定要同一層 ex: {name:{first:'John',last:'Doe'}}

		{name:"!M"} -> 不包含M的

	* function(value,index,array)

		一個 predicate function可以被用來寫自創filters。

		array裡的每一個element都會根據他的index去call這個function，而array本身就是他的輸入。

		最後回傳的結果就是一個array，包含符合這個predicate特性的elements。

* **Param(comparator)** : 形式為 function(actual,expected) , true 或 undefined。

	comparator用來決定是否 expected value(來自filter expression) 和 actual value (來自array裡面的object)是不是符合。

	* function(actual,expected)

		這個function接受ojbect的value跟predicate的value，藉此比較，如果兩個values是equal，回傳true。

	* true

		對以下的縮簡寫法 :

			function(actual,expected){return angular.equal(actual,expected)}

		這是對 expected 和 actual 特別嚴格的比較。

	* fale/undefined

		這是對 比較不嚴謹的substring match 的寫法。

		初始給的values會被轉變成strings。而objects不能跟初始的values比較，除非他們有自訂的 toString method

		( ex:Date objects)

### Refrence

[filter in doc of angularJS](https://docs.angularjs.org/api/ng/filter/filter)

----

# 21. Explain orderby filter.

藉由 `expression` predicate 對 `array` 做排序。

如果是 string，則是按照字母順序排序；如果是 number，則按照數字大小(要注意輸入的到底是 數字 還是 string)。

[ in html ]

	{{ orderBy_expression | orderBy : expression : reverse}}

[ in javascript ]

	$filter('orderBy')(array, expression, reverse)

**Param(array)** : 形式為 array。

可以對 array( 或長得像 array 的obj) 做排序。

**Param(expression)** :　形式為 `function(*)` , `string` 或 `Array.<function(*)|string>=`，也就是所謂的 predicate。

* function

	也就是 Getter function。 使用這個 function 產生的結果會藉由 <, === , > 去做 sort。

* string

	一個正規表現式。他用來比較 elements (ex: `name.substr(0,3)` to sort by 3 first characters of a property called `name`)

* array

	是由 function 或 string predicates 組成的一個 array。array 中的第一個 predicate 是第一個sort條件，只有兩個搜尋項目都符合條件的時候，才會使用 array 中的下一個 predicate。
	(default值是+)

**Param(reverse)** : 形式為 boolean，可選擇要不要附加。

讓這個 array 的順序做反序。

### Reference

[orderBy in docs of angularJS](https://docs.angularjs.org/api/ng/filter/orderBy)
	
----

# 22. Explain ng-click directive.

利用點擊讓javascript做點事情 例如改變值(進而可以改變html)。

	<ANY
  		ng-click="expression">
		...
	</ANY>

### Reference

[ng-Click in docs of angularJS](https://docs.angularjs.org/api/ng/directive/ngClick)

----

# 23. How angular.module works?

Module是 angularJS 用來收納整理 Controller, Directive, Filter,  Serivce 的管理單位。用 Module 將相關邏輯集中，存成不同JS檔，有利於維護及引用。

	var calApp = angular.module('CalApp', []);

使用 angular.module(name, requires) 來建立模組(Module)時，第一個參數是要指定想要的名稱，第二個陣列參數則是說當使用這模組(Module)時，它還會需要使用那些額外的模組(Module)。

**當第二個參數有被設定的時候 則會設定一個全新的 module**

**如果沒有設定 則會根據這個名字去找有設定過的 module**

用樂高積木來說的話，就是當我們建立了一個名叫 CalApp 的積木時，它是不是預設需要搭配其它的積木。

而現在建立了一個模組(Module)之後，若它有其它額外的功能的話，那我們也要一一幫它建立起來，像是我們需要一個用來計算用的 Controller：

	calApp.controller('CalCtrl', function($scope){
		$scope.num1 = 0;
		$scope.num2 = 5;
	});

我們這邊使用 Model 物件的 .controller(name, constructor) 來新增 Controller，而每個 Controller 的動作就是基本的函式組成的。

再以樂高積木來看的話，這邊就是用來設定我們的積木有怎樣的功能，像是氣密窗積木的功能也許是能開窗關窗等等。

**注意: ng-app 這時要改成 CalApp**

	<html ng-app="CalApp">

官方建議要把每一個 feature 都配一個 module，每一個可重複使用的 component(特別是 directives 跟 filters)都配一個 module。

--

先設定一個module :

	// 單純設定一個 module
	angular.module('myModule', []);
	//或你也可以直接設定一個 obj 儲存這個 module
	var myModule = angular.module('myModule',[]);

module 底下提供了這些 method 可以使用 :

	01. myModule.provider( ... );	//產物為 services
	02. myModule.factory( ... );	//產物為 services 產生一個 obj
	03. myModule.service( ... );	//產物為 services 產生一個function
	04. myModule.value( ... );
	05. myModule.constant( ... );
	06. myModule.decorator( ... );
	07. myModule.animation( ... );
	08. myModule.filter( ... );
	09. myModule.controller( ... );
	10. myModule.directive( ... );
	11. myModule.component( ... );
	12. myModule.config( ... );		//注入 `$...` 一類的 services
	13. myModule.run( ... );

**原則上應該針對每一個不同的 method 設定不同的 js檔**

你可以選擇使用這些 module 底下的 method，或者用 `config` 把想要用的 service 包進來(像是 `$provider`, `$filter`)。

不過 NG 設好的 method 會比較方便用啦 ...

### Refrence

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[module in docs of angularJS](https://docs.angularjs.org/guide/module)

[angular.Module in docs of angularJS](https://docs.angularjs.org/api/ng/type/angular.Module)

----

# 24. How to validate data in AngularJS?

angularJS 會監控 form/input fileds  (input,textarea,select) ，而且讓我們去通知使用者如何輸入正確格式的內容。

Required:

	<form name="myForm">
		<input name="myInput" ng-model="myInput" required>
	</form>

	<p>The input's valid state is:</p>
	<h1>{{myForm.myInput.$valid}}</h1>

E-mail:

	<form name="myForm">
		<input name="myInput" ng-model="myInput" type="email">
	</form>

	<p>The input's valid state is:</p>
	<h1>{{myForm.myInput.$valid}}</h1>

angularJS 讓這些欄位帶有一些 state 可以表現 :

* $untouched : 這個欄位還沒有被點擊過
* $touched : 這個欄位已經被點擊過
* $pristine : 這個欄位還沒有被改過值
* $dirty : 這個欄位已經被改過值
* $invalid : 這個欄位的格式並不符合需求
* $valid : 這個欄位的格式符合需求

### Refrence

[AngularJS Form Validation in w3schools.com](http://www.w3schools.com/angular/angular_validation.asp)

----

# 25. Explain ng-include directive.

 ng-inclide 是用來將其他的 HTML page 加載到目前的 HTML 內，例如我定義了一個額外的 HTML page (include.html)內容如下

	<p>I'am a included page</p>
	A quantity of users: {{users.length}}          

並且在原本的頁面加入

	<ng-include src="'include.html'"></ng-include>

指定 src 屬性到要 include 的 page 路徑，注意要用單引號括住整個路徑字串。

如果想要在 page 被 include 近來時做一些事情，可以加上 onload 屬性來執行。

### Refrence

[AngularJS 教學 - Templates](http://programer-learn.blogspot.tw/2014/06/angularjs-templates.html)

----

# 26. How to make an ajax call using Angular JS?

AngularJS提供一個叫做 $http 的 service 物件，讓開發者可以使用AJAX 的功能。

$http 當作Controller 的第二個參數傳遞進來，讓 Controller 直接用程式碼操縱 AJAX，像是這樣：

	myModule.controller('ajaxController',function($scope,$http){})

這是AngularJS中最簡便來實作AJAX的方法。

$http 為四種 HTTP Request 提供的函數為：

* $http.get(url, config)
* $http.post(url, data, config)
* $http.put(url, data, config)
* $http.delete(url, config)

這裡可以看到 post 和 put 有多一個叫做 data 的參數，這個參數是要送到後端的資料，必須是 JSON 格式的物件，在傳送之前，data 會被轉成JSON 字串 ( JSON String )，這一點在後端伺服器程式中，在處理資料上可能需要注意。

url 是 HTTP Request 的 URL ，注意由於瀏覽器的安全性措施 SOP ( Same Origin Policy )，URL 不能是其他網域。關於跨網域的 HTTP Request ，$http 另外有提供 JSONP 的方式。

config 提供額外的參數，以JSON物件的形式作為參數傳入，後面會再提到。

### Reference

[上手 AngularJS - 主題：AJAX](http://code-beginner.logdown.com/posts/307290-hand-angularjs-topics-ajax)

----

# 27. What is use of $routeProvider in AngularJS?

雖然我們一樣是建立模組，但因為需要額外進行一些設定，所以使用的函式是 angular.module(name[, requires], configFn)。

我們在建立模組除了指定所需要的 ngRoute 模組之外，同時還多提供了一個用來設定的函式。在設定時，咱們需要幫我們準備場景的場記 $route，所以得額外傳入 $routeProvider 才行。接著使用 when(path, route) 跟場記 $route說，當遇到是 / 時，模板要從那裡來。

	var onePiece = angular.module('OnePiece', ['ngRoute'], function($routeProvider){
			$routeProvider.when('/', {
			templateUrl: 'view.html'
		});
	});

### Reference

[[AngularJS]AngularJS 入門教學 - ngRoute 與 ngView (一)](http://abgne.tw/angularjs/angularjs-getting-stared/ngroute-ngview.html)

----

# 28. What is $rootScope?

$rootScope 是 AngularJS app 中最上層的 scope，一個 app(ng-app) 只會有一個 $rootScope，也就是說同一個 app 可以共用同一個 $rootScope 的值。

### Reference

[[AngularJS]$rootScope](http://blog.johnsonlu.org/angularjs-rootscope/)

----

# 29. What is scope hierarchy in AngularJS?

The $scope object used by views in AngularJS are organized into a hierarchy. There is a root scope, and the root scope has one or more child scopes. Each view has its own $scope (which is a child of the root scope), so whatever variables one view controller sets on its $scope variable, those variables are invisible to other controllers.

views 所使用的 `$scope` object 是有階層性的。

會有一個 root scope，而root scope會有一個或更多的 child scopes。

每一個 view 都有它自己的 $scope (他是 root scope 的 child scope)，所以某 view 的 controller 在 $scope 所設定的變數，都可以被該 view 的其他 controller 使用。

### Reference

[AngularJS $scope Hierarchy](http://tutorials.jenkov.com/angularjs/scope-hierarchy.html)

----

# 30. What is a service?

跨 Controller 共用的邏輯一般會收納成 Serivce。

angularJS 的 Service 有三種模式: 

* factory() [32.]

* service() [31.]

* provider() [34.]

<img src="https://segmentfault.com/img/bVm9Et">

供应商==>泛指provider

服务==>泛指service

provider==>provider()方法创建的东东

service==>service()方法创建的东东

$provide是一个服务，在Auto模块中

这个服务下面有好多方法，是用来定义供应商

而供应商是用来提供服务的，被注入来注入去的东西就是供应商们提供的服务

*$provide 是一個服務，這個服務下面有很多方法可以定義供應商，而供應商會提供服務，所以被注入來注入去的就是供應商提供的服務。*

--分隔線--

提供一個範例。

以下的程式碼都用來提供相同的功能，也就是註冊一個可以被注入的service，名稱叫 greeting :

1. 使用 `$provide.provider` 的 `$get` 屬性來註冊
2. 使用 `$provide.service` 的方式來註冊
3. 使用 `$provide.factory` 的方式來註冊
4. 使用 `$provide.value` 的方法來註冊
5. 快捷法 ( 取代 `$config` , `$provide` ) 來註冊 - `.provider`
6. 快捷法 ( 取代 `$config` , `$provide` ) 來註冊 - `.service`
7. 快捷法 ( 取代 `$config` , `$provide` ) 來註冊 - `.factory`
8. 快捷法 ( 取代 `$config` , `$provide` ) 來註冊 - `.value`

(實際的程式範例)

1. 使用 `$provide.provider` 的 `$get` 屬性來註冊

		myApp.config(function($provide) {
  			$provide.provider('greeting', function() {
    			this.$get = function() {
      				return function(name) {
        				alert("Hello, " + name);
      				};
    			};
  			});
		});

2. 使用 `$provide.service` 的方式來註冊

		myApp.config(function($provide) {
  			$provide.service('greeting', function() {
    			return function(name) {
      				alert("Hello, " + name);
    			};
  			});
		});

3. 使用 `$provide.factory` 的方式來註冊

		myApp.config(function($provide) {
  			$provide.factory('greeting', function() {
    			return function(name) {
      				alert("Hello, " + name);
    			};
  			});
		});

4. 使用 `$provide.value` 的方法來註冊

		myApp.config(function($provide) {
  			$provide.value('greeting', function(name) {
    			alert("Hello, " + name);
  			});
		});

5. 快捷法 ( 取代 `$config` , `$provide` ) 來註冊 - `.provider`

		myApp.provider('greeting', function() {
  			this.$get = function() {
    			return function(name) {
      				alert("Hello, " + name);
    			};
  			};
		});

6. 快捷法 ( 取代 `$config` , `$provide` ) 來註冊 - `.service`

		myApp.service('greeting', funciton () {
    		this.sayHello = function (name) {
        		alert("Hello, " + name);
    		};
		});

7. 快捷法 ( 取代 `$config` , `$provide` ) 來註冊 - `.factory`

		myApp.factory('greeting', function() {
  			return function(name) {
    			alert("Hello, " + name);
  			};
		});

8. 快捷法 ( 取代 `$config` , `$provide` ) 來註冊 - `.value`

		myApp.value('greeting', function(name) {
  			alert("Hello, " + name);
		});

### Refrence

[NG筆記1-AngularJS學習資源與術語](http://blog.darkthread.net/post-2014-06-11-angularjs-notes-1.aspx)

[AngularJS中的Provider们：Service和Factory等的区别](https://segmentfault.com/a/1190000003096933)

[Dependency Injection 原理](https://github.com/jaceju/book-angular-notes/blob/master/02.md)

----

# 31. What is service method?($provide)

是一種 provider (見 34.，與31.,32.,33.對照看)。

如果想定義 `$get` 跟 return new，沒有其他需求，可以使用 `service`。

	$provide.provider('myDate', {
    	$get: function() {
      	return new Date();
    	}
	});
	//可以寫成
	$provide.service('myDate', Date);

### Refrence

[[AngularJS系列(4)] 那伤不起的provider们啊~](http://hellobug.github.io/blog/angularjs-providers/)

----

# 32. What is factory method?($provide)

是一種 provider (見 34.，與31.,32.,33.對照看)。

如果只是想定義 `$get` 而沒有其他的需求，可以使用 `factory`。

	$provide.provider('myDate', {
    	$get: function() {
      	return new Date();
    	}
	});
	//可以寫成
	$provide.factory('myDate', function(){
    	return new Date();
	});

	//調用的方式:
	app.controller('MainCtrl', function($scope, myDate) {
  		$scope.myDate = myDate; //current date
	});


### Refrence

[[AngularJS系列(4)] 那伤不起的provider们啊~](http://hellobug.github.io/blog/angularjs-providers/)

----

# 33. What are the differences between service and factory methods?($provide)

(見 34.，與31.,32.,33.對照看)。

	$provide.provider('myDate', {
    	$get: function() {
      	return new Date();
    	}
	});
	//可以寫成
	$provide.factory('myDate', function(){
    	return new Date();
	});
	//可以寫成
	$provide.service('myDate', Date);

另一個範例 :

	angular.module('myExample', []).controller('myCtrl', function($scope, LuffyFactory, LuffyService) {
		// 一樣的取用方式
		console.log('LuffyFactory', LuffyFactory.word); // 我要成為海賊王
		console.log('LuffyService', LuffyService.word); // 一樣～
	});  
  
	// factory  
	angular.module('myExample').factory('LuffyFactory', function () {  
    	var factory = { };  
    	factory.word = '我要成為海賊王!';  
    	return factory;  
	})  
  
	// service  
	angular.module('myExample').service('LuffyService', function () {  
    	this.word = '我要成為海賊王！';  
	}) 

Factory 的行為很簡單，Controller 注入 Factory 取得的物件就是被 factory object ({}) 包起來並且 return 的物件。
也就是說，假如沒有被 { } 包起來值都不會被外面取得。
（p.s. 不一定要 return object, 任何 type 都可以）

如果改寫的話 :

	angular.module('myExample').factory('LuffyFactory', function () {  
    	// 這裡可以放 private value，外面無法取得  
    	var value = “Hello, 我是 private 值”;
		return {word: '我要成成海賊王!';}  
	})  

Service 是把東西存在 this 裡面，像是我們平常在製作 constructor 一樣。
以上範例假如寫成 constructor 就會是:

	function LuffyService () {  
  		this.word = '我要成為海賊王！';  
	}  

AngularJS 在初始化的時候，會幫我們把 new LuffyService() 這個動作完成。
所以當 controller 要求注入 LuffyService 的時候，AngularJS 就會把已經 new 過的的 LuffyService 給 controller。

**Provider 都是 singleton。**
所以就算 Service 有 new 這個指令，可是其實也只是做一次而已。
每個要求注入 Service 的 components 拿到的都會是同一個 Service reference。
Factory 也是相同的情況，全部系統共用一個 Factory reference。

### Refrence

[[AngularJS系列(4)] 那伤不起的provider们啊~](http://hellobug.github.io/blog/angularjs-providers/)

[Service，Factory 傻傻分不清楚](http://ithelp.ithome.com.tw/question/10161278)

----

# 34. What is provider?($provide)

`$provide` 負責告訴 angular 怎麼創造新的 injectable things。而這些 things 就是 **services**。 services 會被 **providers** 給呼叫，而providers 正是你用 `$provide` 所創造的東西。
[(原文見)](https://github.com/angular/angular.js/wiki/Understanding-Dependency-Injection)

<img src="http://i.imgur.com/iKDimba.png">

provider 可以為應用提供通用的服務，形式可以是常量或對象。

例如 controller 裡面常用的 $http 就是 angularJS 提供的 provider。

	myApp.controller(‘MainController',
		function($scope, $http) {
    	$http.get(…)
	}

如此便可以直接使用 `$http` 包好的各種功能。

如何自己定義一個 provider :

	//定義:
	$provide.provider('age', {
    	start: 10,
    	$get: function() {
      	return this.start + 2;
    	}
	});
	//或
	$provide.provider('age', function($filterProvider){
    	this.start = 10;
    	this.$get = function() {
      	return this.start + 2;
    	};
	});

	//調用的方式:
	app.controller('MainCtrl', function($scope, age) {
  		$scope.age = age; //12
	});

常見、已提供的provider們 :

service(31.) factory(32.) constant(35.)

Provide的優點 :

* 應用提供通用的服務，形式上可以是常量或對象
* 便於模塊化
* 便於單元測試


### Refrence

[[AngularJS系列(4)] 那伤不起的provider们啊~](http://hellobug.github.io/blog/angularjs-providers/)

[[AngularJs]淺談Angular.js的Provider機制](http://kirkchen.logdown.com/posts/245678-angularjs-talking-about-the-angularjs-provider-mechanisms)

----

# 35. What is constant?($provide)

是一種 prodiver(見34.)。

如果只想要定義 `$get`，且返回一個常量。

	$provide.value('pageCount', 7);
	$provide.constant('pageCount', 7);

但 `value` 跟 `constant` 有些許的不同，雖然都是定義一個常量，然而 `constant` 是不能被修改，但是 `value` 可以藉由 `decorator` 的機制去攔截。

且 `constant` 可以被注入 `config`，但是 `value` 不行 !

### Refrence

[[AngularJS系列(4)] 那伤不起的provider们啊~](http://hellobug.github.io/blog/angularjs-providers/)

[[AngularJs]淺談Angular.js的Provider機制](http://kirkchen.logdown.com/posts/245678-angularjs-talking-about-the-angularjs-provider-mechanisms)

----

# 36. On which types of component can we create a custom directive?

基本上，只要你創建 compilers 跟 services，你就可以自創 directives 為 angularJS 所使用。

註冊 directive 必須藉由 `module.directive` API，而這個 API 會藉由 **factory function** [見32.] 取得正規化後的 directive name。factory function 會回傳一個 obj，內含不同的選項，以告訴 `$compile` 當這個 directive matched後，會有什麼行為。

*factory function 只有 directive 第一次被 match 時才會invoke。*

[自己寫一個directive，讓固定的html段落可以用簡單的方式注入](http://hellobug.github.io/blog/angularjs-directive-1-basic-introduction/) *在docs也有描述*

[自己寫一個directive，輸入的用戶名後，可以去後台請求查詢該用戶是否已經存在](http://hellobug.github.io/blog/angularjs-directive-2-compile-vs-link/)

*docs 中還有一些創建 directives 的範例可以參考*


### Reference

[[AngularJS系列(5-2)] Directive - Compile vs. Link](http://hellobug.github.io/blog/angularjs-directive-2-compile-vs-link/)

----

# 37. What are the advantages of AngularJS?

其優異的框架設計像是 宣告式語法 (Directives)、DOM Templates、雙向資料繫結 (Two Way Data-Binding)、相依性注入 (Dependency Injection) 與關注點分離等特性。

* 程式碼更簡單(少寫很多js代碼)
* 擴展了html的功能
* 實現了model與view的雙向綁定
(隨時在input裡面輸入，就可以藉由ng-model綁定對應的model)
* 分離了js跟view(現在html可以自己處理view的變動問題)
* 讓js有mvc的框架
* 換頁面不再需要重新向後台申請
* 便於寫單元測試(因為行為和介面分離了)


[前端工程的極致精品： AngularJS 開發框架介紹](http://blog.miniasp.com/post/2013/04/23/Front-end-Engineering-Fineart-An-Introduction-to-AngularJS.aspx)

[AngularJS系列(2):Scope什麼的~](http://hellobug.github.io/blog/angularjs-scope/)

----

# 38. What are the disadvantages of AngularJS?

1. sorce code 太繁雜，很難研究。
2. 自己寫一個 directive 其實很複雜，難以使用。
3. 比較難跟其他跟 DOM 相關的 library 一起使用。
4. $resource 的功能比較弱，缺少很多必要的 http 方法。
5. binding 的元素太多可能網頁會變慢 ( 因為 dirty-check)
6. 除了 directive 以外無法操作原生 DOM


### Reference

[AngularJS 有没有缺点？MVVM 框架中有比它更好的吗？](https://www.zhihu.com/question/21151483)

----

# 39. Which are the core directives of AngularJS?

ng-include, ng-click, ng-repeat

### Reference

[AngularJS Core Directives: Include, Click and Repeat](http://www.c-sharpcorner.com/UploadFile/2ed7ae/angularjs-core-directives-include-click-and-repeat/)

----

# 40. What is promises in javascript?


一般處理 javascript 的非同步事件，會利用 callback。 

(使用一個function，然後給他一個函數引用，當這個 function 完結的時候執行這個函數引用)

但這樣做的過程很複雜，會造成大量的 module code 跟 callback 地獄。

所以才需要 promise 的存在。

	Promise.all([img1.ready(), img2.ready()]).then(
		function() {
  			// all loaded
		}, function() {
  			// one or more failed
		}
	);

一個 promise 只能是成功或失敗。而且只有一次，也不能切換。

如果 promise 成功，即使我在後來增加了一個 success callback，這個callback 依然可以被呼叫，儘管 event 發生在它之前。

<img src="http://static.oschina.net/uploads/space/2014/0722/093554_PCfN_1035386.png">

promises 背後的概念分成兩部分 :

+ deferreds : 
+ promises : 從 deferreds

promise 的四種可能 :

+ fulfilled : 與這個 promise 關聯的 action 成功了
+ rejected : 與這個 promise 關聯的 action 失敗了
+ pending : 還沒有 fulfilled 或 rejected
+ settled : 已經 fulfilled 或 rejected

一個 promise-like 的 object 會有一個 `then` method。

**如何創建一個 promise :**

	var promise = new Promise(function(resolve, reject) {
  		// do a thing, possibly async, then…

  		if (/* everything turned out fine */) {
    		resolve("Stuff worked!");
  		}
  		else {
    		reject(Error("It broke"));
  		}
	});

promise 需要一個 argument , 一個 callback 跟 兩個 parameters ( resolve 跟 reject )。

callback 的時候可以做點什麼(像是非同步)，如果都運作了就呼叫 resolve，否則呼叫 reject。

如果要 reject 一個 Error object:

	promise.then(function(result) {
  		console.log(result); // "Stuff worked!"
	}, function(err) {
  		console.log(err); // Error: "It broke"
	});

`then` 需要兩個 arguments : 一個 success 後的 callback，跟一個 failure 後的 callback。

### Reference

[AngularJS 中的 Promise 和 设计模式](http://my.oschina.net/ilivebox/blog/293771)

[JavaScript Promises](http://www.html5rocks.com/en/tutorials/es6/promises/)

# 41. What is a promises in angularJS ?

angularJS 有它自己的 promises : `$q`。

關於 $q，以下做一個比喻 :

> 你是個投資家，有一天你決定要投資某個新公司，所以你到銀行領錢。
> 
> 結果銀行剛好進行大盤點，要明天一早你才能領錢。
> 
> 可是你一定要在今天簽訂投資的合約，不然這個新公司就有別人要投資了！

> 行員：「公司引進了一個新系統 $q 可以解決這個問題！」

> 行員先在新系統 $q 登記了一個請款記錄，之後系統 $q 吐出一張智慧保證卡，行員把智慧保證卡給您。

> 行員：「先生，這是智慧保證卡，這張卡會在隔天票款好時會自動通知持有人，屆時拿著這張卡就可以當做現金了。」

> 於是你拿著智慧保證卡簽成了投資新公司的合約，並且把這張智慧保證卡給了公司的負責人。

> 隔天銀行完成了大盤點的工作，並在系統 $q 看到你的請款記錄，在準備好票款後，銀行用智慧保證卡通知了公司負責人；公司負責人在收到智慧保證卡的通知後，就開始安心的採購他所需要的用品。

在這個故事中，對應的角色分別是 :

+ 申請一個請款紀錄 : `$q.defer()`

+ 這個請款紀錄對應的智慧保證卡 : `$q.defer().promise`

+ 票款好了後，通知保證卡持有人 : `$q.defer().resolve()`

+ 持有人收到票款，可以做其他事情了 : `$q.defer().promise.then`

具體的用程式描述 ...

	var myApp = angular.module('myApp',[]);  

	// 注入 $q 這個 Service
	//$timeout 是為了模擬銀行處理程序而注入
	myApp.controller('MyCtrl', function($scope, $q, $timeout){

		// 銀行對領錢客戶的處理流程
    	var bankCashCheck = function(cash){

			// 行員向系統 $q 註冊請款 job    
        	var cashCheckJob = $q.defer();
			// 請款 job 回給行員一個智慧保證卡 
        	var promiseCard = cashCheckJob.promise;
			 
			// (Thread 2) 銀行進行的工作，這個動作是另一個 thread 處理了  
       		$timeout(function(){
            	// (Thread 2) 銀行盤點完
				alert("Yes! We got it!"); 
				// (Thread 2) 通知智慧保證卡並給予款項  
            	cashCheckJob.resolve(cash); 
        	}, 1*1000);

			// 行員將智慧保證卡給你   
        	return promiseCard;   
    	}  
  
    	$scope.getMoney = "N/A";  

		// 你去銀行領錢的過程
    	$scope.cash = function(cash){
			// 從行員手上拿到智慧保證卡
        	var promiseCard = bankCashCheck(cash);
			// 智慧保證卡兌現後得到 getCash 這些錢之後要做的事  
        	promiseCard.then(function(getCash){
				//要做的事情
            	$scope.getMoney = getCash;
        	});  
  
        	/** 通常在開發時，不會特別設一個參數而是直接寫成一行  
        	bankCashCheck(100).then(function(getCash){ 
            	$scope.getMoney = getCash; 
        	}); 
       		**/  
    	};  
	});

**防錯的功能**

如果今天出現錯誤(帳戶的錢其實不夠)，那要觸發 reject :

	cashCheckJob.reject("Your Account has no enough money!");

如果智慧保證卡沒兌現該做什麼 :

	 intelligentPromise.then(" Give company money. ", "Check my account");

所以稍微修改一下剛剛的 code ...

	var myApp = angular.module('myApp',[]);  
  
	myApp.controller('MyCtrl', function($scope, $q, $timeout{
  
		//銀行對領錢客戶的處理流程
    	var bankCashCheck = function(cash){
			// 行員向系統 $q 註冊請款 job
        	var cashCheckJob = $q.defer();
			// 請款 job 回給行員一個智慧保證卡 
        	var promiseCard = cashCheckJob.promise;  

			// (Thread 2) 銀行進行的工作，這個動作是另一個 thread 處理了          
       		$timeout(function(){
				// (Thread 2) 銀行盤點完
        		alert("Yes! We got it!");  
            	var accountMoney = 100;

			 	// 檢查帳戶是否有足夠金額  
            	if(accountMoney >= cash){
					// 金額足夠：通知智慧保證卡並給予款項  
            		cashCheckJob.resolve(cash);
            	}
				else{
					// 金額不足：通知智慧保證卡並給予款項  
            		cashCheckJob.reject('No enough money.');  
            	}                
        	}, 1*1000);

			// 行員將智慧保證卡給你 
        	return promiseCard; 
    	}
  
    	$scope.getMoney = "N/A";  

    	// 你去銀行領錢
		$scope.cash = function(cash){
			// 從行員手上拿到智慧保證卡 
        	var promiseCard = bankCashCheck(cash);  
        	// 智慧保證卡兌現後得到 getCash 這麼多錢之後要做的事
			promiseCard.then(function(getCash){
				//要做的事情  
        		$scope.getMoney = getCash;  
        	}, function(cancelReason){  
           		// 得到交易取消的通知和原因，可作其他處理  
           		alert("Can't get money, because "+ cancelReason); // Can't get money, because No enough money.  
        	});  
    	};  
	});

**連續的 Promise**

簽完約的新公司拿到智慧保證卡之後，立刻去買機器。

公司負責人只能用智慧保證卡付款，所以公司保證人給機器廠商一個承諾:

保證銀行兌現後，會付給廠商款項，廠商就可以給他機器。

所以流程 ....

1. 取得智慧保證卡 :

		intelligentPromise = cashCheckJob.promise;

2. 公司負責人用智慧保證卡，給機器廠商的承諾 :

		var companyPromise = intelligentPromise.then(" Give company money.", "Check my account");

3. 廠商拿到公司負責人的 prmoise 要做的事情 :

		 companyPromise.then("Give/Not Give company machine.");

**完整的js程式碼如下**

	var myApp = angular.module('myApp', []);

	myApp.controller('MyCtrl', function ($scope, $q, $timeout, $rootScope) {
    	$scope.accountMoney = 100;
    	var bankCashCheck = function (cash) {
        	var cashCheckJob = $q.defer();
        	var promiseCard = cashCheckJob.promise;
        	$timeout(function () {
            	alert("Yes! We got it!");
            	if ($scope.accountMoney >= cash) {
                	cashCheckJob.resolve(cash);                
            	} else {
                	cashCheckJob.reject('No enough money.');
            	}
        	}, 5 * 100);
        	return promiseCard;
    	};
    
    	$scope.getMoney = "N/A";
    	var companyPromiseCard; 
    	$scope.cash = function (cash) {
        	companyPromiseCard = bankCashCheck(cash).then(function (getCash) {
            	$scope.getMoney = getCash;
            	return 60;
        	}, function (cancelReason) {
            	$scope.getMoney = 0;
            	alert(cancelReason);
            	return 0;
       		});
    	};

    	$scope.dealMoney = 50;
    	$scope.buyMachine = function () {
        	if(companyPromiseCard === undefined){
            	alert('Not conatct yet!');
            	return;
        	}
        	companyPromiseCard.then(function (money) {
            	if(money >= $scope.dealMoney){
                	alert('Get ' + money + '. Give machine.');
            	}
				else{
                	alert('No money, no machine.');
            	}   
        	});
    	};
    
    	$scope.reset = function () {
        	companyPromiseCard = undefined;
        	$scope.getMoney = "N/A";
    	};
	});

--看過上面的，因為對 function 跟 promise 命名容易搞混，所以重寫--

[這是只有 resolve 的情況]

	var $scope.getMoney;
	var cash = 100;

	var cashCheckJob = $q.defer();

	// 故意延遲一點點才發 cash 給 resolve
	$time(function(){
		alert("yes we got it");
		cashCheckJob.resolve(cash);
	},1*1000);

	//cash 會餵給 then 只是在這裡 then 的輸入參數改名字叫 getCash

	cashCheckJob.promise.then(function(getCash){
		$scope.getMoney = getCash;
	});

[這是 resolve 跟 reject 的情況]

	var $scope.getMoney;

	// cash 本身也可以讓 html 去決定，用呼叫function
	var cash = 100;
	var cashCheckJob = $q.defer();

	// 故意延遲一點點才發 cash 給 resolve
	$time(function(){
		if(cash>100)
		{
			alert("yes we got it");
			cashCheckJob.resolve(cash);
		}
		else
		{
			cashCheckJob.reject("Not enough money!");
		}
	},1*1000);

	//cash 會餵給 then 只是在這裡 then 的輸入參數改名字叫 getCash

	cashCheckJob.promise.then(
		function(getCash){
			$scope.getMoney = getCash;
		},function(WrongMessage){
			alert("Wrong, Reason : " + WrongMessage);
		}
	);

### Reference

[AngularJS $q deferred 和 promise](http://roxannera.blogspot.tw/2014/03/angularjs-q-deferred-promise.html)

--分隔線--

上面是用範例去理解 `$q` ，所以使用的方法基本上是 :

+ 通過 `$q.defer()` 來得到一個 `deferred` 的 obj
+ `$q.defer()` 出來的 obj 會有一個 promise 的 obj
+ `promise` 的 obj 負責定義 callback function
+ `deferred` 的 obj 觸發 callback

+ deffered 的 obj 決定什麼時候要觸發 promise obj 內的 callback function

--分隔線--

兒子對爸爸承諾會用望遠鏡預測天氣，

如果預測天氣好，爸爸會去釣魚；

如果預測天氣不好，爸爸會待在家；

如果無法預測，爸爸也會待在家。

[Promises in AngularJS, Explained as a Cartoon](http://andyshora.com/promises-angularjs-explained-as-cartoon.html)

ar ABC = $q.difer();

ABC.promise = 某種承諾;

ABC.resolve = 承諾達成做的事情

ABC.reject = 承諾未達成做的事情

餵給 resolve 的參數 會是 then 的第一個function要吃的參數

餵給 reject 的參數 會是 then 的第二個function要吃的參數

--分隔線--

將 objectZ defer 成一個 deferred

這個 objectZ 有一個

+ promise (需要達成的事情)
+ resolve (達成事情之後 可以取得什麼值, ex : a)
+ reject (沒達成事情之後 可以取得什麼值, ex : b)
+ promise.then( function(a),function(b) )

	+ 事情達成 可以用 a 去操作 function(a)

	+ 事情沒達成 可以用 b 去操作 function(b)

所以在範例中，為什麼會使用 timeout
是要模擬即使 resolve 完成的比較晚
then 都還是會等他的值才做接下來的事情

AULIMIT = $q.defer();

則 AULIMIT 這個 object 是一個 deferend

deferend 底下有三個 obj:

+ promise : 是一個物件，底下有 then 的 method
+ resolve : 是一個物件，有達成承諾，就用 resolve return 某個值A
+ reject : 是一個物件，沒有達成承諾，就用 reject return 某個值B

AULIMIT.promise 可以使用 .then( function(A),function(B) ) 來分別使用 resolve 給的 A 與 reject 給的 B

[Deferred and Promise in Recipes with NG](http://fdietz.github.io/recipes-with-angular-js/consuming-external-services/deferred-and-promise.html)

[AngularJS 中的 Promise 和 设计模式](http://my.oschina.net/ilivebox/blog/293771#OSC_h1_1)

# 42. How to use resolve in angularJS routes ?

41 是討論一般的 resolve，接下來要討論如何使用 resolve 在 routing 上。(好累....)

在 $routeProvider 中提過的(見 8.):

route.js

	routeModule.config(['$routeProvider', function($routeProvider)
  	{
    	$routeProvider
      	.when('/index',
      	{
        	templateUrl: 'welcome.html'
      	})
      	.when('/about',
      	{
        	templateUrl: 'about.html'
      	})
      	.when('/product',
      	{
        	templateUrl: 'product.html'
      	})
      	.otherwise(
      	{
        	templateUrl: 'welcome.html'
      	}) ;
  	}]);

.when() 的第一個參數是客製化的 URL，第二個參數則是一個物件，稱為 route。

otherwise 是 default (通常放首頁或404頁面)。

route 的 obj 中有一個 templateUrl 的屬性，代表 html 檔案的路徑。

另外 route 這個 obj 還可以有其他的屬性像是

+ templateUrl
+ controller
+ controllerAs
+ template
+ resolve
+ resolveAs
+ redirectTo
+ ....

現在要來談談這個 resolve。

resolve 是一個 map obj，他會決定什麼樣的 dependencies 應該被注入這個 controller。

如果這些 dependencies 中有 promises，則在 controller 被 router 引用並正式的創造( instantiate )前， 會等到這些 promises 被 resolve 或 reject 才做這件事情。

如果所有的 promises 都成功的被 resolved，則被 resolved的 promises 中所帶的內容就會被注入，而 `$routeChangeSuccess` event 會被 fired (~~啥?~~)。如果任一個 promises 沒有被 resolved，則 `$routeChangeError` event 會被 fired (~~什麼東東~~)。

如何從 template 中取得這些 resolved 的 dependencies? `resolve map` 就藏在 route scope 裡面的 $resolve(預設值)，或客製化的一個名字(可以用 resolveAs property 來指定)。

這個 map object (也就是 when 的 route property，裡面的 resolve 本尊 ) 包含了 :

+ key : 要被注入這個 controller 的 dependency 的名字
+ factory : 可能是 string 或 function。
	+ string - 他是 service 的 alias ( ~~啥阿~~ )。
	+ function - 他會被注入，而回傳的值就會被視為 dependency。
		+ 如果回傳的值是一個 promise，他必須先 resolved，才會注入 controller。
		+ 到底要參考 `$ngRoute.$ngParams` 或 `route.current.params`，這件事情要思考一下

[$routeProvider in docs of angularJS](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider)

# 43. Explain the useful function when customing a directive.

定義 directive 的 obj 提供了以下的指令:

1. multiElement

	如果設為 true，HTML 會去蒐集 `directive-name-start` 到 `directive-name-end` 之間的 DOM nodes，把他們列為這個 direcitve 的 elements。

	通常用於比較簡單的行為 (ngClick)。

2. priority

	當有多個 directives 定義在同一個 DOM element，有時必須指定 directive 應用的先後順序。

	`priority` 會在這些 directives 的 `compile` function 呼叫前，先行排序。

	越高的 `priority`，其 directive 會越先被 compiled。 Prelink function 也會越早被呼叫，但 postlink 則反之。

	相同 priority 的 directives 會是 undefined。 priority 的初始值為 0。

3. terminal

	如果為 true，目前的 directive 會是最後一個被執行的。

4. scope

	 scope 可以是 `true` ， `false` 或者一個 obj。

	+ false : 

		這個 directive 不會創建任何 scope。會使用他的 parent scope。

	+ true :

		一個新的 child scope 會被創建給這個 directive 的 element 使用，繼承 parent scope。

	+ `{...}` :

		一個新的 "isolate" scope 會被創建給這個 directive element 使用。

		這個 "isolate" scope 與一般的 scope 不同，他沒有繼承 parent scope。

		所以他不會不小心讀/改到 parent scope 的資料。

		"isolate" scope object 定義了一套 local scope，承接自 directive element 的 attributes。

		這些 local properties 很適合用於從 template aliasing values。
	
		key 會對應這個 isolate scope 的 property 的名字。

		value 會對應這個 directive element 的 attribute 所定義的值。

		+ `@` 或 `@attr` - 

			將 local scope 對應 DOM attribute 的值，回傳的一定是 string。如果 attr 沒有指定的話，會找跟 localname 一樣的 attribute。

		+ `=` 或 `=attr` -

			雙向綁定 local scope property 與 attribute 帶的 expression。如果沒有指定 attr，會找跟 localname 一樣的 attribute。 讓 local scope 的 property 可以跟 attribute 給的 expression(可以是 ng-model) 綁定。

				[html]
				<my-component my-attr="parentModel">
				[directive]
				scope: {localModel: '=myAttr'}

			`parentModel` 與 `localModel` 會雙向綁定，修改會同步。

			建議會補上 `=?` 或 `=?attr`，避免 attribute 綁定的 expression 並未指定的情況。
