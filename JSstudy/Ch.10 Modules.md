# Modules  
***  
서론  
***  
모듈을 클래스로 구성하는 가장 중요한 이유는 커다란 하나의 코드를 모듈화하여 여러상황에서 사용하기 좋기 때문이다. 모듈의 문제점은 모듈이 되면 캡슐화, 그리고 private인 필드를 숨기고 global namespace를 못쓰기 때문에 변수나 함수 등에 대한 modified가 불가능하다.  
현재까지도 Js는 built-in 된 모듈이 없다. 클로저 베이스 모듈화는 require()에 기반한 pratical form을 제공했다. Node programming에 적용되지만 공식화되진 않았다. 대신 ES6에서는 import와 export keyword를 통해 모듈을 정의한다.  
***  
본론  
***  
## 🦑 Modules with Classes, Objects, and Closures  
가장 중요한 특징 중 하나는 classes가 그들의 methods에 대해서 모듈처럼 작동한다는 것이다.  
classes가 모듈과 같은 이유는 객체가 modular하기 때문이다. object내에 property를 선언하는 것은 변수를 선언하는 것과 같지만 새로 추가되는 property는 다른 property에 영향을 주거나 global namespace에 영향을 주지 않는다.  
모듈화를 위해 class를 쓰거나 object를 쓰는것은 Js에서 충분하진 않다. 예를 들면 이러한 방법은 내부의 정보를 숨기지 않는다.  
nested function의 경우, inner function의 경우 정보를 숨길 수 있기 때문에 모듈화할 수 있다. function 안에는 return value를 써야한다.  
```javascript
const stats = (function(){
  const sum = (x,y) => x+y;
  const square = x=>x*x;
  
  function mean(data){return data.reduce(sum)/data.length;}
  
  function stddev(data){
    let m = mean(data)
    return Math.sqrt(data.map(x=>x=m).map(square).reduce(sum)/(data.length-1));
  }
  return {mean,stddev}; // export public function
}())

stats.mean([1,3,5,7,9]) // => 5
stats.stddev([1,3,5,7,9]) // => Math.sqrt(10)
```
**10.1.1 Automating Closure-Based Modularity**  
javascript 파일에 무엇을 export할 것인지 어떤걸 export하지 않을 것인지 text를 씀으로써 module로 쓸 수 있다.  
```
const modules = {};
function require(moduleName){return modules[modulesName];}

modules["sets.js"] = (function(){
  const exports = {};
  exports.Bitset = class Bitset {...}
  return exports;
}())

modules["stats.js"] = (function(){
  const exports = {}
  
  const sum = (x,y) => x+y
  const square = (x) => x*x
  exports.mean = function(data){...}
  exports.stddev = function(data){...}
  
  return exports;
}())
const stats = require("stats.js")
const Bigset = require("sets.js").Bitset;

let s=new Bigset(100)
s.insert(10)
s.insert(20)
s.insert(30)
let average = stats.mean([...s]) // 20
``` 
## 🦑 Modules in Node  
자바스크립트 파일로 만들면 web browser에서 네트워크를 타고 가져오는것보다 훨씬 빠르다.  
Node에서 각 파일은 각기 다른 모듈을 가지고 있다. 그리고 그 파일에서 export하기전까지 모든 object, values 등은 private하다.  
Node module은 require을 통해 다른 모듈에서 import하고 export property를 통해 export한다.  
**10.2.1 Node exports**  
Node는 항상 export를 global하게 정의한다.  
그러므로 다음과 같이 쓸 수 있다.  
```
exports.mean = data =>data.reduce(sum)/data.length
```
또한 모든 object를 가져오는게 아니라 필요한 object만 가져오고 싶을때는 다음과 같이 .value를 써준다.  
```
modules.exports = class Bitset extends AbstractWritableSet
```
이때 modules.exports의 deflault value는 export와 같다.  
또한 export를 하나씩 하는게 아니라 {}로 묶어서 한번에 export하는 것이 가능하다.  
```
module.exports = {mean,stddev};
```
**10.2.2 Node Imports**  
Node module은 다른 모듈을 require을 통해 import한다.   
만약 Node모듈을 이용하고 싶거나 외부에서 다운받은 모듈을 사용하고 싶다면 모듈의 이름을 쓰면 된다.  
절대경로로 쓴다면 ./ 나 ../를 붙여 그 모듈이 현재 파일이나 부모 파일과 연관이 있음을 명시한다.  
(.js는 생략해도 된다.)  
만약 function하나나 class라면 그저 require을 쓰면 되지만 object는 쓴다면 그 object를 모두 import할건지 아니면 필요한 부분만 import할 건지 선택해야 한다.  
```
const stats = require('./stats.js')
const {stddev} = require('./stats.js')
```
## 🦑 Modules in ES6  
ES6은 import와 export keyword를 추가했다.  
ES6 모듈은 개념적으로 Node 모듈과 같다. export 된 모듈은 다른 모듈에서 import해서 쓸 수 있다.  
ES6모듈은 Node모듈과 web browser에서 importing. exporting 되는 방법이 다르다.  
먼저, ES6 모듈은 보통의 javaScript 와 다르다. 먼저 모듈은 각 파일이 private context를 가지고 있으며 import, export statement를 쓸 수 있다. 또한 ES6의 모듈은 strict mode이다.그러므로 with statement나 argument object, undeclared variable을 쓸 수 없다.심지어 top level의 코드에서 this는 undefined가 된다.  
**10.3.1 ES6 Exports**  
ES6에서 export하기 위해서는 declaration전에 export를 써주면 된다.  
```
export const PI = Math.PI;
export function degreesToRadians(d){return d * PI /180;}
export class Circle{
  constructor(r){this.r = r}
  area(){return PI*this.r*this.r;}
}
```
혹은 각각에 export를 붙이지 않고  
```
export {Circle,degreesToRadians,PI}
```
다음과 같이 one line으로 줄일 수 있다.  
export를 한 value만 하는 경우도 있다. 이경우, export 대신 export default를 쓴다.  
또한 export default는 export보다 import하기 쉽다.  
마지막으로 export는 top-level에서만 선언될 수 있다. 예를 들어 function안, object 안, loop 에서는 defined할 수 없다.  
**10.3.2 ES6 Imports**  
import keyword를 통해 다른 모듈에서 values를 import할 수 있다.  
```
import Bitset from './bitset.js'
```
(1)위와 같이 import할 경우 모듈의 default export values를 가져온다.  
identifier은 코드에서 const등으로 declared되어있어야 한다.  
import는 hoisted되기 때문에 항상 위에 위치하게 된다.  
어느 모듈에서 가져왔는지는 ““ 나 '' 로 기입된다. web browser에서는 이와 같은 string이 url로 연결되기 때문이다.  
module specifier string은 절대경로나 상대경로, 혹은 url과 protocal, hostname이여야 한다.  
위의 코드는 export default values를 import한 것이다. 만약 (2)multiple values를 export하는 모듈에서 가져올때는 그 모듈의 이름으로 가져와야 한다.  
```
import {mean , stddev} from "./stats.js"
```
(3)만약 많은 export를 import하고 싶다면 그저 * 을 써주면 된다.  
```
import * as stats from "./stats.js"
```
다음과 같이 쓰면 모든 object등이 stats에 assign된다.  
예를 들어 imported 된것 중 mean()을 쓰고 싶다면 stats.mean() 다음과 같이 쓸 수 있다.  
간혹 (4)export와 export default를 같이 쓰는 경우가 있다.  
```
import Histogram,{mean,stddev} from "./histogram-stats.js"
```
다음과 같이 export default와 export를 받는다.  
(5)모듈안에 export가 없을 경우 아래와 같이 import만 쓴다.  
```
import "./analytics.js"
```
**10.3.3 Imports and Exports with Renaming**  
만약 두개의 모듈에서 이름이 같은 value를 가져온다면 두 values의 이름을 바꿔 서로 다른 이름으로 사용해야 한다.  
또한 두 모듈에서 가져오지 않더라도 이미 코드내에 해당 이름을 쓰는 변수가 있다면 가져오는 value의 이름을 바꿔줘야 한다.  
```
import {render as renderImage} from "./imageutils.js";
import {render as renderUI} from "./ui.js"
```
defalut export의 경우 이름없이 가져오므로 이름을 바꿔줄 필요가 없다. 그러나 default export와 export를 같이 가져올 경우 이름을 지어야 한다.  
```
import {default as Histogram, mean, stddev} from "./histogram-stats.js"
```
위와 같이 쓰면 default value에 이름을 줄 수 있다.  
export에서 renaming을 할 수도 있다. 다만 curly brace안에서만 가능하다.  
```
export {
  layout as calculateLayout,
  render as renderLayout
}
```
**10.3.4 Re-Export**  
stats라는 모듈에 mean()과 stddev()가 있다고 하자. 그리고 이 모듈을 많은 사람들이 쓰고 필요한 기능만을 쓰고 싶다고 할때 두 함수를 각각 다른 파일로 저장해서 필요한 함수만 쓰고 싶을 것이다.  
그러나 반대로 두 함수를 모두 쓰고 싶은 경우도 있을 것이다.  
원래는 다음과 같이 각 파일에서 함수를 import한뒤 두 함수를 curly brace로 export했다.  
그러나 ES6부터 export와 from으로 re-export할 수 있게 되었다.  
```
import {mean} from "./stats/mean.js"
import {stddev} from "./stats/stddev.js"
export {mean,stddev}
export {mean} from "./stats/mean.js"
export {stddev} from "./stats/stddev.js"
```
import와 마찬가지로 모듈 내 모든 object를 가져오고 싶을땐 *  
default의 이름을 바꾸고 싶을땐 default as mean를 쓴다.  
반대로 default export를 바꾸고 싶을때는 다음과 같이 쓴다  
```
export {mean as default} from "./stats.js"
```
**10.3.5 JavaScript Modules on the Web**  
이번 장에선 실제로 web browser에서 어떻게 동작하는지 알아본다.  
ES6 모듈을 사용하는 프로덕션 코드는 webpack과 같은 툴과 함께 제공된다. 이는 좀더 나은 성능을 제공한다. 좀더 나은 네트워크 속도와 브라우저의 증가는 ES6모듈을 더 최적화할것이다.  
번들링 도구는 브라우저가 자바스크립트 모듈의 기본적인 지원을 하므로 더이상 쓰이지 않는다. 모듈은 반드시 이전의 비모듈코드와 다르게 작동해야 하므로 javascript뿐만 아니라 html코드도 바뀌어야 한다. import를 쓰려면 web browser에 <script = “module“> 태그를 이용해 코드가 모듈이라는 것을 알려야한다.  
ES6 모듈의 가장 큰 특징은 각 모듈이 static set of import를 가지고 있다는 것이다.따라서 하나의 모듈이 시작되면 웹브라우저는 모든 모듈을 import한다. 가져오는 모듈에는 <script>태그가 없지만 정규 javaScript file과 strict mode로 실행된다.  
```
<script type = “module“> import “./main.js“;</script>
```
***  
<임시question입니다, 생각할 시간을 주시면 답변(A)를 가지고 돌아오겠습니다🦜 >
***  
Q1. export default는 export보다 import하기 쉽다. => 이거 이유 다시 읽어보기  
Q2. 10.3.5 부터는 html에 가까운거같은데…. ch.15 읽고 다시 돌아오기  
