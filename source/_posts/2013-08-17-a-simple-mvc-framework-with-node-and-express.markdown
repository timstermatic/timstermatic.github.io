---
layout: post
title: "A simple MVC framework with node and express"
date: 2013-08-17 10:59
comments: true
categories: 
---
I love frameworks. As soon as I dropped my programmer's ego and learned to embrace well conceived conventions over configuration my development and deployment times felt the benefit. On the other hand, I like understanding what is going on underneath the hood which there is a danger of losing sight of when using a framework.

And this brings me to why I love node.js and express. For me they provide an boilerplate for a framework: the tools I need to quickly build my own conventions.

For sure, it's pretty easy to build a site with express as it is, but shifting express closer to a [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) setup is simple.

### MVC in layman's terms.

When I used my first [MVC framework](http://www.cakephp.org) I got bit bad. It's not difficult to see why as a pattern it has stood the test of time. And here is all you need to know to start out:

* M is for model. A place to define data structures and methods to interact with your data store.
* V is for view. A place to manage everything the end user sees on his or her screen.
* C is for controller. A place to take user requests, bring data from the model and pass it back to the view.

And that's enough of that for now. Here's how I arrange my express apps into a basic MVC structure.

First of all I need three folders in the root of my app.

* models
* views
* controllers

Now on to the app.js

``` javascript 
var express = require('express');
		,http = require('http');
		,path = require('path');
		,app = express();
		,fs = require('fs');

// database connection
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/mydb');

// some environment variables
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'jade');
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.bodyParser());
app.use(express.methodOverride());
app.use(express.cookieParser('your secret here'));
app.use(express.session());
app.use(app.router);
app.use(express.static(path.join(__dirname, 'public')));

// dynamically include routes (Controller)
fs.readdirSync('./controllers').forEach(function (file) {
	if(file.substr(-3) == '.js') {
		route = require('./controllers/' + file);
		route.controller(app);
	}   
});

http.createServer(app).listen(app.get('port'), function(){
	console.log('Express server listening on port ' + app.get('port'));
});

```

Much of this file is standard if you are generating your app with express, but there are some important lines. Line 5 is where we include the native `fs` module so we can dynamically read in our controllers. Then in lines 25 to 30 we read in all js files from the controllers directory. Line 13 is pretty important too as it tells express where we want to store our views.

Now whenever we add a js file to our controllers directory we can format it in such a way that it will contain a group of routes. Here is an example that I place in `controllers/users.js`

```
var mongoose = require('mongoose')
var Video = require('../models/user');
module.exports.controller = function(app) {

/**
 * a home page route
 */
	app.get('/signup', function(req, res) {
		// any logic goes here
		res.render('users/signup')
	});

/**
 * About page route
 */
	app.get('/login', function(req, res) {
		// any logic goes here
		res.render('users/login')
	});

}
```
				
I choose to include my models on a per controller basis (`lines 1 to 2`), so in my MVC structure they are required in each controller. As modules are cached the first time they are loaded there is no overhead in doing this. Moreover I like having the reference to the models I am using in the file I am using them in.

Line 3 exports all of our routes in the controller function. From here on in you can use the express way of defining routes. But notice that I set my views like this: `res.render('users/signup')` which results in the view being loaded from `views/users/signup.jade` in this app.

Finally, for reference, here is what the model in `models/user.js` may look like:

```
Var mongoose = require('mongoose')
		,Schema = mongoose.Schema
		userSchema = new Schema( {
			username: String,
			password: String 
		}), 
User = mongoose.model('user', userSchema);

module.exports = User;
```


I like doing things this way because the controller is mediating both the models and the views which for me is the spirit of MVC. There is also a huge organisational win too: if I want to work on users/signup I know I that my model is in `models/user.js`; my route is in `controllers/users.js` and my view is in `views/users/signup.jade`. And that is what makes MVC such a great pattern.
