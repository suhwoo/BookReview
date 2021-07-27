# Metaprogramming
***  
서론  
***  
이 장에서는 재사용 라이브러리 사용자와 object의 세부정보를 알 수 있는 고급 javaScript 기능에 대해 다룬다  
메타프로그래밍은 다른 코드를 조작하기 위해 쓰인다.  
***  
본론  
***  
## 🦑 Property Attributes  
JavaScriptd property는 name과 value가 있고 또한 어떻게 작동할건지와 어떻게 쓸 수 있는지에 대한 3가지 속성이 있다.  
1. writable : value가 바뀔 수 있는지  
2. enumerable : for/in loop나 Object.keys()에 의해 enumerable될 수 있는지.  
3. configurable : 속성이 삭제되거나 바뀔 수 있는지.  
API for querying and setting property attributes => why it is important for library author?  
1. allows add method to property objects and make them enumerable  
2. allows defining properties that can not be changed or deleted (lock down)  
“accessor“속성은 getter과 setter method를 가진다 , getter과 setter속성을 property attribute로 여길것이다. => 이 로직에 따르면 accessor속성에는 get, set, enumerable, configurability로 이루어져 있다.(value와 writable은 data property)  
이 4가지 속성 set을 보여주기 위해 property descriptor이라는 object를 사용한다.  
Object.getOwnPropertyDescriptor()을 통해 property descriptor을 얻는다.  
```javascript
Object.getOwnPropertyDescriptor({x:1},"x") // {value:1,writable:true,enumerable:true,configurable:true}
const random = {
  get octet(){return Math.floor(Math.random()*256);}
}

Object.getOwnPropertyDescriptor(random,"octet"); // {get:/*func*/,set:undefined,enumerable:true,configurable:true}
Object.getOwnPropertyDescriptor() 는 오직 own properties에서만 가져온다.
To set attribute of properties or to create new property with specified attribute, call Object.defineProperty()
let o = {};
//add non-enumerable data property x with value 1
Object.defineProperty(o,"x",{
  value:1,
  writable:true,
  enumerable:false,
  configuralbe:true
})

o.x //1
```
만약 속성을 생략한다면 undefoned나 false가 된다.  
이미 존재하는 value의 속성을 생략한다면 그 부분만 바뀌지 않는다.  
상속된 value는 바꿀 수 없다.  
만약 하나 이상의 value를 바꾸고 싶다면 Object.defineProperties()를 쓴다.  
```javascript
let p = Object.defineProperties({},{
  x: {value:1 , writeable:true,enumerable:true,configurable:true},
  y: {
    get(){return Math.sqrt(this.x*this.x + this.y+this.y);},
    enumerable: true,
    configurable: true
  }
})
```
만약 property를 만들거나 바꿀때 문제가 생긴다면 Object.defineProperty(), Object.definePRoperties 모두 TypeError를 throw한다.  
## 🦑 Object Extensibility  
object가 extensible할지 아닐지 결정하기 위해서는 Object.isExtensible()을 통해 결정할 수 있다.  
만약 non-extensible하려면 Object.preventExtensions().  
이러한 적용을 하면 새로운 property를 추가하려 하면 TypeError를 반환할 것이다.  
한번 non-extensive하게 만들면 다시 extensive하게 만들 수 없다.  
이와 비슷한 작용을 하는 것으로 Reflect.inExtensible(), Reflect.preventExtensions()이 있다.  
Object.seal()은 Object.preventExtension과 비슷하다. + makes all od own properties of that object nonconfigurable  
Object.freeze() locks objects down even more tightly. also makes all of the object’s own data properties read only.  
## 🦑 The prototype Attribute  
object의 prototype 속성은 어떤 속성을 object에 상속할 지 정한다.  
prototype attribute는 object가 만들어질때 set된다. object가 creat될때 Object.creat()의 첫번째 argument를 통해 prototype를 지정할 수 있다.  
Object.getPrototypeOf()를 통해 해당 object의 prototype이 무엇인지 알 수 있다.  
Reflect.getPrototypeOf()를 통해 해당 object가 다른 object의 prototype인지 알 수 있다.  
Object.setPrototypeOf()를 통해 prototype를 바꿀 수 있다. 비슷한 함수로 Reflect.setPrototype가 있다.  
## 🦑 Well-Known Symbols  
ES6부터 Symbol이 쓰이기 시작했다. 가장 큰 이유는 safely add extensions to the language without breaking compatibility with code already deployed on the web.  
**14.4.1 Symbol.iterator and Symbol.asyncIterator**  
Symbol.iterator과 Symbol.asyncIterator은 object와 classes를 iterable하게 하거나 asynchronously iterable하게 한다. (CH 12, CH 13.4.2)  
**14.4.2 Symbol.hasInstance**  
ES6에서 instance의 오른쪽이 [Symbol.hasInstance] method를 가진 object일때 method가 왼쪽의 value를 argument로 invoked 된다.  
```javascript
let uint8 = {
  [Symbol.hasInstance](x){
    return Number.isInteger(x) && x >= 0 && x <= 255;
  }
};
128 instanceof uint8 //true
```
**14.4.3 Symbol.toStringTag**  
만약 일반 object에서 toString()을 부른다면 리턴값은 [object Object]가 될 것이다.  
```javascript
{}.toString() // [object Object]
```
만약 instances of built-in types의 method로 Object.prototype.toString() 을 부른다면 다른 결과가 나온다.  
```javascript
Object.prototype.toString.call(()=>{}) // [object Function]
```
이를 통해 Object.prototype.toString().call()을 통해 “class attribute“를 얻을 수 있다는 것을 알 수 있다.  
classof()는 object의 타입과 관계없이 typeof 와 같은 역할을 한다.  
```javascript
classof(1)
```
**14.4.4 Symbol.species**  
ES6이후로 you can extend any built-in class simply by using class and extends keyword  
```javascript
class EZArray extends Array{
  get first(){return this[0]}
  get last(){return this[this.length-1]}
}
let e = new EZArray(1,2,3)
e.last // 3
```
concat()과 같은 Array defined method의 경우 Array를 extend해 만들어진 EZArray는 return instances of the subclass. ES6 이후의 Array() constructor은 Symbol.species라는 property를 갖는다. extends로 subclass를 만들때 subclass constructor가 superclass constructor를 상속받는다. map()과 같은 method는 기존의 array를 생성해서 반환되는 대신, this.constructor[Symbol.species]()를 통해 새로운 array를 만든다.  
**14.4.5 Symbol.isConcatSpreadable**  
concat()은 Symbol.species를 써서 returned array를 위해 어떤 constructor을 쓸지 결정한다. 또한 concat()은 Symbol.isConcatSpreadable로도 쓰인다. 7.8.3에서 언급했던것과 같이 nonarray argument는 새로운 array에 append된다. 그러나 this array나 any array 는 flattened되거나 “spread“된다.  
ES6이후에 Array.isArray()는 concat()이 object이고 Symbol.isConcatSpreadable이라는 property가 있다면 argument가 spread한지 판단하는 boolean value를 return 한다.  
**14.4.6 Pattern-Matching Symbols**  
11.3.2 에서는 RegExp를 통해 패턴매칭을 했다. match(), matchAll(), search(), replace(), spilt()는 Symbol.match,Symbol.search, Symbol.search 등과 같다.  
RegExp는 패턴매칭에 강력하지만 복잡할 수 있고 fuzzy matching에는 맞지 않는다.  
```javascript
class Glob{
  constructor(glob){
    this.glob = glob
    let regexpText = glob.replace("?","([^/])").replace("*","([^/]*)")
    this.regexp = new RegExp(`^${regexpText}$`,"u")
  }
  toString(){return this.glob}
  
  [Symbol.search](s){return s.serach(this.regexp);}
  [Symbol.match](s){return s.match(this.regexp);}
  [Symbol.replace](s, replacement){
    return s.replace(this.regexp,replacement)
  }
}
let patteren = new Glob("docs/*.txt");
"docs/js.txt".search(patteren) // 0
"docs/js.hym".search(patteren) // -1 //not match
let match = "docs/js.txt".match(patteren)
match[0] // "docs/js.txt"
match[1] // "js"
```
**14.4.7 Symbol.toPrimitive**  
3.9.3에서 object를 primitive value로 바꾸기 위해서 세가지 방법이 있었다. 결과값이 문자열로 기대될때 toString()을 호출하고 없으면 valueOf()에서 정의되었다. 만약 숫자값을 기대한다면 valueOf()를 호출하고 없으면 toString()을 호출했다.  
ES6에서는 Symbol.toPrimitive를 통해 default object-to-primitive behavior 을 override했다. to do this define a method with this Symbolic name.  
- argument가 string이라면 변환의 결과로 string을 원하는 것이다.  
- argument가 number이라면 변환의 결과로 numeric value를 원하는 것이다.  
- argument가 default라면 숫자 혹은 문자열로 변환한다.  
**14.4.8 Symbol.unscopables**  
마지막으로 다룰 것은 with statement로 인한 호환성문제이다. 이전에서 언급됐듯이 with는 scope문제를 해결하기 위해 쓰였지만 새로운 함수가 추가될때 호환성 문제때문에 권장되지 않았다. 그러나 몇몇 코드에는 아직 with statement가 남아있다. ES6부터 Symbol.unscopables 를 통해 이러한 문제를 해결했다. 기존의 코드를 유지하면서 Symbol.unscopables를 넣음으로써 newest Array methods를 가져올 수 있다.  
let newArrayMethods = Object.keys(Array.prototype[Symbol.unscopables])  
## 🦑 The Reflect API  
Reflect 객체는 편리한 함수의 집합을 단일 네임스페이스로 정의한다.  
Reflect기능은 새로운 무언가를 실행하진 않지만 API들을 하나로 그룹화한다.  
- Reflect.apply(f, o, args) : invoke function f as method of o and passes values in args
- Reflect.construct(c, args, newTarget) : invoke constructor c and passed args as argument. newTarget is optional. it uses as new.target
- Reflect.defineProperty(o, name, descriptor) : define property o with named name. descriptor은 위에서 언급되었듯이 property의 속성을 정한다.
- Reflect.deleteProperty(o, name) : delete name of property from object o
- Reflect.get(o, name, receiver) : o에서 name property를 가져온다. receiver이 정의되었다면 get대신에 receiver을 통해서 가져온다.
- Reflect.getOwnPropertyDescriptor(o, name) : o에서 name property의 descriptor을 가져온다.
- Reflect.getPrototypeOf(o) : return prototype of o
- Reflect.has(o, name) : o에 name property가 있는지 확인
- Reflect.isExtensible(o) : o가 extensible하다면 true
- Reflect.ownKeys(o) : return array of names of properties of the object o
- Reflect.preventExtensions(o) : sets extensible attribute of object o to false
- Reflect.set(o, name, value, receiver) : o안에 있는 name property를 value로 set한다. receiver이 전달되었다면 o의 method대신 receiver이 invoked된다.
- Reflect.setPrototypeOf(o, p) : sets prototype of the object o to p  
## 🦑 Proxy Objects  
allow us to write code that alters the fundamental behavior of JavaScript object.  
Proxy class allows us a way to inplement those fundamental operations our-selves and create object that behave in ways that are not possible for ordinary objects.  
Proxy object를 생성할때는 target object와 handler object가 필요하다.  
```
let proxy = new Proxy(target,handlers);
```
Proxy 객체에서 지원되는 것은 Reflect API에서 정의되는 작업과 같다. 예를 들어 delete p.x는 Reflect.deleteProperty()과 동일하다.  
Proxy에서 접근하는 것과 target object에서 접근하는 것이 같으므로 proxy 대신 target을 쓸 이유가 없다. 그러나 Transparent wrapper은 “revocable proxies“를 만들때 유용하다. 이 function은 Proxy object와 함께 revoke() 을 반환한다. 제어하지 않는 라이브러리를 전달해야 할때 revocable proxy를 전달하고 라이브러리를 다 썼을때 Proxy를 해지한다. 이와 같이 하면 라이브러리에서 사용자 기능에 대한 참조를 유지하고 예기치않은 시간에 호출되는 것을 막을 수 있다.  
Proxy를 작성하는 또 다른 방법은 작업은 여전히 target object에 위임하지만 object의 operation을 intercept할 수 있는 handler object를 정의하는 것이다.  
<생각할 시간을 주시면 고민 후 답변을 들고 오겠읍니다 🦜 >  
Q1. Array[Symbol.species] = Array; => 이 경우 발생하는 과정 다시 보기 p390  
Q2. function vs method  
A2. method는 객체 o안에 정의된 function이다.  
Q3. Proxy부분 나중에 재정리… 14.7.1  
PS1. 14.5(Template Tags)는 태그를 이용해 CSS스타일을 JS에 포함하는 부분이기 때문에 적지 않았습니다.  
