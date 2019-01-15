# ES6-Flow
## 공부 후 간단하게 배웠던 것들을 보지않고 기록하도록 한다.
### 2018.11.29 block scope

#### block scope 란
es6에는 기존의 변수인 var 이외에 let, const라는 것이 추가되었다. 일단 알아만두고 후에 추가로 다루도록 하자.

scope는 범위, 영역, 활용공간 등을 뜻하는데 es5까지는 함수스코프라는 것만이 존재하였다.
이 강의에서는 함수스코프를 함수에 의해 생기는 활용공간 정도로 말하였고 이는 변수의 활용공간이 된다.
```
(function() {
  var a = 10;
  console.log(a); // a = 10;
}();
console.log(a); // a is not defined
```
함수 안에서 변수를 선언 후 실행할 경우 함수 내부와 함수 밖에서 변수를 실행할 경우 
위와 같이 결과가 출력될 것이다.

하지만 es6에 오면서 block scope라는 것이 생겼는데 { } 와 같은 중괄호 안에서 작성되는 것들은
모두 block scope의 영역안에 포함이 된다.

block scope 영역에 포함이 되려면 조건이 필요한데 이는 let, const를 사용한다는 것이다.
만약 기존에 사용하던 var 이 block scope의 영향을 받는다면 사용자들이 매우 혼란이 올 것이기에
기존에 사용하던 것들은 유지하고 새로운 변수를 추가하여 기존 사용자가 혼란할 일이 없도록 하였다.

```
{
  let a = 10;
  {
    let a = 20;
    console.log(a); // a = 20;
  }
  console.log(a); // a = 10;
}
console.log(a); // a is not defined
```

위와 같이 es6에 새롭게 추가된 let을 사용할 경우 { } 안으로 block scope의 영향을 받아 { } 밖에 있는
맨 아래 a를 호출한 것은 not defined를 표시한다.

이는 기존의 es5로도 알 수 있는데
```
(function(){
  var a = 10;
  (function(){
    var a = 20;
    console.log(a); // a = 20;
  })();
  console.log(a); a = 10;
})();
console.log(a); a is not defined
```
위와 같이 함수스코프 안에서 선언되는 변수는 함수 밖에서 호출할 수가 없다.

#### block scope - this
```
let value = 1;
let obj = {
  value: 1,
  setValue: function() {
    this.value = 2;
    (function(){
      this.value = 3;
    })();
  }
}
obj.setValue();
console.log(value); // 3
console.log(obj.value); // 2
```

block scope의 this에 대해서 알아보도록 하자
setValue 메소드에 선언 된 this는 obj이다.
setValue 안에 있는 함수의 this는 window 전역이다.

그 결과로 console 에는 각각 3과 2를 출력한다.
보통 이러한 상황에서 함수 안에서도 this를 obj를 가르키고 싶어서 두 가지 방안을 선택한다.

```
let obj = {
  value: 1,
  setValue: function() {
    this.value = 2;
    let _this = this;
    (function(){
      _this.value = 3;
    })();
  }
}

let obj = {
  value: 1,
  setValue: function() {
    this.value = 2;
    (function(){
      this.value = 3;
    }).call(this);
  }
}
```
위와 같은 두 가지 방법을 선택하여 this를 obj를 가르킬 수 있도록 제어하는데 es6에서는 한층 간결해졌다.
scope를 활용하여 분리함과 동시에 this를 obj를 가르킬 수 있는 방법이 있는데 매우 단순하다.

```
let obj = {
  value: 1,
  setValue: function() {
    this.value = 2;
    {
      this.value = 3;
    }
  }
}
```
이게 끝이다. 함수 안에 속해있지 않아 this가 전역을 가르키지도 않으며 call 이나 apply 를 사용하여
this를 강제로 가르킬 필요가 없다.

#### block scope - Hoisting

호이스팅은 변수 선언, 함수 선언을 실행컨텍스트 내부에서 위로 끌어올리는 과정을 말한다.
블록스코프에서는 과연 호이스팅을 하는가?

아래 코드를 보면 그에 대한 해답을 알 수 있을 것이다.
```
{
  let a = 10;
  {
    console.log(a); // a is not defined
    const a = 20;
  }
  console.log(a); // a = 10;
}
console.log(a); // a is not defined
```

첫 번째 a는 a is not defined 를 표시하였고
두 번째 a는 10을 호출하였다.

여기서 첫 번째 a 가 a is not defined 라서 es5라면 a 가 undefined 일텐데
호이스팅이 되지 않는거냐 할 수 있는데 조금 다르게 생각해보면 좋을 것 같다. 아래 es5로 작성한 코드를 보자.

```
(function(){
  var a = 10;
  (function(){
    console.log(a); // undefined
    var a = 20;
  })();
  console.log(a); // a = 10;
})();
console.log(a); // a is not defined
```

위에 설명했듯이 es5 함수스코프로 구성 된 코드는 첫 번째 console 에서 undefined를 호출한다.
es5 는 var a; 변수 선언이 올라오고 console.log(a) 에는 a 라는 선언 된 변수의 값이 비어있으니
undefined를 호출하는데 이 과정을 - 호이스팅 하여 변수를 선언 / 선언 된 값을 대입 정도로 구분해보자.

하지만 es6에서는 다르다 const a; 변수 선언이 올라오고 선언 된 값을 대입하지 않고 끝난 것이다.
그러하여 결과가 a is not defined가 나오는 것이다.
만약 블록스코프 내에서 호이스팅을 아예 하지 않는다면 첫 번째 a 는 상위에 있는 10이라는 값을 호출 했을 것이다.

결론은 블록스코프에서 호이스팅을 진행하나 es5 와 다르게 undefined를 대입하지 않고 변수가 선언된 채로 끝이난다.

### 2018.11.30 block scoped variables

#### let

let은 위에 말했듯이 block scope의 영향을 받는다.

```
let a = 1;
function fn() {
  console.log(a, b, c) // 1, not defined, not defined
  let b = 2;
  console.log(a, b, c); // 1, 2, not defined
  if(true) {
    let c = 3;
    console.log(a, b, c); // 1, 2, 3
  }
  console.log(a, b, c); // 1, 2, not defined
}
```

아래 코드를 실행하면 위와 같은 결과가 출력되며 왜 코드가 이런 식으로 나오는 지는
위에 block scope를 보면 알 수 있으니 생략한다.

for 문에서의 var 과 let 의 차이가 어떻게 진행되는지 알아보자.
```
var arr = [];

for(var i = 0; i < 10; i++) {
  arr.push(function() {
    console.log(i);
  });
}

arr.forEach(function(f){
  f();
});

result: 10 (10번 출력)
```
위와 같이 10이 10번 출력된다.

이유는 변수 i가 가지고 있는 var i 의 값이 for문을 끝내고 10이 되어있는 상태에서 
forEach를 실행해 배열의 함수를 호출하면 함수의 i 값은 함수 내부에서 i를 찾지 못하고
외부에 있는 for문이 끝나 10이 된 i의 값을 받아서 10을 10번 출력하게 된다.

이를 해결하기 위해서 자바스크립트의 클로저를 활용하였다.

```
var arr = [];

for(var i = 0; i < 10; i++) {
  arr.push((function(v) {
    return function() {
      console.log(v);
    };
  })(i));
};

arr.forEach(function(f){
  f();
});

result :
0
1
2
3
4
5
6
7
8
9
```

위와 같은 방식으로 클로저를 활용하여 함수를 리턴해 i의 값을 보존하고 
그 값을 출력하는 방식을 사용하였다.
사용 후 클로저에 내부에 있는 불필요한 코드들이 불필요한 데이터를 잡아먹고 있어 효율적인 코드는 아니다.
~~ 불필요한 데이터 이전에 그냥 뒤에 나올 es6보다 코드가 길다. ~~

하지만 es6에서는 block scope가 존재하기에 좀 더 쉽고 간편하게 위와 동일한 출력을 할 수 있다.

```
const arr = [];

for(let i = 0; i < 10; i++) {
  arr.push(function() {
    console.log(i);
  });
};

arr.forEach(function(f) {
  f();
});

result :
0
1
2
3
4
5
6
7
8
9
```

이게 전부이다. 이렇게만 하면 간편하면서도 위와 동일한 코드를 출력할 수 있는데 
이유는 for문을 실행하면서 block scope가 각각 개별적으로 실행해 값을 부여한다고 하는데
솔직히 아직 이해는 잘 안된다. 아무튼 let을 활용하여 조금 더 짧은 코드와 동시에 불필요한
데이터도 쌓을 일을 줄일 수가 있다.

#### const

const는 constant variable의 약자이며 한국어로는 상수변수이다.
이게 엄청 모순될 수 있는 말이기는 한데 강의 들어보면 딱히 그런 것 같지도 않다.
아무것도 들어가지 않은 無의 상태에서 이것은 상수다. 라고 할 수는 없다. 무언가 하나의 값이 들어간 뒤에서야
변하지 않는 상수를 지정할 수 있는데 이 무언가 하나의 들어가기 전까지는 다양한 값들이 들어갈 수가 있는 변수이며
값이 지정된 이후에야 비로소 변하지 않는 상수가 될 수 있기에 상수변수라고 하여도 이상할 것은 없다고 본다.

서론은 여기까지 하고 const는 재할당이 불가능한 변수이며 선언과 동시에 값을 할당(초기화)해주어야 한다.

이게 무슨 뜻인지 코드를 통해 알아보도록하자.

```
const name = "LEEPARAN";
name = "LEENORAN"; // Uncaught TypeError: Assignment to constant variable.

const age; // Missing initializer in const declaration
const = 26;
```

위와 같이 name = "LEENORAN"; 으로 재할당을 하려할 경우 상수변수에 대입할 수 없다고 타입에러가 나오며
const age; 하고 값을 할당하지 않은 경우 const 선언에서 값이 누락되었다고 에러를 출력합니다.

이렇게 상수는 값을 재할당을 할 수 없어서 값을 바꿀 수가 없다. 그렇다면 아래 코드는 어떤 결과를 보여주는지 한번 알아보자.

```
const obj = {
  name: "LEEPARAN",
  age: 26
}
obj.name = "LEENORAN";
console.log(obj) // { name: "LEENORAN", age: 26 }
```

아니 분명 obj 는 const 안에 들어가서 상수가 되었거늘 왜 name 값이 바뀌는 것인가?
이는 const가 obj 를 상수로 지정했지 객체의 name과 age를 상수로 지정하지 않았기 때문이다.
조금 더 구체적으로 const obj 가 하나의 주소만을 바라보고 그 주소 외에는 다른 주소를 볼 수 없으며 그 주소외에는 영향을 주지 않는다.
코드를 확인 한 후 자세히 이야기 하도록 하자.

```
let a = {
  num1: 1,
  num2: 2
}
const b = a;
let a = 1000;

console.log(b); // { num1: 1, num2: 2 }
console.log(a); // 1000;
```

처음에 이 코드의 결과를 보고 도대체 이게 무슨 상황인가 했다. 하지만 후에  "const가 하나의 주소만 보고 있고 
그 외의 주소는 볼 수 없다."라는 것이 키워드임을 알아차리고 보니 이해가 되기 시작하였다. 다시 코드로 가보도록 하자.

```
let a = {
  num1: 1,
  num2: 2
}
// let a 가 @10번 주소에 할당되었고 객체는 @101번 주소에 할당이 되었다. @10번은 @101번을 바라본다.
// @10: a => @101
// @101: { num1: 1, num: 2 }
const b = a;
// const b가 @11번 주소에 할당이 되었고 b는 a를 바라보며 a는 @101번을 바라본다.
// @11: b => @10 => @101
// @11 => @101 최종적으로 @11번은 @101번을 바라본다.
a = 1000; // 1000이라는 새로운 값은 @102번 주소에 할당되었고 @10번은 재할당되어 1000이 담긴 @102번 주소를 바라본다.

결과적으로 현재 아래와 같은 결과가 나온다. const 인 @11번은 처음 바라본 주소인 @101 만을 바라보고 다른 것을 볼 수 없다.
@10: a => @102: 1000
@11: b => @101: { num: 1, num: 2 }

console.log(b); // { num1: 1, num2: 2 }
console.log(a); // 1000;
```

이러한 과정을 거쳐서 위와 같은 결과가 나오게 된다.

#### Object.freeze and deep copy

위에 const 에서 상수변수인 const에 배열, 객체를 담더라도 해당 값을 바꿀 수 있다는 것을 확인하였다.
그렇다면 이렇게 배열, 객체를 담더라도 바꿀 수 없도록 하려면 어떻게 해야될까?

Object.freeze라는 것이 있는데 한번 코드를 통해 보도록 하자.

```
const obj = {
  name: "LEEPARAN",
  hobby: ["game", "watch youtube", "reading books"]
}
obj.name = "LEENORAN";
console.log(obj); // { name: "LEENORAN", hobby: ["game", "watch youtube", "reading books"] }
Object.freeze(obj);
obj.name = "LEEHAYAN";
console.log(obj) // { name: "LEENORAN", hobby: ["game", "watch youtube", "reading books"] }
obj.hobby[1] = "create youtube video";
console.log(obj) // { name: "LEENORAN", hobby: ["game", "create youtube video", "reading books"] }
```

위와 같이 Object.freeze 메소드가 실행되기 전에는 name의 value 값이 변경되었으나
Object.freeze 를 실행하고 난 뒤에는 obj.name = "LEEHAYAN"; 을 실행하고 난 뒤에도
name의 value 값이 변경되지 않았음을 확인할 수 있습니다.
하지만 배열 값을 가지고 있는 hobby는 여전히 값이 변하는 것을 확인할 수 있다.
이러한 object 값들을 완전히 동결시키려면 type이 "object" 일 경우 Object.freeze 를 해주는 함수를 만드는 것이 좋을 것 같다.

그러므로 이러한 참조형 데이터를 완전히 얼리려면 두 가지 방법을 사용하면 된다.
1) object 자체를 얼린다.
2) object 내부의 참조형 데이터들을 순회하면서 얼린다.

이는 deep copy (깊은 복사)를 할때도 비슷한데 일단 깊은 복사와 얕은 복사에 대해서 알아보도록 하자.

얕은 복사: 객체의 프로퍼티들을 복사 ( depth의 1단계 까지만 )
깊은 복사: 객체의 프로퍼티들을 복사 ( 모든 depth에 대해서 )

그러면 다시 deep copy 과정이 object freeze 와 비슷하다고 하였는데 deep copy 하는 방법을 적어보도록 하자.
1) object 자체를 복사한다.
2) object 내부의 참조형 데이터들을 순회하면서 복사한다.

위의 object freeze와 얼린다라는 점만 복사로 바꾸면 완전히 동일한 방법을 가진다.
그러면 어떻게 복사하는지에 대해 코드로 알아보도록 하자.

```
const obj = {
  name: "LEEPARAN",
  hobby: ["game", "watch youtube", "reading books"]
};

const copy = Object.assign({}, obj);
console.log(copy); // { name: "LEEPARAN", hobby: ["game", "watch youtube", "reading books"];

copy.name = "LEENORAN";
console.log(copy.name) // "LEENORAN"
console.log(obj.name) // "LEEPARAN"

copy.hobby[1] = "create youtube video";
console.log(copy.hobby) // ["game", "create youtube video", "reading books"]
console.log(obj.hobby) // ["game", "create youtube video", "reading books"]
```

Object.assign 을 통해 copy라는 변수에 obj를 복사하였지만 얕은 복사만 이루어졌기에 copy의 참조형 데이터인 hobby를 바꾸면
obj의 hobby까지 같이 변경이 이루어진다. 복사가 제대로 되어 각자 다른 데이터 주소를 보고 있는 것이 아니라 동일한 주소를 보고 있는 것이다.

깊은 복사를 하려면 아래와 같이 코드를 작성하면 된다. Object.freeze 와 매우 유사하다.

```
const obj = {
  name: "LEEPARAN",
  hobby: ["game", "watch youtube", "reading books"]
};

const copy = Object.assign({}, obj);
copy.hobby = Object.assign([], obj.hobby);
copy.hobby[1] = "create youtube video";
console.log(copy.hobby); ["game", "create youtube video", "reading books"]
console.log(obj.hobby); ["game", "watch youtube", "reading books"]
```

이런식으로 참조형 데이터를 Object.assign을 활용해 복사하면 깊은 복사를 하여 서로 완전히 다른 주소를 바라볼 수 있다.

#### for문에서의 주의사항

```
var obj = {
  prop1: 1,
  prop2: 2.
  prop3: 3
}

for(const prop in obj) {
  console.log(prop);
}

result: prop1, prop2, prop3

for(const i = 0; i < 5; i++) {
  console.log(i);
}

result: Uncaught TypeError: Assignment to constant variable.
```

for in 문에서는 예외적으로 const가 사용 가능하며 일반 for 문에서는 const를 사용할 수 없다.

#### let, const 공통사항 

```
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
delete a; // false
console.log(a); // 1
console.log(window.a); // 1
```

전역변수 a는 변수의 a 이자 window 객체의 a 프로퍼티이기도 하다.
delete는 객체 프로퍼티를 삭제하는 것인데 a 값이 변수이자 동시에 객체 프로퍼티이기에 삭제를 할 수가 없어서 delete a가 false 를 표시한다.

```
window a = 10;
console.log(window.a) // 10
delete a; // true
console.log(window.a) // undefiend
```

위와 같이 처음부터 window의 객체로 a를 넣으면 delete 가 정상적으로 true를 표시하는 것을 확인할 수 있다.
그렇다면 let과 const의 공통사항이 무엇인지 아래 코드로 확인해보자.

```
let b = "bee";
const c = "see";

console.log(window.b); // undefiend
console.log(window.c); // undefiend
console.log(b) // "bee"
console.log(c) // "see"
```

위와 같이 let과 const는 전역에서 변수를 선언하여도 window의 객체로 들어가지 않는다.
전역 변수와 window 객체를 완전히 분리해준다.

* ES6를 사용할 경우 변수는 왠만하면 const를 사용하도록 한다. 개발하면서 생각보다 재할당 할 경우가 매우 적으며
  재할당할 필요가 있을 경우에만 let을 사용하고 var 는 그냥 사용하지 않는다.
  재할당할 필요가 있다고 생각되어도 생각보다 const를 사용하여 재할당하지 않아도 괜찮은 코드들을 만들어 낼 수 있다고 한다.
  var 과 혼용하여 사용할 경우 속도가 저하된다. 차라리 var을 쓸거면 var만 쓰도록 하자.

### 2018.12.11 template litreal

#### 소개
#### template litreal 란

기존의 문자열 작성 방법에는 두 가지가 존재하였으나 하나가 더 생겼다. 
이는 백틱(backtick) 이라고 하는 키보드 물결 문자 부분을 쉬프트를 누르지 않고 누르면 된다

```
var a = '안녕하세요.'; // string litreal
var b = "안녕하세요2."; // string litreal
var c = `안녕하세요3.`; // template literal
```

위를 실행하면 위와같이 동일하게 나옵니다.

템플릿 리터럴의 장점으로 줄바꿈을 편하게 할 수 있다는 것이다.
```
var d = "abc\n" +
  "deg"
  
var e = `abc
def
ghi`
```

줄바꿈은 편하지만 코딩시에 가독성을 위한 복조차 없다.

```
        abc
def
ghi
```

출력 값을 이용해서 코드와 개발물이 동시에 잘보이는 코드로 하자.

템플릿의 주 목적은 이것이 아니고 아래와 같이 사용하는게 주 목적이다.

```
const a = 10;
const b = 20;
const sum = `${a} + `{b} = a + b`;
console.log(sum); // 10 + 20 = 30
```

#### 상세 (이전 글을 보고서 작성하였다.)

template literal에 대해 조금 더 자세히 알아보도록 하자.

```
// 1) multi-line 들여쓰기
// 위에도 작성하였지만 템플릿 리터럴 안에서는 모든 띄어쓰기 줄바꿈이 적용된다.
const f = function() {
  const a = `abc ${10}
def
ghij`
}
// 결국 문자열이므로 자동적으로 toString 처리가 된다.
console.log(`${[0, 1, 2]}`);
console.log(`{{a: 1, b: 2}}`);
console.log(`${function() { return 1}}`);
console.log(`${(function() { return 1 })()}` + 1);

// 중첩된 backtick 처리
console.log(`foo ${`bar`}`);

// 가독성을 위해 trim 처리
function a() {
  return `
<div>
  <h1>Lorem ipsum</h1>
</div>
`.trim();
}
console.log(a());

const linesToHTML = function (characters) {
    debugger;
    return characters.reduce(function (charactersResult, characters) {
        let {name, lines} = characters;
        return `${charactersResult || ''}
<article>
    <h1>${name}</h1>
    <ul>
        ${lines.reduce(function(linesResult, line) {
            return `${linesResult || ''}
            <li>${line}</li>
            `.trim()})}
    </ul>
</article>
    `.trim()}, 0);
}
const characters = [{
    name: 'Aria Stark',
    lines: ['A girl has no name.']
}, {
    name: 'John Snow',
    lines: [
        'You know nothing, John Snow',
        'Winter is coming'
    ]
}];
```

#### forEach, map, reduce 메소드에 대하여

위 세 가지 메소드는 ES5에 있는 메소드들이지만 사용하지 않아본 사람들을 위한 설명이 담긴 내용이다.

1. forEach는 알다시피 배열 for문을 편하게 동작시켜주는 역할을 한다.
```
const a = [1, 2, 3];
a.forEach(function(value, index, array) {
  console.log(value, index, array, this);
}, [10, 20, 30]);

출력 :
1 0 (3) [1, 2, 3] (3) [10, 20, 30]
2 1 (3) [1, 2, 3] (3) [10, 20, 30]
3 2 (3) [1, 2, 3] (3) [10, 20, 30]
```

2. map 메소드는 for문을 돌려 새로운 배열 요소를 생성하기 위해 사용한다.

```
const b = [1, 2, 3];
const c = b.map(function(v, i, arr) {
  console.log(v, i, arr, this);
  return this[0] + v;
}, [10]);

출력: [11, 12, 13];
```

3. reduce 메소드는 for문을 돌려 최종적으로 다른 무언가를 만들기 위해 사용한다.

```
const d = [1, 2, 3];
const e = d.reduce(function(acc, val, idx, arr) {
  console.log(acc, val, idx, arr);
  return acc + val;
}, 10);

출력: 16
// acc의 값이 this 10 으로 들어온다.
// 다 합쳐서 10이 주어진다.

// 여기서 뒤에 this가 있을 경우와 없을 경우의 차이점
// this가 존재할 경우 acc의 값을 0으로 지정하고 value 값을 맨 첫 번째 배열로 지정 index는 0 번째를 가르킴
// 하지만 this가 존재하지 않을 경우 acc(누산값)에 첫 번째 배열 값이 1이며 value에 그 다음 인덱스인 2번이 들어온다.
```

위 작업은 숫자 뿐만 아니라 문자열도 하나로 합쳐줄 수 있다.
```
const f = ['a', 'b', 'c', 'd', 'e'];
const g = f.reduce(function(acc, val, idx, arr) {
  return acc + val;
});

출력: "abcde"
```

그리고 이전의 숫자를 하나로 맞춰달라는 작업을 더 손 쉽게할 수 있다.
```
const h = [1, 2, 3, 4, 5, 6, 7, 8, 9];
let sub = 0;
// 1. 정말 귀찮았던 숫자 더하기
for(let i = 0; i < h.length; i++) {
  sub += h[i];
}
console.log(sub);

// 2. reduce 메소드로 조금 더 간단하게
const k = h.reduce(function(a, v, i, arr) {
  return a + c;
})

// 3. ES6 Function Arrow 를 활용한 reduce
const l = h.reduce((a, c) => a, c);

모든 출력값 봉투안으로...
```

#### default parameter

말그대로 기본 매개변수라고 생각하면 좋을 것 같다. 우리는 ES5에서 기본 매개변수 값을 설정하기 위해 아래와 같은 코드들을 사용하였다.

```
const default1 = (a, b, c) => {
  var aa = a || 1;
  var bb = b ? b : 1;
  var cc;
  if(!c) {
    cc = 1;
  }
  else {
    cc = c;
  }
  return aa + bb + cc;
}
default1(10, 10); // 출력 값: 21
```

하지만 이제 ES6에서는 이러한 일을 할 필요가 없다. ES6에서는 이래와 같은 방식으로 하면 된다.

```
const default2 = (a = 1, b = 1, c = 1) => {
    return a + b + c;
}
default2(10, 10); // 출력 값: 21
```

위에 매개변수가 담기는 곳에 default parameter값을 넣어주면 해당 값이 비어있을 경우 default parameter 값을 변수에 담는다.
이 ES6의 default parameter를 담는 방식은 ES5로 적은 방식과 약간의 다른 결과를 나타낸다.
(default1은 ES5 코드, default2는 ES6 코드라는 가정하에 작성한다.)

ES5 코드에서 아래와 같은 방식으로 작성할 경우 어떠한 결과가 출력될 까?

```
default1(null, 0, 5); 출력 값: 7
```

결과는 위에 작성된 것과 같이 7이 나오는데 null과 0이라는 숫자를 함수 호출할 때 고유한 값으로 적용되지 않고 전부 false로 처리되어서
작성해둔 default 값이 적용되어 null => 1, 0 => 1, 5 => 5가 되어서 총 합 7이 되었습니다.
그렇다면 ES6로 작성해둔 default parameter 코드는 어떠한 결과를 나타낼 것인가?

```
default2(null, 0, 5); // 출력 값: 5
```

위에 작성해 둔대로 출력 값이 5가 나왔습니다. ES6의 default parameter는 ES5와 달리 0, null 등을 순수한 true, false로 구분하지 않고
순수하게 값이 입력되었나의 여부만을 따지기 때문에 null과 0 또한 입력되어서 default parameter를 바라보지 않고 값이 입력되어 null + 0 + 5라는
식이 나왔고 5라는 결과가 출력되었습니다.

여기서 궁금해지는 점 과연 arguments는 인자가 비었을 경우 어떤 값을 출력할 것인가?
arguments란 함수 호출 시 들어있는 인자값을 함수의 매개변수에 작성하지 않아도 함수 내에서 실행 시 유사배열 형태로 인자에 담긴 값을 출력한다.

```
function abc(a = 1, b = 2, c = 3) {
    console.log(arguments); // 출력 값: 없음
}
abc();
```

위의 결과에서 보여줬듯이 arguments는 default parameter 값을 가져오지 않고 아무값도 출력하지 않는다.
arguments는 함수 호출 시 인자에 담긴 값만을 출력한 다는 것을 알 수 있다.

#### rest parameter (나머지 연산자)

위에 default parameter에서 arguments는 유사배열이라고 설명하였다.
arguments는 유사배열이므로 이를 유사배열이 아닌 진짜 배열로 바꿔주려면 아래와 같은 절차를 거쳐야한다.
물론 아래 방법 이외에도 유사배열을 배열로 바꾸는 방법은 많다.

```
function abc() {
  var arr = Array.prototype.slice.call(arguments);
}
```

아무튼 나머지 연산자에 대해서 알아보자
- 사용법

```
const abc = (...arr) => {
    console.log(arr);  // 출력 값: 1, 2, 3, 4, 5
}
abc(1, 2, 3, 4, 5);

const abc2 = (a, b, ...arr) => {
    console.log(a, b, arr); // 출력 값: 1, 2, [3, 4, 5]
}
abc2(1, 2, 3, 4, 5);
```

이름에 알맞게 앞에 출력 될 매개변수가 있으면 해당 매개변수를 제외한 나머지 값들을 출력해준다.

```
const abc3 = (a, ...arr, b) => {} 
```

위와 같은 식으로 사용할 경우 나머지 연산자는 맨 마지막에 배치되어있어야 한다며 에러를 출력한다.
위에 언급하였듯이 유사배열이 아닌 Array 그 자체이며 배열에서 사용되는 기능인 shift() 같은 기능도
Array.prototype.slice.call(arguments); 이런 과정 거치지 않고 쉽게 사용할 수 있다.

#### spread operator(펼침 연산자, 전개 연산자)

강의하시는 분께서 한글로 아직 정확히 정의된게 없어서 명칭을 두 가지를 작성하였고 실무에서 혼동이 없도록
말할때에는 그냥 스프레드 오퍼레이터라고 말하는게 좋을 것이라고 하였다.

일단 사용법은 위의 나머지 연산자와 비슷하다. 사용법부터 아래에서 보도록 하자.
- 사용법

```
const arr1 = [0, 1, 2];
const arr2 = [5, 6, 7];
const arr3 = [...arr1, 3, 4, ...arr2];
console.log(arr3); // 출력 값: [0, 1, 2, 3, 4, 5, 6, 7]

// ES5
var arr4 = arr1.concat(3, 4).concat(arr2); // 출력 값: [0, 1, 2, 3, 4, 5, 6, 7]

// 활용
Math.max(0, 1, 2); // 출력 값: 2
Math.max.apply(null, arr1); // 출력 값: 2
Math.max(...arr1); // 출력 값: 2
```

위와 같은 방식으로 활용이 가능하다. 나머지 연산자와 다른 점으로는 사용 법에서 보여줬듯이
배열의 앞, 뒤로 자유롭게 붙여서 사용이 가능하다.

그러면 언제 rest operator이고 언제 spread operator인가?

getter: 나머지 / 받는 입장
setter: 펼치기 / 주는 입장

으로 나누면 쉬울 것 같다.

특징1: spread operator는 '새로운' 배열이다.

```
const oldArr = [1, 2, 3];
const newArr = [...oldArr];
oldArr.push(4);
console.log(oldArr); // 출력 값: [1, 2, 3, 4]
console.log(newArr); // 출력 값: [1, 2, 3]
```

특징2: '얕은 복사'만을 수행한다.

```
const oldArr = [{
    first: "Github",
    second: "Git Desktop"
},{
    first: "ES5",
    second: "ES6"
}];
const newArr = [...oldArr];
newArr[0].first = "Git Bash";
console.log(oldArr[0].first); // 출력 값: "Git Bash"
console.log(newArr[0].first); // 출력 값: "Git Bash"
```

#### shorthand property
ES5에서 귀찮은 것들을 제거해주었다.

```
// ES5
var x = 5;
var y = 10;
var obj = {
    x: x,
    y: y
}
// ES6
const obj2 = {
    x,
    y
}
console.log(obj, obj2) // 출력 값: { x: 5, y: 10 }, { x: 5, y: 10 }
```

ES6로 들어오면서 위와 같이 프로퍼티 값과 대입하고자 하는 값의 명칭이 동일할 경우 생략하고 작성하여도 원하는 결과를 나타낼 수 있다.

```
// ES5
var obj = {
    greeting: function() {
	return "hi";
    }
}
// ES6
const obj2 = {
    greeting() {
    	return "hi";
    }
}
console.log(obj.greeting()); // 출력값: "hi"
console.log(obj2.greeting()); // 출력값: "hi"
```

메소드를 호출하는 방식도 조금 더 가볍게 바뀌었는데 결정적인 차이점으로는 ES6의 방식에서는 해당 메소드에 prototype이 빠져있다.
이로 인해 가장 큰 장점이 해당 기능에 조금 더 충실하고 prototype 메소드가 빠짐으로서 조금 더 가벼워져 성능 향상이 된다고 하는데
또 무언가가 빠진 것 같다. 강의 다시 듣고 보충하도록 해야겠다.


