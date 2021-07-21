---
layout: page
title: Javascript
permalink: /js/
---

Spread operator for objects:  
```js
let res = {...obj1, ...obj2};
```

Assignment object variables in new way:

```js
const x = 25, y = 10;
const coords = {
    x,
    y
}
```

```js
const x = 25, y = 10;
const coords = {x,y,
    calcSq() {
        console.log(this.x * this.y);
    }
}
```

```js
const user = {
    name: {
        first: 'Sam',
        second: 'Smith'
    },
    pass: 'qwerty',
    rights: 'user'
};

const {name: {first, second}, pass, rights} = user;
```
```js
function connect({
    host = 'localhost',
    port = 3003,
    user = 'def'
} = {}) {
    console.log(`host: ${host}, port: ${port}, user: ${user}`);
}

connect({
    host: 'localhost',
    port: '300',
});
```

Destructuring assignment:

```js
const numbers = [3, 5, 6, 7];
const [a, b, c] = numbers;
console.log(a, b, c); //3, 5, 6
const [, , d] = numbers;
console.log(d); // 6
const nums = [[3, 5], [6, 7]];
const [[a, b],[c, d]] = nums;
console.log(a, b, c, d); // 3, 5, 6, 7
```

```js
const {prop1: { prop2: [urVar1, urVar2] } = objToDestruct;
```

### Time

*  Get milliseconds:

```js
let time = Date.parse('Some date string');
```

*  Parse

```js
new Date(Date.parse(time)).getFullYear();
new Date(Date.parse(time)).getMonth() + 1;
new Date(Date.parse(time)).getDay();
```

### Array

```js
Array.toString();
```
