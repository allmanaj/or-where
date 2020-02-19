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
    {name: 'Angus', age: '24'},
    {name: 'Jess', age: 25},
    {name: 'Aaron', age: 25}
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
[{name: 'Jess', age: 25}]
```
would be returned.

### orWhere
This can be used in the exact same way as `where()` however it acts as an `or` (` || `) operator rather than an `and`(`&&`).
