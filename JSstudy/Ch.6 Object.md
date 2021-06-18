# Object  
***
서론
***
composite value이다. values와 objects 들로 구성되어 있다.  
unordered properties  
string-value의 조합. 비슷한 예시로는 hash, hash table, dictionary 등이 있다.  
항상 prototype라는 object를 상속받는다. => 이 속성이 js의 key 특성!  
dynamic하기 때문에 요소가 추가되거나 삭제될 수 있다.  
모든 value는 object이다. 심지어는 number이나 string까지도…  
value들이 immutable하다. immutable하기 때문에 만약 let x = y라고 할 경우, reference를 참조하게 된다. => y의 값을 바꾸면 x의 값도 바뀐다(같은 주소를 가리키고 있으니까~)  
Object가 하는 일은 자기자신을 create하기 , value들을 set, query, delete, test, enumerate 하기  
property는 value이거나 setter, getter이다.  
prototype object에서 상속되지 않은 property를 own property라고 부른다.  
Js에서 built-in 된 object의 properties는 readonly이며 직접 만든 propertues는 writeable하다.  
***
본론 
***
🦑 Create Object  
let o = new Object() //이런식으로 쓰면 Object.create() 함수가 호출된다.  
literal하게는 
```
let o = {“main title“:”JS”, “sub title“:{“isJava“: false, subname: ”ES”} }  
```
콤마로 연결한다, Object 안에 Object 가능  
기본 데이터형도 new 연산자를 이용하여 명시적으로 객체 생성이 가능하나 원칙적으로 금지  
### 🦑 Prototype  
모든 Object의 부모는 prototype object이다.  
new() 로 새로운 object를 만들면 prototype를 상속받는다.  
object는 object.prototype, array는 array.prototype 이런식.  
Object.prototype는 유일하게 부모가 없는 object이다.  
**Object.prototype**  
built-in constructor를 가지고 있는데 다른 (Date.prototype)같은 prototype이 여기서 built-in constructor를 가져온다.  
즉, new Date() => Date.prototype() => Object.prototype() 이런식으로 chaining  
Object.create를 하면 prototype을 상속받는데 여기서 상속받은 constructor로 기본 연산이 가능하다. (뭐 toString()이라던지 +,- 라던지)  
Object.creat(null)을 하면 prototype를 상속받지않기 때문에 기본적인 연산이 불가하다. => Object.creat(Object.prototype) 이런식으로 써야한다. (== {}, new Object())  
### 🦑 To obtain value of property  
dot 이나 []를 통해 접근가능.  
```javascript
let author = book.author  
let author = book[“author“]  
```
array를 통한 접근도 가능  
dot으로 접근  
Js는 약한 검사를 하기 때문에 property의 수가 정해져있지 않고 미리 정의되지 않아도 된다.  
 identifier은 literal 해야한다.  
[]으로 접근
```javascript
for(let i =0; i<4; i++){  
 o = book['addition${i}']  
}  
```
다음과 같이 literal로 바꿔서 접근한다. Ch.5 처럼 for~ in 도 가능!  
### 🦑 Inheritance  
prototype ← o{x} ← p{y} ← q{z}  
q.x를 불러오면 q에서 있는지 확인 → p에서 있는지 확인 → o에서 있는지 확인 → 있으며 가져오기  
없었다면 ~ prototype … null이 나올때까지 찾는다.  
```javascript
let unit = {r:1}  
let c = Object.create(unit) // c가 unit을 상속  
c.r = 2  
unit.r // → 1
```
상속될 뿐이지 original의 값은 변하지 않는다. => 상속되는 값이 readonly라는 것을 알 수 있다. (물론 나중에 setter을 이용해 바꿀 수는 있다.)  
### 🦑 Access Error  
부모 object나 자신 object에 x라는 property가 없다면 undefined를 return 한다.
Ch.5에서 언급했듯 undefined는 TypeError를 발생시킨다.
### 🦑 Deleting Properties
delete book.author // value를 삭제하는게 아니라 property자체를 삭제한다.  
다만, inherited 된건 삭제 불가. 삭제하려면 처음에 명시된 object에서 삭제해야 한다.  
### 🦑Testing Properties  
Object는 set of properties  
set 안에 object가 정해진 이름을 가진 property 를 가졌는지 test하면 유용할 것이다.  
in operator, hasOenProperty(), propertyIsEnumerable(), query 을 이용하면 확인할 수 있다.  
  
**1) in**  
let o = {x:1}  
“x“ in o //true 상속된 property도 가능  
(name) in (object)  
**2)hasOwnProperty()**  
o.hasOwnProperty(“x“) //true 상속된 property불가  
**3)propertyIsEnumerable()**  
Object.prototype.propertyIsEnumerable(“toString“) // false =>not enumable  
**4)!==**  
o.x !== undefined //이런식으로 쓸 수도 있다.  
### 🦑 Enumerating properties  
object 안의 모든 properties보기.  
o.propertyIsEnumerable(“toString“) 이런식으로는 볼 수 없다.  
loop 써야한다.  
### 🦑 Extending Object  
object ← object 로 copy할때  
Js에서는 Object.assign()을 사용한다. Object.assign()을 하면 get, set, operator을 이용한다.  
Object.assign(o,defalut) //properties들이 모두 defalut가 된다.(overwrite 기존의 것을 없앤다.)  
```
o = Object.assign({},defalut,o) //기존의 defalult를 대체한다.(override)  
Object.assign({x:1},{x:2,y:3}) //{x:2,y:3} 덮어씌기가 된다.  
```
### 🦑 Serializing Object  
어떠한 type → String  
JSON.stringfy(), JSON.parse() serialize, restore Object  
{x : 1, y : {z : [flase, null, ” ”]}} → Stringfy → {“x “: 1, “y” : {“z” : [flase, null, ” ”]}} → parse → {x : 1, y : {z : [flase, null, ” ”]}}  
NaN, Infinity, - Infinity 는 null로 seriallzed된다.  
Stringfy는 enumerable, own properties만 serialized한다.  
### 🦑 Object method  
**toSting**  
o.toSting() → 이건 object라는것만 알려준다.  
제대로 쓸려면 o{toSting : () => {return ${this.x}}} 이런식으로 object안에 미리 정의해놔야 한다.  
**toLocalString**  
default value는 toSting과 같다.  
Date와 number이 toLocalString을 쓸 수 있다.  
마찬가지로 () => return ${this.x.toLocalString()}  
**valueof()**  
object를 primitive type로 바꿀때(String보다)  
예를들어 Date type를 number로 바꾼다던지.  
**toJSON()**  
 value를 serialized하게 return 한다.  
### 🦑 Extended Object Literal Syntax  
ES6이 되면서 Js는 syntax를 확장했다.  
x=1 y=2 일때  
 이전 : o = {x:x, y:y}  
ES6 : o = {x, y}  
property의 이름이 Literal하면 상수시간에 접근이 불가하다.  
ES6 : propertyName = “p1“ computeName = {return “p“+2}  
let p = {[propertyName] = 1 , [computeName ] = 2}  
p.p1 + p.p2 //이런식으로 접근이 가능해졌다.  
Symbol을 property Name으로 쓸 수 있다.  
 symbol은 property name이외로는 쓸 수 없다. => unique한 property name으로 쓸 수 있다.  
not security하지만 safe extension mechanism이다.  
**Spread Operator**  
object를 … 이라는 spread operator를 이용해 copy할 수 있다.  
 ex) position {x:2,y:3} dimension {w:100,h:75}  
rect:{…position,…dimention}  
 접근도 rect.x 이런식으로 한다.  
만약 이미 있는 property면 덮어씌가된다. 상속된 object 요소는 접근불가.  
**Shortand method**  
area:function(){} => area(){}  
**getter, setter**
```javascript
get accessorProp(){return this.p}  
set accessorProp(value){this.p = value}
```
  
    
### 생각해볼 문제 
Q1.dot으로 접근에서 Js가 약한 검사를 한다는 것과 array를 통해서 접근 가능하다는 것은 어떤 관계가 있는걸까?  
Q2.Extending Object에서 Object.assign(o,defalut)를 할 경우 overwrite가 되고 o = Object.assign({},defalut,o) 이 경우, override가 된다고 쓰여있는데, overwrite와 override의 정확한 차이를 모르겠습니다.  
  
  
🐹   
Object = Property + Method
