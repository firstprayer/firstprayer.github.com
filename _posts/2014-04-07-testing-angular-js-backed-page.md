---
layout: post
title: "Testing AngularJS-Backed Web Pages"
description: ""
category: ""
tags: []
---
{% include JB/setup %}

Half a year ago I started to use AngularJS. As a MVC framework, its learning curve is a little ugly but really enjoyable to use once one has understood how it works. What's more, it's convenient to test. Here is my experience about testing AngularJS-backed web pages inside Django(or any other web framework, since only js files are involved).   

**Notice: I write tests after I've finished the web app inside Django. But perhaps this is not the best approach to development angular applications. Tests go first seems to be quite useful in my recent practises.**

#### Here is how it goes:  
##### Part One: Unit test  
##### 1. Environment  
One need to set up [Karma](http://karma-runner.github.io/0.12/index.html) to run unit tests. In my practise, I install karma-cli and install all module globally so I could run the test anywhere I want. It's simple to do this, just install with a parameter **-g** like "npm install karma -g".  
##### 2. Project set up   
I recommend to use Angular-Seed to set up a test for a specific page, for a very simple reason: I'm lazy. But if you want to understand exactly how to config karma and other details, you can start from scratch, it's not difficult at all.   
Now let's take a look at the config file first:  

	module.exports = function(config){
	    config.set({
	    basePath : '../',

	    files : [
	      'app/lib/angular/angular.js',
	      'app/lib/angular/angular-*.js',
	      'test/lib/angular/angular-mocks.js',
	      '../../../../static/assets/plugins/jquery-1.8.3.min.js',
	      '../../../../static/assets/js/purl.min.js',
	      '../../../../static/assets/scripts/angular-sheet-common.js',
	      '../../../../static/assets/scripts/app/*.js',
	      {'pattern': 'mock/data/data.json', 'included': false},
	      'test/unit/**/*.js'
	    ],

	    exclude : [
	      'app/lib/angular/angular-loader.js',
	      'app/lib/angular/*.min.js',
	      'app/lib/angular/angular-scenario.js'
	    ],

	    autoWatch : true,

	    frameworks: ['jasmine'],

	    browsers : ['Chrome'],

	    plugins : [
	            'karma-junit-reporter',
	            'karma-chrome-launcher',
	            'karma-firefox-launcher',
	            'karma-jasmine'
	            ],

	    junitReporter : {
	      outputFile: 'test_out/unit.xml',
	      suite: 'unit'
	    }

	})}

**basePath**: The root directory to run the test  
**files**: What do you want to include during the test. You can put all library dependencies, your application scripts and your test scripts here. You can also include some fake data(by setting 'included' to false, you tell karma to include the file but not take it as a script file) here for testing.    
**exclude**: You know.  
**autoWatch**: If you set this to true, then each time your code is changed, the test will be runned again automatically.   
**framework**: Test framework. [Jasmine](http://jasmine.github.io) is a good choice.  
**browers**: The emulator environment.  
**plugins**: You can add some extra plugins here. For example, you can add karma-converage to test the code converage.  
You can see that I want to my test suite to follow up with the lastest application scripts, so I put the test codes within the same reposity of the website and access application scripts by relative path.   

##### 3. Write the test!  
First, we use jasmine here, so if you're not familiar with the syntax, you can take a look at [here](http://jasmine.github.io).  
Now, we skip the simple tutorial of jasmine and directly write unit test suites for controllers.  

	'use strict';

	/**
	* First, we need to load fake data, so that we can use httpBackend to construct response
	*/
	var globalData;

	var URL_GET_DATA = '/images'; // Suppose this is the url you call for data in real world application

	$.ajaxSetup({'async': false});
	$.getJSON('base/mock/data/data.json') // Remember to add a 'base' before the string!!
	    	.done(function(data){
	    		globalData = data;
	    	})
	    	.fail(function(res){
	    		throw new Error('LOAD DATA FAIL');
	    	});
	$.ajaxSetup({'async': true});

	/* jasmine specs for controllers go here */

	describe('controllers', function(){

		var scope, httpBackend;

		beforeEach(module('App.Controllers'));

		// Before each test, we initialize the global scope
		beforeEach(inject(function($injector) {
	        httpBackend = $injector.get('$httpBackend');

	        var $rootScope = $injector.get('$rootScope');
	        scope = $rootScope.$new();

	        var $controller = $injector.get('$controller');

	        $controller('DemoCtrl', {
	            '$scope': scope
	        });

	        httpBackend.when('GET', URL_GET_DATA)
				.respond(globalData);

			/**
			* This is part is necessary to trigger the http get request, and ask the fake back end to return data
			*/
			scope.$apply(function(){

			});
			httpBackend.flush();

	    }));

		it('should load the data correctly', function(){
				// Here the scope has been initialized
				// You can access the scope and start to test!
				expect(scope.images).toBeDefined();
			});  
	});


In your application js code  
	function DemoCtrl(scope, http){
		http.get('/images')
			.then(function(data){
				scope.images = data;
			});
	}

The codes above show how to:  
1. Construct a fake backend, so when you use http.get and http.post inside you code, the request would be redirect to the fake backend, and you can total control the behavior of the backend within you test scripts.  
2. How to initialze controller and get the responding scope.  

As long as your know how to initialize and access the scope object, you can test the logic of your application code by calling functions and manipulating model binded to scope.   

Now let's take a look at back end validation:  

This is code for saving data in your application code  
	http.post('/save', {'name': 'Taylor', 'id': 20});

This is the code for getting posted data with httpBackend  
	httpBackend.when('POST', '/save')
				.respond(function(method, url, data, headers){
					data = JSON.parse(data);
					expect(data.name).toBeDefined();
					expect(data.id).toBeDefined();

					return [200, {'success': true}]; // This is a success request
				});


In the second piece of code, you can do anything about the request and respond with anything you like, so it would be a good way to test if your code submit the data correctly.  

##### 4. Running multiple tests  
Now in my project there're many pages that need tests. I want to test all of them by running one simple command. A good choice to integrating multiple tests (multiple karma config file) is to use grunt.    

Here is an example about how to config multiple jobs in grunt to run all the tests.

	module.exports = function(grunt){
		grunt.initConfig({
	    	'pkg': grunt.file.readJSON('package.json'),
	    	'karma': {
	    		'app1': {
	    			'configFile': '../app1/config/karma.config.js',
	    			'singleRun': true
	    		},
	    		'app2': {
	    			'configFile': '../app2/config/karma.config.js',
	    			'singleRun': true
	    		}
	    	}
	    });
		
		grunt.loadNpmTasks('grunt-karma');
	}

##### Part 2: E2E Test
I will add this section later.  