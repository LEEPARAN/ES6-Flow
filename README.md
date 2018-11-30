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
undefined를 호출하는데 이 과정을 - 호이스팅 하여 변수를 선언 / 선언 된 값을 대입 정도로 구분해보자

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

result = 10 (10번 출력)
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
