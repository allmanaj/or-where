# OrWhere
OrWhere is a Laravel inspired filter builder for Javascript arrays. It allows the dynamic building of complex filters using the `where`, `orWhere`, `whereIncludes` and `whereIn` methods.

## Example
OrWhere allows the writing of more user-friendly code when filtering through a large arrays of Objects (e.g. information from a database). Here's an example without and with orWhere:

### Without orWhere
```
users.filter(user => {
    return (user.name == 'Angus' || user.name == 'Jess' || user.name == 'Aaron') && user.age >= 25;
});

```

### With orWhere
```
filter = FilterBuilder(users);
filter.whereIn('name', ['Angus', 'Jess', 'Aaron'])
      .where('age', '>=', 25)
      .get()
```

## Installation

```
npm i --save or-where
```
or
```
yarn add or-where
```

## Setup

To set up use of orWhere's `FilterBuilder` your file may look like the following

```
// CommonJS
var orWhere = require('or-where');
var filterBuilder = new orWhere.FilterBuilder([data])

//ES6+
import {FilterBuilder} from 'or-where';
let filterBuilder = new FilterBuilder([data])
```

## Methods

In order to provide a bit of context I'll be using the following dataset all examples 

```
const data = [
    {
        name: 'Angus',
        age: 24,
        hobbies: [
            {name: 'swimming', hoursNeeded: 2},
            {name: 'running', hoursNeeded: 1}
        ]
    },
    {
        name: 'Jess',
        age: 25,
        hobbies:[
            {name: 'swimming', hoursNeeded: 2},
            {name: 'reading', hoursNeeded: 3},
        ]
    },
    {
        name: 'Aaron',
        age: 25,
        hobbies:[
            {name: 'go-karting', hoursNeeded: 3}
            {name: 'reading', hoursNeeded: 1}
        ]
    }
];
```

### where
The `where` method is the most basic filter. It matches an object based on the parameters you pass to it. When you chain `where` calls they act as an `and`(`&&`) operator between conditions. You can use `where` in four different ways.

```
where(keyName)
```
This usage checks that the value in the assigned keyname evaluates to true. e.g. `where('name').get()` would check that the name value in the object is not falsy (false, 0, "", null, undefined)

```
filterBuilder.where(keyName, value)
```
This usage assumes that the operation that the user wants is `===`. Therefore `where('name', 'Angus).get()` would check that the name value in the object is equal to `'Angus'`.

```
filterBuilder.where(keyName, operation, value)
```
This allows the user to specify the `keyName`, `operator`, and `value`. e.g `where('age', '>=', 25).get()` would return only the objects in the array with a `age` key which has a value greater than or equal to `25`.

```
filterBuilder.where(query => {});
```
This allows the user to scope conditions so that they only compare with the other conditions in the closure. See the example below for more detail.

```
filterBuilder.where(query => {
    query.where('name', 'Angus')
         .orWhere('name', 'Jess')
})
.where('age', '>=', 25)
.get();
```
This would filter out any items where then name is neither `Angus` nor `Jess` and the age is under 25. so 
```
[{name: 'Jess', age: 25, ...truncated}]
```
would be returned.

### orWhere
This can be used in the exact same way as `where()` however it acts as an `or` (` || `) operator rather than an `and`(`&&`).

### whereIncludes
The `whereIncludes` method is useful when you need to check if an object property that is an array contains a certain value. The `whereIncludes` method takes 2 or 3 parameters.
```
filterBuilder.whereIncludes(key, keyToFind, value)
```
The best way to show this is with an example:
```
filterBuilder.whereIncludes('hobbies', 'name', 'swimming').get();

/** 
* returns the following
*    [{
*        name: 'Angus',
*        age: 24,
*        hobbies: [
*            {name: 'swimming', hoursNeeded: 2},
*            {name: 'running', hoursNeeded: 1}
*        ]
*    },
*    {
*        name: 'Jess',
*        age: 25,
*        hobbies:[
*            {name: 'swimming', hoursNeeded: 2},
*            {name: 'reading', hoursNeeded: 3},
*        ]
*    }]
*/
```
The alternate option is that you can use `whereIncludes` with only two parameters. The `key` and the `value`. This is ideal for checking that simple arrays (e.g ['swimming', 'cycling', 'diving']) contain a value.

```
filterBuilder.whereIncludes(key, value)
```
If we imagine that `hobbies` is an array of strings rather than objects
```
filterBuilder.whereIncludes('hobbies', 'reading')
//This would return the objects for `Jess` and `Aaron`.
```

### WhereIn
Runs a check to see whether the `value` of the object is contained within the array provided. Useful to shorten multiple `orWhere` checks on the same `key`

Documentation in progress...
```
filterBuilder.whereIn('name', ['Angus', 'Jess']) - returns the objects for `Angus` and `Jess`
```


### WhereContains
Runs a case insensitive check to see if the `value` in the object contains the given string. Useful with search

Documentation in progress...

```
filterBuilder.whereContains('name', 'a') - returns the objects for `Angus` and `Aaron`
```
