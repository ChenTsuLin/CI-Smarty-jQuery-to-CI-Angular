from [CI, Smarty, jQuery] to [CI, Angular] Guide
==============================

##First Step: Remove Smarty

###Controller
--
####1. Parse Tpl -

*[CI, Smarty, jQuery] way*

```php
$this->load->library('parser');
$this->parser->parse('dirPath/file.tpl', $data);
```

*[CI, Angular] way*

```js
$this->load->view('dirPath/file.php', $data);
```

###View
--
####1. File Ext -

```
file.tpl -> file.php
```


####2. Include File -

*[CI, Smarty, jQuery] way*

```smarty
{include file="dirPath/file.tpl"}
```

*[CI, Angular] way*

```php
<?php include APPPATH.'views/dirPath/file.php'; ?>
```


####3. Include Var -

*[CI, Smarty, jQuery] way*

```smarty
{$var}
```

*[CI, Angular] way*

```php
<?=$var?>
```


####4. If / else -
*[CI, Smarty, jQuery] way*

```smarty
{if codition}
    <input name="test" value="true">
{else}
    <input name="test" value="false">
{/if}
```

*[CI, Angular] way*

```php
<?php if (condition) { ?>
    <input name="test" value="true">
<?php } else { ?>
    <input name="test" value="false">
<?php } ?>
```


####5. foreach -
*[CI, Smarty, jQuery] way*

```smarty
{foreach key=i from=$data_list item=data}
    <li data-key="{$i}">{$data}</li>
{/foreach}
```


*[CI, Angular] way*

```php
<?php foreach ($data_list as $i => $data) { ?>
    <li data-key="<?=$i?>"><?=$data?></li>
<?php } ?>
```

####6. time -
*[CI, Smarty, jQuery] way*

```smarty
<?=$time|date_format:'%d'?>
```

*[CI, Angular] way*

```php
<?=strftime('%d', $time)?>
```

##Second Step: Start Angular

####1. Add Script
*need add others for another usages(at html head)*

```html
<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.2.8/angular.min.js"></script>
<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.2.8/angular-route.min.js"></script>
```

*js for this page(at html body bottom)*

```html
<script src="dirPath/js/thisPage.js"></script>
```

####2. Add App Scope (at body)

```html
<body ng-app="app">
```

####3. Do thisPage.js Init

```js
var app = angular.module('app', ['ngRoute']);
```

####4. Page Route
*body div for view*

```html
<div ng-view>
```

*use ng-template (at html body bottom)*
*part separate to individual php file*

```php
    <script id="part1.html" type="text/ng-template">
        <?php include APPPATH.'views/filePath/firstPart.php'; ?>
    </script>
    <script id="part2.html" type="text/ng-template">
        <?php include APPPATH.'views/filePath/secondPart.php'; ?>
    </script>
    <script id="part3.html" type="text/ng-template">
        <?php include APPPATH.'views/filePath/thirdPart.php'; ?>
    </script>
```

*thisPage.js add route config*

```js
    app.config(function($routeProvider) {
        $routeProvider
            .when('/part1', {
                controller:'FirstPartControl',
                templateUrl:'part1.html'
            })
            .when('/part2', {
            controller:'SecondPartControl',
            templateUrl:'part2.html'
            })
            .when('/part3', {
                controller:'ThirdPartControl',
                templateUrl:'part3.html'
            })
            .otherwise({
                redirectTo:'/part1'
            });
    });
```

####5. Add Controller 

```js
    app.controller('FirstPartControl', ['$scope', function($scope) {
        $scope.datas = [{key: value1}, {key: value2}];
        $scope.addData = function(data) {
            $scope.datas.push(data);    
        }
    }]);
```

####6. Redirect In Controller function (add param $location)
*[CI, Smarty, jQuery] way*

```js
    function redirect() {
        location.href = 'path/part1';
    }
```

*[CI, Angular] way*

```js
    app.controller('SecondPartControl', ['$scope', '$location', function($scope, $location) {
        $scope.toPart1 = function() {
            $location.path('/part1');
        };
    }]);
 ```
    
####7. Ajax In Controller function (add param $http)
*[CI, Smarty, jQuery] way*

```js
    $.ajax({
        type: 'POST',
        url: 'some.php',
        data: { "foo": "bar" }
    }).done(function(data) {
        // get data to view
    }).fail(function(jqXHR, textStatus) {
        // error msg
    });
```

*[CI, Angular] way*

```js
    app.controller('thirdPartControl1', ['$scope', '$http', function($scope, $http) {
        $scope.ajax = function() {
            $http({
                url: 'http://www.example.com/targetUrl',
                method: 'POST',
                data: { "foo": "bar" }
            }).success(function(data, status, headers, config) {
                $scope.data = data;
            }).error(function(data, status, headers, config) {
                $scope.status = status;
            });
        };
    }]);
```
  
###8. Share Data between Controllers
*factory*
*data value for share*
*getValue setValue method*

```js
    app.factory('data', [function () {
        var dataValue = '',
            Data = {
                getDataValue: function() {
                    return this.dataValue;
                },
                setDataValue: function(dataValue) {
                    this.dataValue = dataValue;
                }
            };
        return Data;
    }]);
```

*controller1*

```js
    app.controller('FirstPartControl', 'data', ['$scope', function($scope, $data) {
        $scope.datas = $data.getDataValue();
    }]);
```

*controller2*

```js
    app.controller('secondPartControl', 'data', ['$scope', function($scope, $data) {
        $scope.doSomeThing = function() {
            $data.setDataValue($scope.data);
        };
    }]);
```

####9. Foreach
*[CI, Angular] way (pervious)*

```php
<?php foreach ($data_list as $i => $data) { ?>
    <li data-key="<?=$i?>"><?=$data?></li>
<?php } ?>
```

*[CI, Angular] way*

```html
<li data-key="{{$index + 1}}" ng-repeat="data in data_list"></li>
```

####10. Click
*[CI, Smarty, jQuery] way*

```html
<button type="submit" id="want-to-click"></button>
```

```js
    $('#want-to-click').on('click', function() {
        doSomething();
    });
```
    
*[CI, Angular] way*

```html
<button type="submit" ng-click="deleteData()"></button>
```

```js
    app.controller('Controller', ['$scope', function($scope) {
        $scope.datas = [{key: value1}, {key: value2}];
        $scope.deleteData = function() {
            $scope.datas.pop();    
        }
    }]);
```
    
####11. Show & hide
*[CI, Smarty, jQuery] way*

```html
<div id="want-to-show"><div>
<div id="want-to-hide"><div>
```

```js
$('#want-to-show').show();
$('#want-to-hide').hide();
```

*[CI, Angular] way*

```html
<div ng-show=""><div>
<div ng-hide=""><div>
```
    
####12. Data Init
*[CI, Angular] way*

*controller php*

```php
$data['for_init'] = json_encode($for_init)
```

*view php*

```php
<div ng-init="data = <?=$for_init?>">
```

*js*

```js
    app.controller('Controller', ['$scope', function($scope) {
        var for_init = JSON.parse($scope.data);
        ........
        }
    }]);
```
