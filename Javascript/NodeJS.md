JavaScript (NodeJS)
===

## Table of Contents


   * [JavaScript (NodeJS)](#javascript-nodejs)
      * [Table of Contents](#table-of-contents)
      * [Notations](#notations)
      * [Introduction to ES6](#introduction-to-es6)
      * [Template Literal](#template-literal)
      * [Prototypal Inheritance and Function Constructors](#prototypal-inheritance-and-function-constructors)
      * [Immediately Invoked Function Expressions](#immediately-invoked-function-expressions)
      * [Multiple Files](#multiple-files)
      * [File System Module](#file-system-module)
      * [Event Emitter](#event-emitter)
      * [Call and Apply](#call-and-apply)
      * [Class Inheritance](#class-inheritance)
      * [ES6 Classes](#es6-classes)
      * [Event Emitter ES6](#event-emitter-es6)
      * [Callbacks](#callbacks)
         * [Finally](#finally)
      * [Readable and Writeable chunks](#readable-and-writeable-chunks)
      * [Using pipes](#using-pipes)
      * [First Simple Web Server](#first-simple-web-server)
      * [Outputting JSON](#outputting-json)
      * [Routing](#routing)


## Notations

**Dot**
person.greet();

**Bracket**
person.["firstname"]

## Introduction to ES6

Instead of using *var*, use *let* and *const*.

Object.create
```javascript
let person = {
    firstname: "",
    lastname: "",
    greet: function() {
        return this.firstname + ' ' + this.lastname;
    }
}

let john = Object.create(person);
john.firstname = 'John';
john.lastname = 'Doe';

console.log(john.greet());
```

To prevent an object from being changed:
```javascript
Object.freeze(obj)
```

Use *new* to create a new instance of an object
```javascript
new Date()
```

use .concat() to concatenate arrays
```javascript
const myConcat = (arr1,arr2) => {
  return arr1.concat(arr2)
}
```

Rest parameter or Spread operator (...)
*Rest parameter*: Collects all remaining elements into an array.
*Spread operator*: Allows iterables to be expanded into single arguemnts/elements

With the rest parameter (...), you can create functions that take a variable number of arguments. These arguments are stored in an array that can be accessed later from inside the function.
```javascript
const sum = (...args) => {
  return args.reduce((x, y) => x + y, 0); // the 0 means the summation is starting from zero.
}
```

Spread operator allows us to expand arrays
```javascript
const arr1 = ['JAN', 'FEB', 'MAR', 'APR', 'MAY'];
let arr2;
arr2 = [...arr1];
```

## Template Literal
Another way to concatanate strings
```javascript
let name = 'John';

let greet = "Hello " + name;
let greet2 = `Hello ${name}`; //use backticks

console.log(greet);
console.log(greet2);
```

## Prototypal Inheritance and Function Constructors

```javascript
function Person(firstname, lastname) {

    this.firstname = firstname;
    this.lastname = lastname;
    
};

Person.prototype.greet = function() {
    console.log('Hello, ' + this.firstname + ' ' + this.lastname);
};

let john = new Person('John', 'Doe');
var jane = new Person('Jane', 'Doe');
console.log(john.firstname);
john.greet();
console.log(john.__proto__);
console.log(john.__proto__===jane.__proto__);
```
Output:
John
Hello, John Doe
Object {greet: , constructor: }
true



## Immediately Invoked Function Expressions

```javascript
var firstname = 'Jane';

(function (lastname) {

	var firstname = 'John';
	console.log(firstname);
	console.log(lastname);
	
}('Doe')); // the outside brackets make this an expression

console.log(firstname);
```
Output:
John
Doe
Jane

## Multiple Files
How the tree looks like
```bash
├── app.js
└── greet
    ├── english.js
    ├── greetings.json
    ├── index.js
    └── spanish.js
```
Within app.js:
```javascript
var greet = require('./greet');

greet.english();
greet.spanish();
```
Within english.js
```javascript
var greetings = require('./greetings.json');

var greet = function() {
	console.log(greetings.en);
}

module.exports = greet;
```
Within spanish.js
```javascript
var greetings = require('./greetings.json');

var greet = function() {
	console.log(greetings.es);
}

module.exports = greet;
```
Within index.js
```javascript
var english = require('./english');
var spanish = require('./spanish');

module.exports = {
	english: english,
	spanish: spanish	
};
```
Within greetings.json
```json
{
	"en": "Hello",
	"es": "Hola"
}
```

## File System Module
To include the File system module so we can work with file systems on our computer
```javascript
let fs = require('fs');
```
**Note**
Both __dirname and ./ give similar results when node is running in the same directory as the currently executing file, but produces different results when node runs from some other directory.


```javascript
var fs = require('fs');

var greet = fs.readFileSync(__dirname + '/greet.txt', 'utf8'); 
console.log(greet); // readFileSYNC is synchronous

var greet2 = fs.readFile(__dirname + '/greet.txt', 'utf8', function(err, data) {
	console.log(data);
}); // asynchronous

console.log('Done!'); // V8 moves on to print this line 
// while the file from greet2 was being read.
```
**Output:**
Hello World!
Done!
Hello World!

Done is in between hello world, which came from greet.txt

## Event Emitter
When an EventEmitter object emits an event, all functions attached to that specific event are called synchronously.
```javascript
var EventEmitter = require('events');
var util = require('util');

function Greetr() {
    EventEmitter.call(this); //using EventEmitter as a Superconstructor
	this.greeting = 'Hello world!';
}

util.inherits(Greetr, EventEmitter);

Greetr.prototype.greet = function(data) {
	console.log(this.greeting + ': ' + data);
	this.emit('greet', data);
}

var greeter1 = new Greetr();

greeter1.on('greet', function(data) {
	console.log('Someone greeted!: ' + data);
});

greeter1.greet('Tony');

```

## Call and Apply
```javascript
var obj = {
	name: 'John Doe',
	greet: function()  // function does not take in any argument
		console.log(`Hello ${ this.name }`);
	}
}

obj.greet();
obj.greet.call({ name: 'Jane Doe'}); // function takes in none or a single argument
obj.greet.apply({ name: 'Jane Doe'}); // if function takes in an array as argument
```

## Class Inheritance
```javascript
var util = require('util');

function Person() {
	this.firstname = 'John';
	this.lastname = 'Doe';
}

Person.prototype.greet = function() {
	console.log('Hello ' + this.firstname + ' ' + this.lastname);
}

function Policeman() {
	Person.call(this);
	this.badgenumber = '1234';
}

util.inherits(Policeman, Person);
var officer = new Policeman();
officer.greet();
```

## ES6 Classes
```javascript

class Person {
	constructor(firstname, lastname) {
		this.firstname = firstname;
		this.lastname = lastname;
	}
	
	greet() {
		console.log('Hello, ' + this.firstname + ' ' + this.lastname);
	}
}

let john = new Person('John', 'Doe');
john.greet();

let jane = new Person('Jane', 'Doe');
jane.greet();

console.log(john.__proto__);
console.log(jane.__proto__);
console.log(john.__proto__ === jane.__proto__);
```

## Event Emitter ES6
```javascript
let EventEmitter = require('events');

class Greetr extends EventEmitter {
	constructor() {
		super();
		this.greeting = 'Hello world!';
	}
	
	greet(data) {
		console.log(`${ this.greeting }: ${ data }`);
		this.emit('greet', data);
	}
}

let greeter1 = new Greetr();

greeter1.on('greet', function(data) {
	console.log('Someone greeted!: ' + data);
});

greeter1.greet('Tony');
```

## Callbacks
A greet function that prints "Hi!", then callback a function given as input.
```javascript
function greet(callback) { //callback is a function
    console.log("Hi!");
    callback();
}
greet(function() {
    console.log('The callback was invoked.');
});
```
Since functions are first class in Js, they can be passed around like variables.
```javascript
function greet(callback) { 
    console.log("Hi!");
    callback();
}
greet(function() {
    console.log('The callback was invoked.');
});
greet(function() { //invoke greet() second time
    console.log('A different callback was invoked.');
});
```
**Output:**
Hi!
The callback was invoked.
Hi!
A different callback was invoked.

### Finally
My function could take some parameter, which passes it to the callback.
```javascript
function greet(callback) {
	console.log('Hello!');
	var data = {
		name: 'John Doe'
	};
	callback(data);
}

greet(function(data) {
	console.log('The callback was invoked!');
	console.log(data);
});

greet(function(data) {
	console.log('A different callback was invoked!');
	console.log(data.name);
});
```
**Output:**
Hello!
The callback was invoked!
Object {name: "John Doe"}
Hello!
A different callback was invoked!
John Doe

## Readable and Writeable chunks
```javascript
var fs = require('fs');

var readable = fs.createReadStream(__dirname + '/greet.txt', { encoding: 'utf8', highWaterMark: 16 * 1024 });
//highWaterMark is how much we want our chunk to be, 16 kilobytes
var writable = fs.createWriteStream(__dirname + '/greetcopy.txt');

readable.on('data', function(chunk) {
    console.log(chunk); // read from readable
    console.log(chunk.length);
    writable.write(chunk); // write to writeable .txt 
});
```
## Using pipes
We can use pipe to transfer data, making the code shorter and sweeter.
```javascript
var fs = require('fs');

var readable = fs.createReadStream(__dirname + '/greet.txt');

var writable = fs.createWriteStream(__dirname + '/greetcopy.txt');

readable.pipe(writable);
```

We can use a zip file with zlib
```javascript
var fs = require('fs');
var zlib = require('zlib');

var readable = fs.createReadStream(__dirname + '/greet.txt');

var writable = fs.createWriteStream(__dirname + '/greetcopy.txt');

var compressed = fs.createWriteStream(__dirname + '/greet.txt.gz');

var gzip = zlib.createGzip(); //creates a transformed stream
//Both readable and writeable

readable.pipe(writable);

readable.pipe(gzip).pipe(compressed);
// readable pipes to gzip, gzip pipes to compressed which is greet.txt.gz
```

## First Simple Web Server
```javascript
let http = require('http');

http.createServer(function (req, res) {
        // status code, wrap name in quotes, MIME type
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World\n');
// end means the last thing we are sending

}).listen(1337, '127.0.0.1');
```


With HTML
```javascript
var http = require('http');
var fs = require('fs');

http.createServer(function(req, res) {
    
    res.writeHead(200, { 'Content-Type': 'text/html' });
    var html = fs.readFileSync(__dirname + '/index.html', 'utf8');
    // var message = 'Hello World';
    // html = html.replace('{Message}', message);
    res.end(html);
    
}).listen(1337, '127.0.0.1');
```
With Pipe/Streams
```javascript
var http = require('http');
var fs = require('fs');

http.createServer(function(req, res) {
    // write to response stream
    res.writeHead(200, { 'Content-Type': 'text/html' });
    fs.createReadStream(__dirname + '/index.html').pipe(res);
    // create readable file stream, then pipe it to response 
    
}).listen(1337, '127.0.0.1');

```
index.HTML
```htmlembedded
<html>
	<head></head>
	<body>
		<h1>Hello World</h1>
	</body>
</html>
```

## Outputting JSON
```javascript
var http = require('http');
var fs = require('fs');

http.createServer(function(req, res) {
    
    res.writeHead(200, { 'Content-Type': 'application/json' });
                                        // MIME for JSON
    var obj = {
        firstname: 'John',
        lastname: 'Doe'
    };
    res.end(JSON.stringify(obj)); // converts obj into string
    // so it can be displayed correctly on webpage  
    
}).listen(1337, '127.0.0.1');
```

## Routing
```javascript
var http = require('http');
var fs = require('fs');

http.createServer(function(req, res) {
    
    if (req.url === '/') {
        fs.createReadStream(__dirname + '/index.html').pipe(res);
    }
    
    else if (req.url === '/api') {
        res.writeHead(200, { 'Content-Type': 'application/json' });
        var obj = {
            firstname: 'John',
            lastname: 'Doe'
        };
        res.end(JSON.stringify(obj));
    }
    else {
        res.writeHead(404);
        res.end();
    }
    
}).listen(1337, '127.0.0.1');
```





