---
title: AngularJS Learning
date: 2017-12-10 20:52:45
categories: AngularJS
tags: [AngularJS, notes]
---

Learning Material: [AngularJS tutorial for beginners](https://www.youtube.com/watch?v=zKkUN-mJtPQ&list=PL6n9fhu94yhWKHkcL7RJmmXyxkuFB3KSl)

## 1. What is AngularJS

- JavaScript framework
- Help to build web application
- MVC

<!--more-->

### Two requirements to start using AngularJS
1. Copy `angular.min.js` in and insert to html
```
<head>
	<script src="scripts/angular.min.js"></script>	<!-- 1st requirement -->
</head>
```
2. insert `ng-app` to activate angular
```
<body ng-app>	<!-- 2nd requirement: activate angular -->
	<div>
		10+20={{10+20}}
	</div>
</body>
```

### Demo
```
10+20={{10+20}}
{{1==2}}
{{ {name: 'Ryan', age: '30'}.name }}
{{ ['David','Lucy','Shell'][2] }}	#Shell

```

## 2. Modules and Controllers

### Intro
1. Module
	- a container: controller, service, directive, filter..
	- as a main() method
2. Create a module
	```
	var myApp = angular.module("myModule",[]);	# ModuleName & its dependency
	```
3. Controller
	- a JS function
	- to build a model for the view to display
	- model is data
4. Create a controller
	```
	var myController = function ($scope) {
		$scope.message = "Hello world..";
	}
	```

!! $scope is NOT model. The data attached is the model.

### Demo

- script.js
	```
	var myApp = angular.module("myModule",[]);	// 1. create module

	myApp.controller("myController", function ($scope) {	// 2. create controller
		$scope.message = "Hello world..";
	}); 

	```
- index.html
	```
	<head>
		<script src="scripts/script.js"></script>
	</head>

	<body ng-app="myModule">
		<div ng-controller="myController">
			{{ message }}
		</div>
	</body>
	```

## 3. Controllers - cont

### Attach a complex object to the scope
```
myApp.controller("myController", function ($scope) {
	
	var customer = {
		firstName: "ryan",
		lastName: "luo",
		gender: "male"
	};				// create object
	
	$scope.customer = customer;	// attach object to scope

}); 
```

### In the view:
```
<body ng-app="myModule">
	<div ng-controller="myController">
		
		<div>first name: {{ customer.firstName }}</div>
		<div>last name: {{ customer.lastName }}</div>
		<div>gender: {{ customer.gender }}</div>
		
	</div>
</body>
```

- misspell controller -> Error
- misspell object property -> Null

### Method chaining to combine controller into module:
- Module:
	```
	var myApp = angular.module("myModule",[]);
	```
- Controller:
	```
	myApp.controller("myController", function ($scope) {
	
		var customer = {
			firstName: "ryan",
			lastName: "luo",
			gender: "male"
		};
		$scope.customer = customer;

	}); 
	```
- Combine:
	```
	var myApp = angular
			.module("myModule",[])
			.controller("myController", function ($scope) {
				var customer = {		
					firstName: "ryan",
					lastName: "luo",
					gender: "male"
				};
				$scope.customer = customer;
			}); 
	```

## 4. Directive: ng-src

- add `image` folder to project with an image
- use `ng-scr` to ensure a request is issued only after NG has evaluated.
- no 404 error

### JS
```
var myApp = angular
		.module("myModule",[])
		.controller("myController", function ($scope) {
			var topic = {		
				name: "Git",				
				picture: "image/Git-Cycle1.png"		#source-path
			};
			$scope.topic = topic;
		}); 
```

### HTML
```
<div ng-controller="myController">
	
	<div>
		Topic : {{ topic.name }}
	</div>
	
	<img ng-src="{{ topic.picture }}" 		#dynamic
		alt="{{ topic.name + ' picture' }}"			
		style="height:300px; width:400px"/>
	
</div>
```


## 5. Two-way DataBinding & ng-model

### Two-way data binding
- keep model and view in sync at all time
- `$scope.message` won't change
- `ng-model="message"` and `{{ message }}` will keep the same
- model takes value from js initially. Once change, model value changes. 

```
var myApp = angular
		.module("myModule",[])
		.controller("myController", function ($scope) {
		
			$scope.message = "Hello World.."
		
		}); 

<div ng-controller="myController">
	
	<input type="text" ng-model="message"/>
	<br/><br/>	
	{{ message }}
	
</div>

```

### ng-model
used for
- input
- select
- textarea

### Demo

```
# JS
var myApp = angular
		.module("myModule",[])
		.controller("myController", function ($scope) {
			
			var customer = {
					name: "ryan",
					age: "29"
			};
			
			$scope.customer = customer;
		}); 


# HTML
<div ng-controller="myController">
	<table>
		<tr>
			<td>Name</td>
			<td><input type="text" ng-model="customer.name"/></td>
		</tr>
		<tr>
			<td>Age</td>
			<td><input type="text" ng-model="customer.age"/></td>
		</tr>
	</table>
	<br/>
	<table>
		<tr>
			<td>Name</td>
			<td>{{ customer.name }}</td>
		</tr>
		<tr>
			<td>Age</td>
			<td>{{ customer.age }}</td>
		</tr>
	</table>
</div>
```

Result:
![ng-model](/images/ng-model.png)





