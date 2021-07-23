# Function  
***  
서론  
***  
Js의 function은 parameterized.
parameter : list of identifier : work as local variable in function body
function 내에서 만든 변수는 this keyword를 이용한다.
만약 function이 object안에 있다면 method라고 부른다.
새로 만들어진 object를 초기화하는 function을 constructor이라고 한다.
Js에서 function은 object와 같은 취급을 받는다.
function은 같은 범위 내에 있는 변수를 쓸 수 있다. => Js function은 closure이다.
***  
본론  
***  
## 🦑 Defining Function    
가장 평범한 방법은 function keyword 사용하기  
그러나 ES6부터는 function keyword 대신 “arrow function“을 사용한다.  
convention shorthand syntax for defining methods  
또한 function은 Function() constructor을 통해 defined될 수 있다.  
**function declaration**  
```javascript
function functionName (identifier){statement}
```
중요한것은 function의 Name은 그 자체로 variable된 것이다. function을 value로 가진.  
function은 Js script의 맨 위에 hoisted된다.  
  
function에서 return은 function을 stop excuting하게 하고 value를 return 하게 한다. return 다음에 아무것도 명시되지 않으면 undefined를 return한다.  
  
 그외의 경우로 아예 function 안에 return이 없는 경우가 있다. 이 경우 function의 body끝까지 실행하고 마지막에 undefined를 return한다.  
  
ES6 이후 function은 block과 함께 정의되고 그 block안에서만 존재한다.  
**Function Expression**  
언뜻보면 function declaration과 비슷해보이지만 statement, name이 optional이다.  
```
const square = function(x){return x*x}
```
function declaration은 variable을 declare하고 function에 assign해야하지만  
function expression의 경우 variable을 declare 할 필요가 없다.  
  
function에 name을 쓴다면 function이라는 object가 name과 bind된다. 그러나 expression의 경우 name을 명시할 필요가 없다.  
  
만약 declaration form을 썼다면 function code는 가장 위로 hoisted되서 실행전에 create된다. 그러나 expression을 쓰면 그 함수를 부르기 전까지 create나 invoked되지 않는다.  
**Arrow function**
parameter과 function body를 분리한다.  
function keyword도 안쓰인다.  
```
const sum = (x,y) => {return x+y}
```
만약 single return statement 하나밖에 없다면 {}를 제거할 수 있다.  
심지어 parameter이 하나밖에 없다면 ()도 뺄 수 있다.  
결과적으로 x => x*x 이런식으로 작성될 수 있다.  
그러나 parameter이 없다면 () 을 써야한다.  
return이 없다면 undefined만 반환하므로 arrow function을 쓸 필요가 없다.  
  
arrow function은 function().function().function() 과 같은 function chain에 이상적이다.  
 this 가 자신이 명시된 범위에서 가져올 수 있다.  
 prototype property가 없다. => constructor function에선 쓰일 수 없다.  
**Nested function**  
function을 nested하게 쓸 수 있다.  
```javascript
 function(a,b){
 function square(x){return x*x}
 return Math.sqrt(square(a)+square(b))
}
```
여기서 중요한 점은 nested된 function은 nestest function의 variable을 읽고 쓸 수 있다  
예를들어 위의 함수에서는 square이 a와 b를 읽고 쓸 수 있다.  
## 🦑 Invoking Function
  
Js에서 function은 정의된다고 실행되지 않는다. => 호출될때 실행된다.  
invoke하는 5가지 방법이 있다.  
1. functions  
2. method  
3. constructor    
4. Indirectly through their call(), apply() method    
5. JavaScript language feature that do not appear like nomal function invocations    
**Function**  
invoked with invocation expression  
invocation expression is consist of function expression(argument)  
```
ex) distance(0,0,2,1)
```
return value → value of invocation expression  
만야 interpreter가 function body 끝에 다다랐다면 undefined return  
혹은 return value가 없거나 undefined면 undefined  
  
non-strict mode : invocation context = global object  
strict mode : undefined  
invocation이 this일 경우, arrow function에서는 this를 상속받기 때문에 defined된다.  
**Method**  
```
 Object.function(argument)
```
return은 regular function invocation과 똑같이 이뤄진다.  
그러나 둘의 다른 점은 invocation context에 있다.  
Property access expression은 object와 property name으로 이루어져 있다.  
 this는 object가 되고 function body는 this 를 통해 object를 refer할 수 있다.  
  
dot으로도 호출 가능하지만 []으로도 호출 가능  
```
o[“m“](x,y)
```
method invocation은 어떠한 object가 어떠한 일을 하는지 clearly하게 보여준다.  
  
this  
arrow function을 제외하고 function에 상속되지 않는다.  
nested function이 호출되었을때, this value는 global value이거나 undefined  
nested function에서 this는 object가 아니다.  
```javascript
let o = {
  m : function(){
    let self = this;
    this === o // true
    self === o // true
    f();
    function f(){
      this === o // false
      self === o // true
    }
  }
}
```
arrow function에서는 this value를 상속받는다.  
**Constructor Invocation**  
new를 이용해 호출하면 constructor Invocation  
argument,invocation context, return value 를 handling하는 부분에서 앞의 Invocation과 차이를 보인다.  
(argument)를 써도 되긴하는데 not nomal.  
  
new로 만들면 prototype에 있는 constructor로 부터 object가 상속된다.  
Constructor function은 initialize object해주고 새로 만들어진 object는 invocation context로 쓰인다. 그러므로 constructor function은 this로 불러와질 수 있다.  
심지어 method invocation처럼 생겨도 new object는 invocation context로 쓰인다.  
보통 return 을 쓰진 않는다. 그냥 body end에 다다르는 것으로 끝난다.  
만들어진 new object는 value of constructor invocation expression  
**Indirect Invocation**  
Js function 는 object, they have methods  
call(), apply() invoke function indirectly  
you can invoke any function as a method of any object  
call() : uses it’s own argument list  
apply() : expect array of value as argument  
**Implicit Function Invocation**    
function invocation처럼 생기진 않았지만 function을 invoke시키는 feature  
invoke할 의도가 없어도 invoke될 수 있으므로 주의해야 한다.  
1) object에 getter setter이 있으면 quering, setting value는 function을 invoke시킬 수 있다.  
2) 만약 object가 string context나 numeric context라면 toString()과 valueOf() 메소드가 호출된다.  
3) iterable object를 loop로 돌리면 몇몇 method가 작동한다.  
4) tagged template literal is function in disguise  
5) Proxy object have their behavior controlled by function  
## 🦑 Function Arguments and Parameters  
Js는 자료형을 명시하지 않는다. 또한 전달 인자의 갯수도 표시하지 않는다. 그렇다면 매개변수보다 적거나 많이 parameter이 전달되었을때 어떻게 처리를 할까?  
1. optional parameters and default  
정의된 인자 수보다 전달된 인자 수가 적으면 뒤의 인자는 undefined로 정의된다. 만약 이러한 조건에 대해 미리 함수를 만들어 놓는다면, undefined가 아닌 다른 동작을 할 수 있다.  
예를 들어 if (a==undefined){a = []}로 한다면 a가 전달되지 않았을때 a는 빈 배열을 갖게 된다. 이 코드는 a = a || []와도 같다.  
생략될 수 있는 인자는 생략될 수 없는 인자의 뒤쪽에 위치해야 한다.  
  
ES6부터는 인자를 생략했을때 default value를 parameter list에 명시할 수 있다.  
function getParam(o, i = []){} 다음과 같이 쓸 수 있다.  
function이 호출될때 default value가 확인된다. (not when it’s defined)  
default가 함수나 변수가 될 수도 있다.  
2. Rest Parameters and Variable-Length Argument List  
Rest Parmeter은 정반대의 상황이다. 정의된 인자 수보다 전달된 인자 수가 많다면 Rest Parameter이 쓰인다.  
Rest Parameter은 … 으로 표현된다. 그리고 파라미터의 마지막에 위치해야 한다. 첫번째 파라미터는 … 이여서는 안된다. 이것은 마지막 파라미터가 항상 배열이라는 소리이다. array는 empty일 수는 이지만 undefined가 되진않는다.  
3. Argument Object  
ES6이전에는 Argument Object를 이용했다.  
Argument Object는 유사 배열 객체이고 인덱스 숫자를 통해 전달인자를 가져온다.  
그러나 현재는 Rest Parameter이 쓰이므로 사용을 지양해야 한다.  
*참고: argument가 x일때 x 혹은 argument[0]을 이용하면 첫번째 인자에 접근 할 수 있다. 이와 마찬가지로 배열처럼 접근한다. 또한 배열처럼 length property를 가지고 있는데 이를 통해 인자의 수를 알 수 있다.
4. Spread Operator  
spread operator은 array의 element들을 context로 spread out 시킨다.  
text가 되기때문에 value간의 비교는 불가하다.  
5. Destructuring Function Argument into Parameter    
만약 argument에 인자를 넣는다면 그 인자는 함수 정의때 declared된다.  
parameter에 []를 넣는다면 array가 들어갈 것이라는 것을 명시한 것이다.  
parameter의 name을 통해 접근할 수 있다.  
```
vector(x,y){
 return [x[0]+y[0],x[1]+y[1]]
}
```
쓸때 언제나 property name은 left에 두고 parameter의 name은 오른쪽에 둔다.  
```
({x:x1 , y:y1},{x:x2,y:y2}){return {x:x1+x2,y:y1+y2}}
```
6. Argument type  
매개변수는 정해진 형식이 없기 때문에 이름을 잘 표시하거나 각 매개변수마다 주석을 써주면 도움이 된다.  
 모든 object는 매개변수와 사용하려는 의도가 다를 경우, 의도대로 매개변수를 바꿔준다.(예를 들어 모든 object 안에있는 toSting()은 문자열로 변수를 바꿔준다.) 그러나 그렇지 않은 경우도 있기 때문에 의도한 값이 아닐때는 그에 대한 처리를 해주는 편이 낫다. => Error()을 던져준다.  
## 🦑 Function as Value  
함수의 가장 중요한 점은 invoke와 define 이다. (다른언어에서도 마찬가지지만)  
자바스크립트의 함수는 문법뿐만이 아니라 값이기도 하기때문에  
value, function, array에서 데이터가 될 수 있다.  
```javascript
function square(x){return x*x}
var s = square;
s(2) // 4
var o = {square:function(x){return x*x}}
o.square(2) // 이 경우 메소드이다.
var a = [square,20]
a[0](a[1])
```
Defining your own Function Properties  
Js에서 함수는 property를 가질 수 있다.  
정적변수가 필요할때는 전역변수보다는 함수의 property를 사용하는 편이 낫다.  
unique.counter =0 //초기화 함수 정의는 맨 위로 끌어올려지기 때문에(hoisted) 먼저 할당할 수 있다.  
```
function unique(){
  return unique.counter++;
}
```
## 🦑 Function as Namespaces  
Js는 함수 단위의 유효범위를 갖는다. 함수 내에서 정의된 변수는 (중첩된 function까지도) 접근이 가능하지만 함수 밖으로는 접근이 불가하다. 함수 밖에 정의된 변수는 전역변수라고 부른다.  
간단한 임시 네임스페이스처럼 작동하는 함수는 정의하는 것은 전역변수 네임스페이스를 더럽히지 않는다.  
만약 어떤 모듈을 다른 프로그램들이 공유해서 쓴다고 할때 모듈의 코드 내의 변수가 값이 바뀔때 충돌이 날 수 있다. 그러나 모듈을 아예 함수 안에 두고 함수를 호출하면 이러한 문제를 해결할 수 있다.  
```
function module(){
  여기에 모듈을 정의한다.
}
module(); // 함수를 호출해서 사용한다.
```
## 🦑 Closure  
Js도 어휘적 유효범위를 쓴다. =>함수가 정의된 시점에서 변수의 유효범위를 사용하여 함수가 실행된다.  
이를 위해 함수 객체는 코드와 함께 유효범위에 대한 체인 참조도 갖고 있다. => 참조를 가지고 있으므로 클로저다  
함수 객체와 함수의 변수가 해석되는 유효범위를 클로저라고 한다.  
함수 대부분은 유효범위 체인에 의해 호출되지만 클로저는 유효범위와 다른 범위의 체인에서 사용될 수 있다.  
```
let scope = "global scope";
funcion check(){
  let scope = "local scope"
  function f(){return scope}
  return f
}
check()()
```
이때 출력되는 값은 “local scope“이다.  
check()일때 f를 반환한다. f는 이미 check() scope 안에 들어가 있으므로 지역변수인 scope에 접근이 가능하다. 그러므로 출력값은 local scope가 나오게 된다.  
scope와 같은 내부 변수는 여러 클로저가 공유할 수 있다. 즉, 작은 함수 안의 정의된 중첩함수 들은 같은 유효범위 체인을 공유한다.  
이는 중요한 기법이지만 정작 공유되어선 안되는 변수들이 공유되기도 한다.  
또한 this는 Js property지, 변수가 아니기 때문에 중첩된 함수에서 바깥쪽 this로의 접근은 불가하다.  
## 🦑 Function Properties, Methods, Constructor  
함수가 객체이기 때문에 property(length, prototype), method(call(), apply(), bind(), toString()),Function() 생성자를 갖는다.  
1. Length() property  
length property는 해당 함수가 요구하는 전달인자의 개수이다.  
2. name property  
함수가 정의될때 name, 변수의 이름을 정의하거나 unnamed function이 쓰였을때 그것이 언제 시작하는지 확인한다. 디버깅이나 에러메세지를 쓸때 쓰인다.  
3. prototype prototype  
프로토타입 객체를 참조한다. 모든 함수는 서로 다른 프로토타입 객체를 가지고 있다.  
4. call(), apply()  
함수를 다른 객체의 메소드처럼 간접적으로 호출될 수 있게 한다.  
만약 f()를 객체 o의 메서드로 호출하고 싶다면  
```
f.call(o)
f.apply(o)
```
다음과 같이 쓴다   
위의 코드는 아래와 같다  
```
o.m = f
o.m();
delete.o.m;
```
만약 arrow function을 쓴다면 call(), apply()를 못쓴다. 쓴다면 첫번째 argument가 무시된다.  
첫 argument 이후 인자는 모두 함수 인자가 된다.  
```
f.call(o,1,2)
```
apply 함수는 배열형태여야 한다.  
```
f.apply(o,[1,2])
```
ES6에서는 …을 쓰므로 잘 보이지 않지만 ES5 코드에서 apply()를 이용한다.  
5. bind() method  
bind()는 객체와 함수는 묶는다.(object, function)  
function f 가 bind()를 호출하면서 o를 전달하면 bind는 새로운 함수를 반환한다. 반환된 새 함수를 호출하면 객체 o안의 f를 호출한다.  
```
function f(y){return this.x+y}
let o = {x:1}
let g = f.bind(o)
g(2) // => 3
```
마찬가지로 arrow function에서는 실행되지 않는다. 보통 non-arrow function에서 실행된다.  
또한 bind() 메소드는 파셜 어플리케이션을 구현하는데 첫번째 인자 이루 인자는 this와 함께 함수의 인자로 바인딩된다.이를 curring이라고 부르기도 한다.  
6. toString()  
함수선언문 다음에 나오면 문자열을 반환한다.(전체 소스코드를 반환한다)  
7. Function() 생성자  
함수는 Function() 생성자를 통해 생성할 수도 있다.  
```
let f = new Function(“x“,”y”,”return x+y;”); === let f = function(x,y){return x+y;}
```
마지막 인자는 함수의 몸체이고 나머지는 함수의 인자이다.  
또한 함수의 이름을 정하지 않는 익명함수이다.  
특징으로는  
1. 동적으로 함수를 생성하고 실행시간에 컴파일된다.  
2. 호출될때마다 새로운 함수객체를 생성한다.  
3.어휘적 유효범위를 사용하지 않는다.  
```
let scope = "global"
function construct(){
  let scope = "local"
  return new Function("return scope"); // 지역 유효범위에 포함되지 않는다.  
}
construct()(); // =>global
```
## 🦑 Functional Programming  
자바스크립트는 함수형 언어는 아니지만 함수를 객체로 취급하므로 함수형 프로그래밍이 가능하다.  
그렇다면 자바스크립트가 함수형 프로그래밍을 하기 위해 필요한 기법이 무엇일까?  
Processing array with function  
map() 과 reduce를 이용하여 array 안에 있는 요소들을 함수형 프로그래밍으로 처리할 수 있다.  
```javascript
let sum = function(x,y){return x+y;} // === (x,y)=>x+y
let square = function(x){return x*x;}

let data = [1,1,3,5,5]
let mean = data.reduce(sum)/data.length
let deviation = data.map(function(x){return x - mean})
let stddev = Math.sqrt(deviation.map(square).reduce(sum)/(data.length-1))
```
2. Higher-Order Function  
하나 이상의 함수를 받고 새로운 함수를 return한다.  
다음의 not함수는 함수 하나를 argument로 받고 새로운 함수 f를 return 하고 있으므로 고차함수가 된다.  
```
function not(f){
  return function f(){
    let result = f.apply(this, argument) // (f, [1,1,3,5,5])
    return !result // => 결과를 부정
  };
}


let even = function(){
  return x % 2 ===0;
}
let odd = not(even);
[1,1,3,5,5].every(odd);
```
3. Partial Application of Functions  
bind() 메소드는 함수를 어떤 객체레 바인딩하고 부분적으로 인자들을 적용한다.  
부분적으로 인자를 적용할때 부분인자를 왼쪽에 적용하는게 이는 인자들이 대상 함수의 parameter의 시작점에 위치한다는 것이다. 부분인자들은 또한 오른쪽으로 적용될 수 있다.  
```
function partialLeft(f, ... outerArgs){
  return function(...innerArgs){
    let args = [...outerArgs, ...innerArgs];
    return f.apply(this,args);
  }
}
function partialRight(f, ...outerArgs){
  return function(...innerArgs){
    let args = [...innerArgs,...outerArgs];
    return f.apply(this.args)
  }
}
function partial(f,...outerArgs){
  return function(...innerArgs){
    let args = [...outerArgs];
    let innerIndex=0;
    for(let i =0; i<args.length;i++){
      if(arg[i] === undefined) arg[i] = innerArgs[innerIndex++];
    }
    args.push(...innerArgs.slice(innerIndex));
    return f.apply(this,args);
  };
}
const f = function(x,y,z){return x*(y-z)}
partialLeft(f,2)(3,4) // 2*(3-4)
partialRight(f,2)(3,4) // 3*(4-2)
partial(f,undefined,2)(3,4) // 3*(2-4)
```
4. Memoization  
함수형 프로그래밍에서 이전 계싼의 결과를 캐시해두는 캐싱 방식을 메모리제이션이라고 한다. 함수를 인자로 받아들이고 메모리제이션을 적용시킨 새로운 함수를 반환한다.  
```
function memorize(f){
  const cache = new Map();
  return function(...args){
    let key = args.length + args.join("+")
    if(cache.has(key)){
      return cache.get(key);
    }else{
      let result = f.apply(this,args)
      cache.set(key,result);
      return result;
    }
  }
}
```
memorize는 캐쉬 값을 저장하는데 사용할 객체를 생성하고 memorize 지역변수에 객체를 할당한다. 즉, 객체는 변환되는 함수의 내부에만 존재한다. 캐쉬에 값이 있다면 값을 반환한다.  
***  
임시question입니다, 생각할 시간을 주시면 답변(A)를 가지고 돌아오겠습니다🦜   
***  
Q1. invoke bind() method of nested function to define new function 8.1.2  
Q2. opposite effect to use … operator rather than function invocation 8.3.4  
Q3. Destructuring Function argument into parameter 8.3.5  
Q4. 저렇게 프로포티로 썼을때 전역변수보다 나은점? func as value 8.4.1  
Q5.  Higher-Order Function에서 왜 결과값을 부정하는지, 부정했는데 왜 결과값은 true가 나오는지.  
A5. 코드를 보면 even은 짝수인지를 검사하는 함수이다. 짝수가 아니므로 f.apply(this, argument) 한 even에서는 false가 나오고, 감싸고 있는 함수가 not으로 “even이 아닌게 맞는가?”를 묻고 있으니 답은 yes가 나와야 한다. let odd = not(even) 에서 볼 수 있다.  
Q6. Partial Application of Functions, Memorization 다시 코드 보기  
