---
layout: page
title: Typescript
permalink: /typescript/
---
Explicitly define type of variable:
```ts
let a: number = 4; // number
```
Automaticaly define type of variable with assigning:
```ts
let b = 'sdfsdf'; //string
let c = true; // boolean
```
You can't summ different types between each other:
```ts
let ac = a + c;
// Error
```
Define array of strings:
```ts
let d: string[] = ['asd', 'dsa'];
```
You can't add any data except string in array of strings:
```ts
let d: string[] = ['asd', 1, 'dsa']; // Error
```
Like "normal" JS variable. Not recommended:
```
let e: any = 3;
e = 'sdfs'; // can be reassigned
```
Implicit function parameter definition returns Error, if "nonImplicitAny" set to True:
```ts
function test(a) {
	return ''
} //Error
```
Explicit function parameter definition. Takes string - returns number OR string;
```ts
function test(a: string): number | string {
	return '';
} 
```
Arrow function:
```ts
const test2 = (a: number): number => {
	return a*2;
} 
```
Map array of string:
```ts
d = d.map( (x: string) => x.toLowerCase())
```
Object typing with optional argument "long" (can also be {long: number | undefined}):
```ts
function countCoord(coord: {lat: number, long?: number}) {
	...
}
```
Safety type checking:
```ts
function printIt(id: number | string) {
	if (typeof id === 'number') {
		console.log(id.toString());
	} else {
		console.log(id.toUppercase());
	}
}	
```
Check for array type:
```ts
function getSum(a: number | number[]) {
	if (Array.isArray(a)) {
		...
	}
}
```
If function returns nothing - set 'void':
```ts
function test(a: string): void {
	...
} 
```
Null and undefined can be only null and undefined:
```ts
const x: undefined = undefined;
const z: null = null;
```
---
Custom types definition and inheritance, which makes code cleaner:
```ts
type Point = {
	lat: number,
	long?: number
}

//inheritance
type D3Point = Point & {
	alt: number,
}

function countCoord(coord: Point) {
	...
}
```
Another custom type:
```ts
type stringOrNumber = string | number;
```
Definition of custom interface and extension of it;
```ts
interface IPoint {
	lat: number,
	long?: number
}

interface I3DPoint extends IPoint {
	alt: number;
}
```
In contrast to types, iterfaces are able to be assgined a new property;
```ts
interface ITest {
	a: number;
}

interface ITest {
	b: number;
}
// All is ok

type UTest {
	a: number;
}

type UTest {
	b: number;
}
// Error you can't assign new prop, you are trying to redefine UTest;
```
Cast of types:
```ts
// Error
const c = (point: IPoint) => {
	const d: I3DPoint = point;
} 
// Valid
const c = (point: IPoint) => {
	const d: I3DPoint = point as I3DPoint;
} 
// Valid
const myCanvas = document.getElementById('canvas') as HTMLCanvasElement;
```

## Literal types
Variable 'c' can only be equal to 'test':
```ts
let c: 'test' = 'test';
```
Example of use case of literal types:
```ts
type actionType = 'up' | 'down';

function performAction(action: actionType): 1 | -1 {
	switch (action) {
		case 'up': 
			return: 1;
		case 'down': 
			return: -1;
	}
}
```
Can be combined with interface:
```ts
interface ComplexAction {
	s: string;
}

function performAction(action: actionType | ComplexAction) {
	switch (action) {
		case 'up': 
			return: 1;
		case 'down': 
			return: -1;
	}
}
```

## Enum
Similar to literal types but more handy. Every prop is automaticaly numbered from 0, hence it's numbered enum;
```ts
enum Direction {
	Up, // 0
	Down, // 1
	Left, // 2
	Right // 3
}
```
You can explicitly set starting number:
```ts
enum Direction {
	Up = 3, // 3
	Down, // 4
	Left, // 5
	Right // 6
}
```
String enums:
```ts
enum Direction {
	Up = 'UP',
	Down = 'DOWN',
	Left = 'LEFT',
	Right = 'RIGHT'
}
```
Heterogeneous enum:
```ts
enum Decision {
	Yes = 1,
	No = 'No'
}
```
Calc enum. Can only be number:
```ts
enum Decision {
	Yes = 1,
	No = calcEnum()
}

function calcEnum() {
	return 2;
}
```
Frontal enum. Can use other enum as input object:
```ts
function runEnum(obj: {Up: string}) {
	...
}

runEnum(Direction);
```
Reverse mapping (get string):
```ts
enum Test {
	A
}

let test = Test.A;
let nameA = Test[test] // A
```
Type 'never' which mean that var will never be defined:
```ts
enum Dice {
	One = 1,
	Two,
	Three
}

function runDice(dice: Dice) {
	switch(dice) {
        case Dice.One:
            return 'one';
        case Dice.Two:
            return 'two';
        case Dice.Three:
            return 'three';
        default:
            const a: never = dice;
	}
}
```
## Tuple
Length and order are constant:
```ts
const a: [number, string, number] = [0, 'a', 1];
```

## Generics
Allows reuse of code through <>:
```ts
function logTime<T>(num: T): T {
	console.log(new Date());
	return num;
}

// Copypaste
function logTime2<T>(num: T): T {
	if (typeof num === 'string') {
		num.toUpperCase();
	}
	return num;
}

logTime<string>('asd');
logTime<number>('asd'); // Error
logTime<number>(5);

// Generic interface
interface MyInterface {
	transform: <T, F>(a: T) => F
}

// Generic in class
class MyGenClass<T> {
	value: T;
}

const a = new MyGenClass<number>();

interface TimeStamp {
	stamp: number;
}

function logTimeStamp<T extends TimeStamp>(num: T): T {
	console.log(num.stamp); //without extending Generic, num.stamp isn't reachable
	return num;
}

```

## JSX
File extension .tsx
In tsconfig.json set "jsx": "react"
```node
npm i react
npm i -D @types/react
```
Definition of JSX element:
```tsx
import React from 'react';

const a: JSX.Element = 
	  <div tabIndex={0}>
		  <span>
			  {1 + 1}
		  </span>
	  </div>;

const b: JSX.Element = React.createElement('div', {tabIndex: 1 + 1}, 'a');
```
Cast:
```tsx
const a = b as string;
```
