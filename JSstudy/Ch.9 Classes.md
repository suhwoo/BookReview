# Classes  
***  
서론  
***  

각 객체는 다른 객체와 구별되는 property의 고유한 집합으로 취급된다.  
그러나 class를 정의해서 객체들끼리 특정 property를 공유할 수 있도록 하기도 한다.  
class의 구성요소를 인스턴스라고 한다. 인스턴스는 상태를 정의하거아 저장하는 property와 동작을 정의하는 property들을 가지고 있다. 이러한 동작 메소드는 클래스 수준에서 정의되고 그 안에 있는 모든 인스턴스가 사용가능하다.  
두 객체가 같은 prototype 객체로 부터 property를 상속받았다면 둘은 같은 클래스의 인스턴스이다. 같은 prototype를 상속하면 두 객체가 같은 생성자에 의해 생성되고 초기화되었다는 것을 의미한다.  
ES6부터 class를 쉽게 만들 수 있는 방법이 제공되었다.  
Js의 class와 prototype기반 상속 매커니즘은 다른 언어의 상속과는 다르다.  
***  
본론  
***  
## 🦑 Classes and Prototype  
class는 같은 포로토타입 객체로부터 프로퍼티를 상속받은 객체의 집합이다.  
인스턴스는 초기화작업이 필요하기 때문에 객체를 생성할때마다 초기화가 필요하다.  
```
function range(from,to){
  let r = Object.create(range.methods);
  r.from = from
  r.to = to
  return r;
}
range.methods = {
  includes(x){return this.from <= x && x<= this.to},*[Symbol.iterator](){
    for(let x = Math.ceil(this.from); x<= this.to ; x++) yeild x;
  }
  toString(){return "("+this.from+ "..."+this.to+ )"}
}

let r = range(1,3);
r.includes(2);
r.toString()// => "(1...3)"
[...r] // => [1,2,3]
```
위의 코드는 클래스 프로토타입을 저장하기 위해 range함수의 range.methods프로퍼티를 사용하고 있다.  
또한 range 객체에 from 프로퍼티와 to 프로퍼티를 저장하고 있는데 이 프로퍼티들은 range객체의 고유한 상태를 저장하고 공유되거나 상속되지 않는다. range.methods의 모든 함수는 상속되고 공유되며 from,to를 참조하기 위해 this를 이용하고 있다.  
## 🦑 Classes and Constructor  
앞선 코드는 생성자를 정의하지 않았기 때문에 관용적인 방법은 아니다.  
생성자는 새로 생성된 객체를 초기화하는 용도로 사용되는 함수이다.  
생성자함수는 새 객체의 상태를 초기화하는데 집중한다.  
생성자 호출의 핵심적인 특징은 생성자의 prototype property가 새 객체의 prototype이 된다는 것이다. 이는 한 생성자를 통해 생성된 모든 객체를 같은 객체를 상속하고 그러므로 같은 클래스의 멤버이다.  
9-2에서는 앞의 코드가 어떻게 생성자 코드로 변할수 있는지, class keyword가 아닌 이전의 방법을 알아본다.  
```
//새로운 Range 객체를 초기화하는 생성자 함수일뿐.   
//return도 없다.  
function Range(from,to){
  this.from;
  this.to;
}
//모든 객체가 이 객체를 상속하려면 property 이름이 "prototype"이여야 한다.
Range.prototype = {
  [Symbol.iterator]: function*(){
    for(let x = Math.ceil(this.from);x <= this.to;x++) yield x;
  },
  toString: function(){return "("+this.from+"..."+this.to+")"}
};
let r = new Range(1,3)
r.includes(2) // => true
r.toString() // "(1...3)"
[...r] // => [1,2,3]
```
9-1 과 9-2 비교  
1.	클래스와 생성자의 이름은 대문자로 시작하는 반면 일반 함수와 메소드는 소문자로 이루어져 있다.  
2.	Range 생성자는 new를 이용해 생성된다. 따라서 새 객체를 생성하기 위한 다른 행동은 필요없다. 생성자를 호출하면 새로운 객체는 자동을 생성된다.   
3.	Range.prototype은 임의로 지어진 이름이 아닌 정해진 규칙이다.  
4.	변하지 않은 부분: range의 메소드들은 같은 방식으로 정의되고 호출된다.  
5.	두 예시 모두 생성자나 메소드에서 arrow function이 쓰이지 않았다. 왜냐하면 arrow function은 prototype을 상속받지 않기 때문에 생성자를 쓰지 못한다. 또한 this의 범위도 다르다.  
**9.2.1 Constructor, Class Identity, and instance of**  
두 객체는 같은 프로토타입 객체를 상속한 경우에만 같은 클래스의 인스턴스이다. 서로 다른 생성자함수라도 같은 프로토타입 객체를 가리키는 prototype 프로퍼티를 가질 수 있다.  
대부분 생성자 함수의 이름은 클래스 이름과 같지만 아닐 수도 있다.  
객체가 어떤 클래스에 속한 것인지 검사할 때 instanceof를 사용한다.  
**r instanceOf Range**  
생성자를 구별하진 않고 프로토타입을 상속하는지를 검사한다.이는 상속받은 객체여도 마찬가지이다.  
예를 들어 Range 생성자는 구별하지 않는다. Range.prototype이지만 생성자가 Strange여도 true로 반환한다.  
만약 생성자가 없어도 isPrototypeOf()를 쓰면 프로토타입에 속하는지 알 수 있다.  
**9.2.2 Constructor Property**  
함수가 생성자로 호출되려면 prototype property가 있어야 한다. 모든 함수는 자동으로 prototype property가 설정된다. 이렇게 생성된 prototype property는 constructor property하나만 가진 객체이다.  
constructor은 자신의 생성자를 가리킨다. 그러나 만약 새로운 prototype을 만든다면 본래 있었던 prototype을 덮어쓰므로 constructor이 지워진다. 이는 명시적으로 constructor을 추가함으로써 해결할 수 있다.  
```
Range.prototype = {
  constructor: Range,
  /*methods*/
}
```
또한 이전의 코드에서는 prototype object를 확장하기 위해서  
```
Range.prototype.includes = function(){ ~~}
Range.prototype.toString = function(){~~}
```
다음과 같이 확장하기도 했다.  
## 🦑 Class with the class keyword  
class keyword는 ES6부터 생겼다.   
이전의 코드를 class로 바꾸면 다음과 같다.  
```
class Range{
  constructor(from,to){
    this.from
    this.to
  }
  includes(x){return this.from <= x && x <= this.to}
  *[Symbol.iterator](){
    for(let x =Math.ceil(this.from); x <= this.to; x++) yield x;
  }
  toString(){return '(${this.from}...${this.to})'}
}
let r = new Range(1,3)
r.includes(2)//true
r.toString()//"(1...3)"
[...r]//=>[1,2,3]
```
이와 같은 코드는 prototype-based도 위반하지 않았고 여전히 constructor도 있다.  
●	form은 class className{}  
●	class body는 function이 빠진 method definition을 포함한다. 또한 method 사이에 콤마가 붙지 않는다.  
●	constructor은 class의 variable을 지정한다.  
●	심지어 class를 초기화 할 필요가 없다면 constructor을 생략해도 좋다.  
만약 subclass 혹은 상속받는 클래스를 만들고 싶다면 단지 extends를 붙이면 된다.  
```
class Span extends Range{
  constructor(start, length){
    if (length <= 0){
      super(start,start+length);
    }else{
      super(start+length ,start);
    }
  }
}
```
class내의 body는 strict mode이다. 변수를 쓰기 전에 선언해야 하며 with statement를 쓸 수 없고 octal integer literal을 쓸 수 없다.  
function 정의와 달리 class는 hoisted되지 않는다. 그 말은 class 선언하기 전에 class는 instantiate될 수 없다.  
**9.3.1 Static Method**  
class 내에 있는 method를 static keyword를 통해 선언할 수 있다. static method는 constructor의 property로 선언된다. 그러므로 constructor을 이용해 실행시켜야 한다.  
```
static parse(s){}
let r = Range.parse(); // =>return new Range Object
r.parse() //=>typeError
```
**9.3.2 Getter, Setter, and Other Method forms**  
마치 object처럼 getter과 setter을 선언할 수 있다.  
대부분의 경우, shorthand method definition syntax는 class body에서도 허용된다. 그리고 * 을 사용하는 generator method의 경우도 마찬가지이다.  
```
*[Symbol.iterator](){
  for(let x = Math.ceil(this.form);x <= this.to; x++) yield x;
}
```
**9.3.3 Public,Private, ans Static Field**  
ES6에서는 class creation과 static method만 만들고 필드를 정의하는 syntax는 정의하지 않았다. 그러므로 필드를 정의하고 싶다면 constructor 안이나 다른 method의 안, 혹은 class 밖에 정의해야 한다.  
그러나 표준화가 진행중이고 크롬 등과 같이 몇몇 곳에서는 아래와 같이 쓰이고 있다.  
```
class Buffer {
  size =0;
  capacity =4069;
  buffer = new Uint8Array(this.capacity)
}
```
다음과 같이 this를 안쓰고 바로 class body에 선언할 수 있다.  
그러나 this를 쓰는 편이 좋다. 왜냐하면 이렇게 하면 먼저 class definition의 맨 위에 initializers up 할 수 있고 가독성있게 쓸 수 있다.  
또한 표준화되는 것은 private instance field이다.  
name 앞에 #을 붙여서 쓸 수 있다.  
class 내에선 접근이 가능하지만, class body 외부에서는 접근, 혹은 read도 불가능하다. 그러나 method에서 return한다면 get을 통해 readonly가 가능하다. 미리 #size 를 명시해줘야 this.#size가 가능하다.  
마지막으로 만약 static을 public이나 privet field를 명시하기 전에 적는다면 그 필드는 properties of constructor function이 된다.  
## 🦑 Adding Methods to Existing Classes  
프로토타입 기반 산속 메커니즘은 동적이다. 객체는 자신의 프로토타입에서 프로퍼티를 상속받는데 이는 자바스크립트 객체의 프로토타입에 메서드를 추가함으로써 클래스를 확장할 수 있다는 뜻이다.  
Object.prototype에도 메서드를 추가하면 모든 객체에서 추가된 메서드를 사용할 수 있다. 그러나 이 방법은 for/in 루프에서 열거될 수 있기 때문에 권장되지 않는다. 또한 prototypes 에 built-in으로 추가하는 것도 권장되지 않는데 왜냐하면 이후 새로운 버전에서 같은 이름에 의한 충돌문제가 발생할 수 있기 때문이다.  
## 🦑 Subclasses  
클래스 A와 B가 있을때 B가 A를 확장하거나 상속받을 경우, A를 슈퍼클래스, B를 서브클래스라고 한다.  
B는 A의 모든 메서드를 상속받는데 B의 메서드를 재정의했을때 A의 메소드를 호출할 수 있다. 이를 method chaining이라고 한다. 이와 마찬가지로 B의 생성자가 A의 생성자를 호출할 수 있는데 이를 constructor chaining이라고 한다.  
클래스 계층 구조를 나타낼때는 추상 클래스가 유용할 수 있다. 추상클래스는 실제로 구현되지 않는 method가 하나이상 있는 클래스로 추상클래스를 상속받은 서브클래스가 실제 구현을 하게 된다.  
**9.5.1 Subclasses and Prototypes**  
Range가 superclass이고 Span이 subclass일때 둘의 instance는 같고 Span의 prototype은 Range의 prototype를 상속받는다. 이렇게 상속받는 것은 아래의 코드를 통해 받을 수 있다.  
```
Span.prototype = Object.create(Range.prototype)
```
Span의 생성자는 Span.prototype이지만 위의 코드는 Range의 prototype으로 만듬으로써 Range는 Span.prototype과 Range.prototype 둘 다에서 상속받는다.  
Arobust subclassing mechanism needs to allow classes to invoke the methods and constructor of their superclass  
=>ES6 solved this with super keyword  
**9.5.2 Subclasses with extends and super**  
ES6 이후로 extends를 이용해서 superclass를 만들 수 있다.  
```javascript
class EZArray extends Array{
  get first(){return this[0];}
  get last(){return this[this.length-1];}
}

let a = EZArray();
a instanceOf EZArray // true
a instanceOf Array // true
a.push(1,2,3,4) // [1,2,3,4]
a.pop() // [1,2,3]
a.first() // 1 //subclass
a.last() // 3 //subclass
a[2] // 2 // Array
Array.isArray(a) //true //subclass instance really is an array
EZArray.isArray(a) //true //subclass inherits static methods
using superclass
class TypedMap extends Map{
  constructor(keyType,valueType,entries){
    if(entries){
      for(let [k,v] of entries){
        if(typeof k !== keyType || typeof v !== valueType){
          throw new TypeError('Wring type for entry [${k},${v}]');
        }
      }
    }
    super(entries);
    this.keyType = keyType;
    this.valueType = valueType;
  }
  set(key, value){
    if(this.keyType && typeof key !== this.keyType){
      thrownew TypeError('${key} is not of type ${this.keyType}');
    }
    if(this.valueType && typeof value !== this.valueType){
      throw new typeError('${value} is not of type ')
    }
  }
}
```
위의 코드에서 super(entries)를 통해 superclass인 Map의 constructor에 entries를 전해주는 것을 볼 수 있다.   
그 후 TypedMap에서 this를 통해 subclass의 변수를 초기화한다.   
**super**  
- 만약 extends를 이용해 class를 만들었다면 superclass의 constructor을 쓰기 위해서는 super() keyword를 써야한다.  
- 만약 constructor을 따로 명시하지 않았다면 defulat로 constructor이 하나 나오는데 value가 무엇이든 간에 super로 전달된다.  
- super을 쓰기 전까지는 자기자신 class의 value를 쓰기 위해 this를 선언할 필요가 없다.  
- new.target은 function에서는 정의되지 않았다 . 그러나 new.target은construcor이 호출될때 reference된다. 만약 subclass가 super을 통해 super class를 불렀다면 superclass에는 subclass의 constructor과 new.target을 보게 된다.  
**9.5.3 Delegation Instead of Inheritance**  
extends를 이용해서 상속받아서 subclass를 만들수도 있지만 원하는 동작을 다른 class의 instance로 만들고 delegating해서 자신의 class 안으로 가져올 수도 있다.  
subclassing을 이용해 새로운 class를 만드는게 아니라 다른 class들을 wrapping, composing한다. 이러한 방법을 composition이라고 한다.  
```javascript
class Histogram{
  constructor(){this.map = new Map();}
  count(key){return this.map.get(key) || 0;}
  has(key){return this.count(key) > 0;}
  get size(){return this.map.size}
  add(key){this.map.set(key,this.count(key)+1)}
  delete(key){
      let count = this.count(key);
      if(count === 1){
        this.map.delete(key)
      } else if(count>1){
        this.map.set(key, count -1);
      }
  }
  [Symbol.iterator](){return this.map.keys();}
  
  keys(){return this.map.keys();}
  values(){return this.map.values();}
  entries(){return this.map.entries();}
}
```
**9.5.4 Class Hierarchies and Abstract Classes**  
생각보다 상속을 위해 조합을 많이 쓰고 extends는 잘 안쓴다.  
간혹 여러 levels의 subclassing이 필요한때가 있다. => 여러 다른 sets으로 class를 수직적으로 나타낼 수 있다.  
Abstract class를 이용해 각 관련있는subclasses을 정의한다. 모든 서브클래스가 무엇을 상속받고 공유하고 있는지 정의한다. subclass는 그저 자신이 쓸 function만 재정의하면 된다.  
***  
<임시question입니다, 생각할 시간을 주시면 답변(A)를 가지고 돌아오겠습니다🦜 >  
***  
Q1. 9.2.1 나중에 다시 이해하기… instanceOf와 isPrototypeOf()의 차이를 모르겠음.. 생성자 이름은 상관없이 프로퍼티만 보지만 생성자를 사용해서 클래스를 구별한다?  
Q2. 9.3.2 generator method? => A2. CH12 참고  
Q3. 9.5.3 Delegation Instead of Inheritance  
