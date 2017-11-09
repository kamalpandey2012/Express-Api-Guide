# Express API Guide with Examples. 


## Topics to be covered
- Routing 

## Routing
### Basic Routing 
**Routing** refers to determing how an application responds to client request to a particular endpoint, which is a URL or path and some HTTP method such as 
GET, POST and so on.

Each router has one or more handler function which runs when a route is matched.

Basic example 

```
app.METHOD(PATH, HANDLER);
```

- `app` is instance of the application. 
- `METHOD` is an HTTP REQUEST METHOD
- `PATH` is a path to the server
- `HANDLER` is function which is executed when route matches.

The following example simulate the most basic route 'Hello world' of Node application

```
app.get('/', function(req, res){
	res.send('Hello World');
});
```
Here the path is '/' which represents 'home' of the application so when the user tries to navigate to ( or request home URL) then express will call a function which has two parameter 'req', 'res' returned from the node. 

The 'req' parameter is 'request' parameter that we will get from the 'client'. 
The 'res' parameter is 'response' parameter that we will send to the client. Here we are sending a string with 'Hello World' content. 

Below are some more examples of routing 

- Response to post request at root or home 

	```	
		app.post('/', function(req, res){
			res.send('Received POST request');
		});
	```
- Response to put request at '/user' route

	```
		app.put('/user', function(req, res){
			res.send('Post request from \user');
		});
	```
- Response to 'delete' request at '/user'

	```
		app.delete('/user', function(req, res){
			res.send('Delete request at \user'
		});
``` 

### Routing method
Express supports the following routing methods derived from the HTTP methods
	
- get
- post
- put 
- head
- delete
- options
- trace
- copy
- lock
- mkcol
- move
- purge
- unlock
- report
- mkactivity
- checkout
- merge
- m-search
- notify
- subscribe
- unsubscribe
- patch and search

The routing methods that are invalid javascript names use bracket notation such as `app[m-search],...`

There is a special routing method `app.all()` which is not part of HTTP methods. It is used for loading middleware function for all request methods. 

In the following function the app.all is running when '/secret' path is requested for all the HTTP methods like Get, post etc

```
app.all('/secret', function(req, res, next){
	console.log('Serving secret section');
	next(); //pass control to the next handler
});
```

### Routing paths
Routing path could be **strings, string patterns or regular expressions**

The character ?, +, * and () are subsets of the regular expression counterparts. The (-) hyphen and the dot(.) are interpreted literallly by string based paths.

If you need to use the dollar character ($) in a path string, enclose it escaped within ([ and ]). For example path string for request at "/data/$book" would be "/data/([\$\)book"

Here are some examples of routes based on string

- Route will match to **random.text**
	
	```
	app.get('random.text', function(req, res){
		res.send('random.text');
	});
	```
- This route will match **'acd', 'abcd'**

	```
	app.get('/ab?cd', function(req, res){
		res.send('abc or abcd');
	});
	```
- This route will match **abcd, abbcd, abbbcd**
	
	```
	app.get('/ab+cd', function(req, res){
		res.send('abcd, abbcd, abbbcd...');
	});
	```
- This route will match **abcd, abxcd, abrandomcd, ab1122cd**
	
	```
	app.get('/ab*cd', function(req, res){
		console.log('abRANDOMcd');
	});
	```
- This route will match **abe, abcde**
	
	```
	app.get('/ab(cd)?e', function(req, res){
		console.log('abe, abcde');
	});
	```
	
Examples of route path based on **regex** (Regular expressions)	

- This route path will select anything **with 'a'** in the name
	
	```
	app.get(/a/, function(req, res){
		console.log('/a/');
	});
	```	 	
- This route path will match **butterfly and dragonfly** but not butterflyman and dragonflyman
	
	```
	app.get(/.*fly$/, function(req, res){
		console.log('butterfly not butterflyman');
	});
	```
	Here anything before fly and at the end only fly represented by $. This is part of the regex whose couse is **coming soon**.
	
### Route Parameters

The route parameters like any other parameter is used to transfer data between 2 or more clients or server

For example 

Route path

```
/user/:userID/books/:booksId	
```

here semicolor (:) reqpresents variable, next step will be to retrieve it.

```
app.get('/user/:userId/books/:booksId', function(req, res){
	res.send(req.params);
});
```


### Route Handlers

The route handlers are used to herd the traffic to a specific function. These could be in form of a function, array of functions, or combination of both. 

More than one callback function could handle the route

```
app.get('/example/a', function(req, res, next){
	res.send('The requst will be send to the next...');
	next();
},function(req, res){
	res.send('this is from b');
});
```
An array of callbacks could also be provided

```
let cb0 = function(req, res, next){
	console.log('cb0');
	next();
	}
	
let cb1 = function(req, res, next){
	console.log('cb1');
	next();
	}
let cb2 = function(req, res, next){
	console.log('cb2');
	}
	
app.get('/example/a', [cb0, cb1, cb2]);	

```

A combination of independent function and array could handle the routes

```
let cb0 = function(req, res, next){
	console.log('cb0');
	next();
	}
let cb1 = function(req, res, next){
	console.log('cb1');
	next();
	}
	
app.get('/example/a',[cb0, cb1],function(req, res, next){
	console.log('let next function execute and give you the output');
	next();
}, function(req, res){
console.log('last to execute');
});				
```

### Response methods 

-  `res.download()` prompt a file to be downloaded
-  `res.end()` End the response process
-  `res.json()` Send a JSON response
-  `res.jsonp()` Send a json response with jsonp support
-  `res.redirect()` Redirect a request
-  `res.render()` Render a view template
-  `res.send()` Send a response of various types eg txt, .html 
-  `res.sendFile()` Send a file as an octet stream
-  `res.sendStatus()` Set the response code eg with 500, 404 etc

### `app.route()`
It could be used to chain the routes for example 

```
app.route('/book')
.get(function(req, res){
	console.log('Get some random books');
}) 
.put(function(req, res){
	console.log('update a random book');
})
.post(function(req, res){
	console.log('Add a random book');
});
```

### Express Router

Use 'Router' class to create modular mountable route handlers. A router instance is a complete middleware and routing system thats why it is called 'mini-app'. 
Its a great way to organise a large application hence we will be using this kinda approach into our application

```
import express from 'express';

let router = express.Router();

router.use(function timelog(req, res, next){
	console.log('Time: ', Date.now());
	next();
});

router.use(function auth(req, res, next){
	if(userAuthorized){
		next('admin');
	}
	else{
		return res.status('Unauthorized : Error 401 !');
		//or return res.render(404.html);
		}
});

router.get('/users', function getUsers(req, res, next){
	res.send('return list of users');
});

router.put('/users', function putUsers(req, res, next){
	res.send('user updated');
});

```
