from [CI, Smarty, jQuery] to [CI, Angular] Guide
==============================

##First Step: Remove Smarty

###Controller
--
####1. Parse Tpl -

*[CI, Smarty, jQuery] way*

    $this->parser->parse('dirPath/file.tpl', $data);

*[CI, Angular] way*

    $this->load->view('dirPath/file.php', $data);

###View
--
####1. File Ext -

    file.tpl -> file.php



####2. Include File -

*[CI, Smarty, jQuery] way*

    {include file="dirPath/file.tpl"}

*[CI, Angular] way*

    <?php include APPPATH.'views/dirPath/file.php'; ?>



####3. Include Var -

*[CI, Smarty, jQuery] way*

    {$var}

*[CI, Angular] way*

    <?=$var?>



####4. If / else -
*[CI, Smarty, jQuery] way*

    {if codition}
        <input name="test" value="true">
    {else}
        <input name="test" value="false">
    {/if}


*[CI, Angular] way*

    <?php if (condition) { ?>
        <input name="test" value="true">
    <?php } else { ?>
        <input name="test" value="false">
    <?php } ?>



####5. foreach -
*[CI, Smarty, jQuery] way*

    {foreach key=i from=$data_list item=data}
        <li data-key="{$i}">{$data}</li>
    {/foreach}


*[CI, Angular] way*

    <?php foreach ($data_list as $i => $data) { ?>
        <li data-key="<?=$i?>"><?=$data?></li>
    <?php } ?>


##Second Step: Start Angular

####1. Add Script (at html head)
*need add others for another usages*

    <script src="//ajax.googleapis.com/ajax/libs/angularjs/1.2.8/angular.min.js"></script>
    <script src="//ajax.googleapis.com/ajax/libs/angularjs/1.2.8/angular-route.min.js"></script>

*js for this page*

    <script src="dirPath/js/thisPage.js"></script>

####2. Add App Scope (at body)

    <body ng-app="app">
    
####3. Do thisPage.js Init

    var app = angular.module('app', ['ngRoute']);
    
####4. Page Route
*body div for view*

    <div ng-view>

*html body bottom*

    <script id="part1.html" type="text/ng-template">
        <?php include APPPATH.'views/filePath/firstPart.php'; ?>
    </script>
    <script id="part2.html" type="text/ng-template">
        <?php include APPPATH.'views/filePath/secondPart.php'; ?>
    </script>
    <script id="part3.html" type="text/ng-template">
        <?php include APPPATH.'views/filePath/thirdPart.php'; ?>
    </script>
    
*thisPage.js*

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

####5. Add Controller 

    app.controller('FirstPartControl', ['$scope', function($scope) {
        $scope.datas = [{key: value1}, {key: value2}];
        $scope.addData = function(data) {
            $scope.datas.push(data);    
        }
    }]);

####6. Controller for redirect (add param $location)

    app.controller('SecondPartControl', ['$scope', '$location', function($scope, $location) {
        $scope.toPart1 = function() {
            $location.path('/part1');
        };
    }]);
    
####7. Controller for ajax (add param $http)

    app.controller('thirdPartControl1', ['$scope', '$http', function($scope, $http) {
        $scope.ajax = function() {
            $http({
                url: 'http://www.example.com/targetUrl',
                method: 'POST',
                data: {"foo": "bar"}
            }).success(function(data, status, headers, config) {
                $scope.data = data;
            }).error(function(data, status, headers, config) {
                $scope.status = status;
            });
        };
    }]);
    
###8. Share Data betwwen Controllers
*factory*

    app.factory('data', [function () {
        var Data = {
                dataValue: '',
                getDataValue: function() {
                    return this.dataValue;
                },
                setDataValue: function(dataValue) {
                this.dataValue = dataValue;
            }
        };
        return Data;
    }]);

*controller1*

    app.controller('FirstPartControl', 'data', ['$scope', function($scope, $data) {
        $scope.datas = $data.getDataValue();
    }]);

*controller2*

    app.controller('secondPartControl', 'data', ['$scope', function($scope, $data) {
        $scope.doSomeThing = function() {
            $data.setDataValue($scope.data);
        };
    }]);
