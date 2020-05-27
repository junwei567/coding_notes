# JavaScript Notes

Math Syntax
---
```javascript
// Math.random() returns a random float in the range 0 to less than 1
console.log(Math.floor(Math.random() * 100)) // 0 to 99
console.log(Math.ceil(43.8)) //44

Math.floor(Math.random() * (myMax - myMin + 1)) + myMin;

// convert string to int
parseInt('007')
// second argument is for radix e.g. base 2 for binary
parseInt('11',2) 
```

```
console.log(Number.isInteger(2017))
```

Manipulate Arrays
---
The push() method adds an item to the end of an array.
The unshift() method adds an item to the front of an array.
The shift() method removes the first element of an array. It also returns the element removed.
The pop() method removes the last element of an array. It also returns the element removed.

Scopes
---
Declaring a variable without 'var' will make it a global variable
Local variable takes precedence over other variable in a function

If Else Statement
---
```javascript
function testSize(num) {
  
  if (num < 5) {
    return "Small";
  } 
  else if (num < 10) {
    return "Medium";
  } 
  else {
    return "Large";
  }
}
```

Switch Statement
---
```javascript
function caseInSwitch(val) {
  var answer = "";
 
  switch(val) {
    case 1:
      answer = "alpha";
      break;
    case 2:
      answer = "beta";
      break;
    case 3:
      answer = "gamma";
      break;
    case 4:
      answer = "delta";
      break;
      
    default:
      answer = None;
  }

  return answer;
}
```

Conditional (Tenary) Operator
---
```javascript
function checkSign(num) {
  return (num > 0) ? "positive"
    : (num < 0) ? "negative"
    : "zero"
}
```

Equality
---
```javascript
Strictly equal:     ===    
Strictly not equal: !==

Equal (Type coercion): ==
Not equal:  !=
```

Objects
---
.typeof()
```javascript
typeof(myObject['example'])
```
.hasOwnProperty() returns a boolean
```javascript
hasOwnProperty(myObject['example'])
if 'example' in myObject
```

While Loop
---
```javascript
var myArray = [];
var i = 5;

while(i >= 0) {
  myArray.push(i);
  i--;
}
```

For Loop
---
```javascript
var myArray = [];

for (var i = 1; i < 6; i++) {
  myArray.push(i);
}
```
```javascript
var myArr = [ 2, 3, 4, 5, 6];
var total = 0;

for (var i = 0; i < myArr.length; i++) {
  total += myArr[i];
}
```

Nested For loop
---
```javascript
function multiplyAll(arr) {
  var product = 1;
  
  for (var i = 0; i < arr.length; i++) {
    for (var j = 0; j < arr[i].length; j++) {
      product = product * arr[i][j];
    }
  }
  return product;
}

multiplyAll([[1, 2], [3, 4], [5, 6, 7]]);
```

Do... While Loop
---
Loop will be executed at least once.
```javascript=
var myArray = [];
var i = 10;

do {
  myArray.push(i);
  i++;
} while (i <= 10);
```

Recursion
---
```javascript=
function sum(arr, n) {
  if (n <= 0) {
    return 0;
    } 
  else {
    return sum(arr, n - 1) + arr[n - 1];
    }
}
```

Examples
---
### Q1
We have an array of objects representing different people in our contacts lists.

A lookUpProfile function that takes name and a property (prop) as arguments has been pre-written for you.

The function should check if name is an actual contact's firstName and the given property (prop) is a property of that contact.

- If both are true, then return the "value" of that property.

- If name does not correspond to any contacts then return "No such contact".

- If prop does not correspond to any valid properties of a contact found to match name then return "No such property".

```javascript=
var contacts = [
    {
        "firstName": "Akira",
        "lastName": "Laine",
        "number": "0543236543",
        "likes": ["Pizza", "Coding", "Brownie Points"]
    },
    {
        "firstName": "Harry",
        "lastName": "Potter",
        "number": "0994372684",
        "likes": ["Hogwarts", "Magic", "Hagrid"]
    },
    {
        "firstName": "Sherlock",
        "lastName": "Holmes",
        "number": "0487345643",
        "likes": ["Intriguing Cases", "Violin"]
    },
    {
        "firstName": "Kristian",
        "lastName": "Vos",
        "number": "unknown",
        "likes": ["JavaScript", "Gaming", "Foxes"]
    }
];

function lookUpProfile(name, prop){

for (var i = 0; i < contacts.length; i++) {
    if (name == contacts[i]["firstName"]) {
        if (prop in contacts[i]) {
            return contacts[i][prop]
        }
        else {
            return "No such property"
        }
    }    
}
return "No such contact"
}

lookUpProfile("Kristian", "lastName");
```

### Q2

You are given a JSON object representing a part of your musical album collection. Each album has several properties and a unique id number as its key. Not all albums have complete information.

Write a function which takes an album's id (like 2548), a property prop (like "artist" or "tracks"), and a value (like "Addicted to Love") to modify the data in this collection.

- If prop isn't "tracks" and value isn't empty (""), update or set the value for that record album's property.

Your function must always return the entire collection object.

There are several rules for handling incomplete data:

- If prop is "tracks" but the album doesn't have a "tracks" property, create an empty array before adding the new value to the album's corresponding property.

- If prop is "tracks" and value isn't empty (""), push the value onto the end of the album's existing tracks array.

- If value is empty (""), delete the given prop property from the album.

```javascript
var collection = {
  2548: {
    album: "Slippery When Wet",
    artist: "Bon Jovi",
    tracks: [
      "Let It Rock",
      "You Give Love a Bad Name"
    ]
  },
  2468: {
    album: "1999",
    artist: "Prince",
    tracks: [
      "1999",
      "Little Red Corvette"
    ]
  },
  1245: {
    artist: "Robert Palmer",
    tracks: [ ]
  },
  5439: {
    album: "ABBA Gold"
  }
};
```
```javascript
function updateRecords(id, prop, value) {
  if (prop != "tracks") {
    if (value != "") {
      collection[id][prop] = value
    }
  }

  if (prop == "tracks") {
    if (!("tracks" in collection[id])) {
      collection[id][prop] = [value];
    }
    else if ("tracks" in collection[id]) {
      collection[id]["tracks"].push(value)
    }
  }

  if (value == "") {
    delete (collection[id][prop])
  }
  return collection;
}
```

### Q3

We have defined a function called countdown with one parameter (n). The function should use recursion to return an array containing the integers n through 1 based on the n parameter. If the function is called with a number less than 1, the function should return an empty array. For example, calling this function with n = 5 should return the array [5, 4, 3, 2, 1]. Your function must use recursion by calling itself and must not use loops of any kind.

```javascript=
function countdown(n){
  if (n < 1) {
    return [];
  } else {
    var countA = countdown(n - 1);
    countA.unshift(n);
    return countA;
  }
}
```

### Q4

We have defined a function named rangeOfNumbers with two parameters. The function should return an array of integers which begins with a number represented by the startNum parameter and ends with a number represented by the endNum parameter. The starting number will always be less than or equal to the ending number. Your function must use recursion by calling itself and not use loops of any kind. It should also work for cases where both startNum and endNum are the same.

```javascript=
function rangeOfNumbers(startNum, endNum) {
  if (startNum == endNum) {
    return [startNum];
  } else {
    var arr = rangeOfNumbers(startNum, endNum-1);
    arr.push(endNum);
    return arr;
  }
}

```
