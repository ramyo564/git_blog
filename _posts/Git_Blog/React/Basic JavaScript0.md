---
layout: single
title: "[React] JavaScript 기본기"
categories: React
tags:
  - Python
  - JavaScript
  - 기본기
toc: true
toc_sticky: true
author_profile: false
sidebar:
---

# 자바스크립트 기본기

프로젝트를 하면서 리액트에 대한 기본기가 부족하다보니 머릿속에서 뭔가 새로운 아이디어가 떠오르거나 응용을 잘 못 하고 있다는 생각이 들어서 리액트를 기본기부터 배워야 겠다고 생각헀다.      
자바스크립트를 모르는건 아니지만 깔짝대기만 했고 파이썬처럼 자세하게 알지는 못 한다.       
기본 문법에 대해서 배울 떄는 자바랑 비슷한 부분이 많다고 느꼈는데 활용을 하다보니 파이썬과 비슷한 부분이 많다고 느꼈다.     
너무 뒤죽박죽이라서 정리 고고!
## 변수와 값 다시 보기

```javascript 
let userMessage = "Hi";
```

1. 우선 let은 값이 변할 수 있는 변수고 const는 처음에 값이 지정되면 변할 수 없다.
2. 이름 짓는 건 카멜케이스 그 이외에는 `_ 또는 $` 가 변수명에 사용될 수 있지만 웬만하면 카멜케이스로 지으면 된다.

## 함수와 매개변수

```javascript
function greet() {
	console.log("hello!");
}
great();

function greatUser(userName, message="Hi") {
	console.log(userName)
	console.log(message)
}
greatUser("Z");
-> Z
-> Hi

greatUser("A", "Hello!")
-> A
-> Hello!



function createGreating(userName, message="Hi") {
	return "Hi" + userName + " " + message;
}
console.log(createGreating("J"))

const greating1 = createGreating("Jiki", "All right?")
console.log(greating1)
```

1. 매개변수를 사용하지 않을 수도 있고 사용할 때 디폴트 값을 가지게 할 수도 있다.
2. 반환 값이 있는경우 위와 같이 사용해야 한다.

## 화살표 함수

```javascript
export default (userName, message) => {
// -> function(userName, message) {} 이렇게 하는거랑 똑같다
	console.log("hi")
	return userName + message;
}

// 매겨변수가 하나일 경우 () 는 생략 가능하다
(userName) => {}
userName => {}

// 매개변수가 없을 경우 또는 매개변수가 2개 이상일 경우 생략 x

 ```

## 객체와 클래스

```javascript
const user = {
	name: "J",
	age: 34
	greet() {
		console.log("hello!");
		console.log(this.age);
	}
};
-> {name: "J", age: 34}

console.log(user.name) 
-> J

user.great();
-> hello!
-> 34


class User {
	constructor(name, age) {
		this.name = name;
		this.age = age;
	}

	greet() {
		console.log('Hi');
	}
}

const user1 = new User("Manuel", 35);
console.log(user1);
-> User{name : Manuel, age: 35, constructor: Object}

user1.great();
-> Hi

```

## 배열 및 배열 메소드 (map)

```javascript
const hobbies = ["a","b","c"];
console.log(hobbies[0]);
-> a

hobbies.push("d");
console.log(hobbies);
-> a,b,c,d 

const index = hobbies.findIndex((item) => {
	return item === 'Sports';
})
// 다른 로직이 없다면 return 생략 가능
const index = hobbies.findIndex((item) => item ==="Sports");
console.log(index);
-> 0

const x = hobbies.map((item)=> item + "!");
console.log(x);
-> a!,b!,c!,d!

const y = hobbies.map((item)=> ({text: item}));
// 객체로 반환할 때는 () 로 감싸줘야 함수라고 인식을 안함


function transformToObjects(numberArray) {
	return numberArray.map(number => {
		return {val: number}
	})
}
```

- 객체는 키와 값으로 쌍으로 묶이지만 배열은 값만 순서대로 저장된다.

## 디스트럭처링

```javascript
const userNameData = ["j","kiki"]

const firstName = userNameData[0];
const lastName = userNameData[1];

////

const [firstName, lastName] = ["j","kiki"]


// 배열뿐만 아니라 객체에도 가능
const user = {
	name: "Max",
	age: 34
};

const name = user.name;
const age = user.age

///

const {name: userName, age} = {
	name: "Max",
	age: 34
};
console.log(userName);
console.log(age);
```

## 스프레드 연산자

```javascript
const hobbies = ["a","b","c"];
const newHobbies = ["d"];

const mergedHobbies = [hobbies, newHobbies]
-> 이렇게 되면 배열이 중첩됨
const mergedHobbies = [...hobbies, ...newHobbies]
-> ... 이 배열에서 묶여 있는 테두리들을 해체 해준다는 개념으로 이해하면 될 듯


// 객체도 가능
const user = {
	name: "J",
	age: 34
};

const extendedUser = {
	isAdmin: true,
	...user
}
console.log(extendedUser);
-> 객체도 풀어헤쳐짐
	isAdmin: true
	name: "Max"
	age: 34

```

## 컨트롤 구조

```javascript
if (10 === 10) {
	...
}else if(3 === 5){
 ...
}else {
	console.log("Hi")
}

const nums = [1,2,3,4]

for (const num of nums){
	console.log(nun);
}

```

## 함수를 값으로 사용하기

```javascript
const handleTimeout2 = () => {
	console.log("Timed out ... again!");
};

setTimeout(handleTimeout, 2000);
// 이 때 값으로 진행하는거라 handleTimeout() 이런 식으로 안쓰고 이름만 사용해서 불러옴
```

## 함수 내부에서 함수 정의하기

```javascript
function init() {
	function greet() {
		console.log("Hi");
	}
	greet();
}

init();
-> Hi
```

## 참조형과 기본 값 비교

```javascript
const hobbies = ["Sports", "Cooking"];
// hobbies = []; <- 이렇게는 안됨

hobbies.push("Working"); <- 이렇게는 가능
```

- 기본형 데이터 값은 기존에 메모리에 저장된 스트링이 삭제되고 새로운 주소 값을 할당됨
- 하지만 배열은 다름 const 로 지정된 배열 hobbies에 내장 함수 push를 사용하게 되면 메모리 배열은 수정되지만 메모리 주소는 변하지 않는다.
	- 메모리 주소에 값을 덮어 쓰는게 아니라 메모리 주소에 있는 값을 조작하는 것 -> 덮어 쓰는게 아님

## 펌

차세대 JavaScript - 요약

이 모듈에서, 저는 몇몇 핵심 차세대 자바스크립트 기능들에 대한 간략한 소개를 해 드렸습니다. 물론 이 과정에서 여러분들이 자주 보시게 될 것들에 초점을 맞추었죠. 여기 간략한 요약이 있습니다!

#### **let & const**

`let` 에 대해 더 읽어보기: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)

`const`에 대해 더 읽어보기:: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)

`let` 과 `const` 는 기본적으로 `var` 를 대체합니다. 여러분은 `var` 대신 `let` 을 사용하고, `var`  대신 `const`를 사용하게 됩니다. 만약 이 "변수"를 다시 할당하지 않을 경우에 말이죠 (따라서 효과적으로 constant로 변환합니다).

#### **ES6 Arrow Functions**

더 읽어보기: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

Arrow function은 JavaScript 환경에서함수를 생성하는 또 다른 방법입니다. 더 짧은 구문 외에도 `this` 키워드의 범위를 유지하는데 있 이점을 제공합니다 ([여기](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_binding_of_this)를 보세요).

Arrow function 구문은 낯설게 보일 수 있으나 사실 간단합니다.

1. function callMe(name) { 
2.     console.log(name);
3. }

또한 다음과 같이 작성할 수도 있습니다:

1. const callMe = function(name) { 
2.     console.log(name);
3. }

이렇게 됩니다:

1. const callMe = (name) => { 
2.     console.log(name);
3. }

**중요:** 

**arguments가 없는 경우**, 함수 선언시 빈 괄호를 사용해야 합니다:

1. const callMe = () => { 
2.     console.log('Max!');
3. }

**정확히 하나의 argument가 있는 경우**, 괄호를 생략할 수 있습니다:

1. const callMe = name => { 
2.     console.log(name);
3. }

**value를 return할 때**, 다음과 같은 숏컷을 사용할 수 있습니다:

1. const returnMe = name => name

이것은 다음과 같습니다:

1. const returnMe = name => { 
2.     return name;
3. }

#### **Exports & Imports**

React 프로젝트에서 (그리고 실제로 모든 최신 JavaScript에서), 모듈이라 불리는 여러 자바스크립트 파일들에 코드를 분할합니다. 이렇게 하면 각 file/ 모듈의 목적을 명확하게 하고 관리가 용이하게 합니다.     

다른 파일의 기능에 계속 액세스하려면 `export`  (available하게 하기 위해) 및 `import` 엑세스를 확보하기 위해) statements가 필요합니다.     

두 가지 유형의 export가 있습니다: **default** (unnamed)와 **named** 입니다.     

**default** => `export default ...;`      

**named** => `export const someData = ...;`      

**default exports**를 다음과 같이 import 할 수 있습니다.          

`import someNameOfYourChoice from './path/to/file.js';`     

놀랍게도, `someNameOfYourChoice`  전적으로 여러분에게 달려 있습니다.      

**Named exports**는 이름으로 import되어야 합니다:     

`import { someData } from './path/to/file.js';`      

파일 하나는 오직 하나의 default와 무한한 named exports를 가질 수 있습니다. 하나의 default를 같은 파일 내에서 named exports와 믹스할 수 있습니다.     

**named exports를 import할 때,** 다음 구문을 이용해 한 번에 모든 named exports를 import할 수 있습니다.     

`import * as upToYou from './path/to/file.js';`      

`upToYou` 는 모든 exported 변수/함수를 하나의 자바스크립트 객체에 모읍니다. 예를 들어, `export const someData = ...`  (`/path/to/file.js` ) 이와 같이 `upToYou` 에 액세스 할 수 있습니다: `upToYou.someData` .      

#### **Classes**

Classes는 constructor 함수와 prototypes를 대체하는 기능입니다. 자바스크립트 객체에 blueprints를 정의할 수 있습니다.    

예시:

1. class Person {
2.     constructor () {
3.         this.name = 'Max';
4.     }
5. }

7. const person = new Person();
8. console.log(person.name); // prints 'Max'

위의 예시에서, class뿐 만 아니라 해당 class의 property (=> `name`) 이 정의됩니다. 해당 구문은, property를 정의하는 "구식" 구문입니다. 최신 자바스크립트 프로젝트에서는 (이 코스에서 사용된 것처럼), 다음과 같은 보다 편리한 정의 방법을 사용해 class property를 정의합니다:     

1. class Person {
2.     name = 'Max';
3. }

5. const person = new Person();
6. console.log(person.name); // prints 'Max'

메소드를 정의할 수도 있습니다. 다음과 같이 말이죠:      

1. class Person {
2.     name = 'Max';
3.     printMyName () {
4.         console.log(this.name); // this is required to refer to the class!
5.     }
6. }

8. const person = new Person();
9. person.printMyName();

혹은 이와 같이 할 수도 있습니다:     

1. class Person {
2.     name = 'Max';
3.     printMyName = () => {
4.         console.log(this.name);
5.     }
6. }

8. const person = new Person();
9. person.printMyName();

두 번째 접근 방식은 all arrow function과 같은 이점이 있습니다: `this`키워드가 reference를 변경하지 않습니다.     

class 사용시 **inheritance**를 사용할 수도 있습니다.     

1. class Human {
2.     species = 'human';
3. }

5. class Person extends Human {
6.     name = 'Max';
7.     printMyName = () => {
8.         console.log(this.name);
9.     }
10. }

12. const person = new Person();
13. person.printMyName();
14. console.log(person.species); // prints 'human'

#### **Spread & Rest Operator**

Spread 와 rest operator는 사실 같은 구문을 사용합니다:     `...` 

맞습니다, 연산자입니다 - 점 세개죠. 이것을 사용해 spread로 사용할지 rest operator로 사용할지 결정합니다.     
 
**Spread Operator 사용하기:**

Spread operator는 배열에서 요소들을 가져오거나 (=> 배열을 요소들의 리스트로 분해) 객체에서 속성을 가져옵니다.      

두 가지 예시가 있습니다:     

1. const oldArray = [1, 2, 3];
2. const newArray = [...oldArray, 4, 5]; // This now is [1, 2, 3, 4, 5];

객체에 spread operator를 사용한 예시입니다:     

1. const oldObject = {
2.     name: 'Max'
3. };
4. const newObject = {
5.     ...oldObject,
6.     age: 28
7. };

그러면 `newObject`는 다음이 될 것입니다.     

1. {
2.     name: 'Max',
3.     age: 28
4. }

sperad operator는 배열과 객체를 복제하는데 매우 유용합니다. 둘 다  [(primitives가 아닌) reference 유형](https://youtu.be/9ooYYRLdg_g)이기 때문에, 안정적으로 복사를 하는게 어려울 수 있습니다. (복사된 원본에 future mutation 발생 방지). Spread operator로, 객체나 배열의 복사본 (shallow!)을 쉽게 얻을 수 있습니다.     

  

#### **Destructuring**

Destructuring을 사용하면 배열이나 객체의 값에 쉽게 엑세스할 수 있고 변수에 할당할 수 있습니다.     

한 배열의 예시입니다:     

1. const array = [1, 2, 3];
2. const [a, b] = array;
3. console.log(a); // prints 1
4. console.log(b); // prints 2
5. console.log(array); // prints [1, 2, 3]

다음은 객체의 예시입니다:     

1. const myObj = {
2.     name: 'Max',
3.     age: 28
4. }
5. const {name} = myObj;
6. console.log(name); // prints 'Max'
7. console.log(age); // prints undefined
8. console.log(myObj); // prints {name: 'Max', age: 28}

Destructuring은 인자를 가진 함수를 작업할 때 매우 유용합니다. 이 예시를 보시죠:     

1. const printName = (personObj) => {
2.     console.log(personObj.name);
3. }
4. printName({name: 'Max', age: 28}); // prints 'Max'

여기서, 함수내 name만을 print하고 싶지만 함수에 완전한 person 객체를 보내고 있습니다. 당연히 이것은 문제가 되지 않지만 personObj.name을 이 함수내에서 호출해야만 합니다. 이 코드를 destructuring으로 압축시켜 보겠습니다.     

1. const printName = ({name}) => {
2.     console.log(name);
3. }
4. printName({name: 'Max', age: 28}); // prints 'Max')

위와 동일한 결과를 얻지만 코드가 줄었습니다. Destructuring을 통해, `name` property를 가져와 `name` 이라는 이름의 변수/인수에 저장하고 함수 본문에서 사용할 수 있습니다.     

#### JS Array functions

차세대 자바스크립트는 아니지만 중요합니다. 다음과 같은 자바스크립트 array 함수가 있습니다: `map()` , `filter()` , `reduce()`.     

많은 React 개념이 (불변의 방식으로) 배열 작업에 의존하기 때문에 제가 그것들을 꽤 많이 사용하는 것을 보게 될 것입니다.     

다음 페이지는 어레이 프로토타입에서 사용할 수 있는 다양한 방법에 대한 좋은 개요를 제공합니다. 필요에 따라 이를 클릭하고 지식을 리프레시할 수 있습니다.      [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)

이 코스에서 특히 중요한 사항은 다음과 같습니다:     

- `map()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
    
- `find()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)
    
- `findIndex()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)
    
- `filter()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
    
- `reduce()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce?v=b](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce?v=b)
    
- `concat()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat?v=b](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat?v=b)
    
- `slice()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
    
- `splice()`  => [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)