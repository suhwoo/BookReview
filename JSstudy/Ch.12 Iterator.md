# Iterator  
***  
서론  
***  
iterable한 객체는 for/of loop를 통해 iterated될 수 있다.  
Iterator은 …operator, destructuring assignment로 쓰일 수 있다.  
Map object에서 iterator을 쓸때 [key,value] pair이라면 for/of loop를 쓰고 key, 혹은 value만 가지고 오고 싶다면 keys(), values()를 쓴다.  
Array object에서 쓰이는 몇몇 constructors과 built-in functions는 written to accept arbitrary iterators  
***  
본론  
***  
## 🦑 How Iterators work  
java script iteration 이해를 위한 세가지 : 1. iterable object, 2.iterator it self 3. iteration result  
iterable object : object with special iterator method that returns an iterator object.  
iterator : object with next() method  
iteration result : object with properties named value and done  
iterable object → iterator method → next() → done property set true  
```javascript
let iterable = [99]
literator = iterable[Symbol.iterator]();
for(let result = iterator.next(); ! result.done;result = iterator.next()){
  console.log(result.value);
}
```
## 🦑 Implementing Iterable Object
Range를 통해 generator을 이용하지 않고 iterable하게 만들어 보자.  
class를 iterable하게 만드려면 Symbol.iterator methos를 구현해야 한다. which return iterator object.  
```
class Range {
  constrictor(from,to){
    this.from = from
    this.to = to
  }
  has(x){return typeof x === "number" && this.from <= x && x<= this.to}
  toString(){return '{x|${this.from} < x < ${this.to}}'}
  [Symbol.iterator](){
    let next = Math.ceil(this.from)
    let last = this.to
    return {
      next(){return (next <= last? {value:next++} : {done:true};},
      [Symbol.iterator](){return this;}
    };
  }

}

for(let x of newRange(1,10)) //23456789
```
when computation is requires to compute the next value, that computation can be deferred until the value is actually needed.  
**12.2.1 “Closing“ an Iterator : The return value**  
문자열이 아닌 파일에서 문자를 읽는 프로그램을 생각할때, 파일 읽기가 끝나면(next가 마지막 문자를 갖다주면) 파일을 닫아야 한다.  
그러나 iterator이 항상 끝까지 돌지는 않는다 (break나 return에 의해 중단될 수 있다.)  
그러나 끝까지 돌지 않을때도 loop에서 빠져나왔다면 파일을 닫아야 한다. 그러므로 iterator은 next()와 함께 return()을 제공한다.만약 next()가 중단되면 iterator은 return()이 있는지 확인하고 파일을 닫고 메모리를 해제한다.return()은 iterator reult object를 반환한다.  
## 🦑 Generators  
generator은 element of data structure들이 아닐때 유용하다.  
generator을 만들려면 일단 generator function을 정의해야 한다. 이 function은 * function keyword를 사용함으로써 만들어질 수 있다. return으로 generator object를 반환하는데 이 object가 iterator이다. next() method causes generator function to run until reached yield  
```
function* oneDigit(){
  yield 2;
  yield 3;
  yield 5;
}
let prime = oneDigit();
prime.next().value // 2
prime.next().value //3
prime.next().value // 5
prime.next().done //true
```
혹은 …를 써서 value를 출력하거나 function을 생략할 경우 * g() 와 같이 쓸 수 도 있다.  
**12.3.2 yield* and Recursive Generator**  
yield를 통해 value를 하나씩 전달하는게 아니라 아예 여러개의 element를 iterable한 object로 넘겨주는 것이 더 쉬울것이다. => ES6에서 yield* 를 통해 구현될 수 있다.  
```
function* sequence(...iterables){
  for(let iterable of iterables){
    yield* iterable;
  }
}
[...sequence("abc",oneDigit())]//=>["a","b","c",2,3,5]
```
## 🦑 Advanced Generator Feature  
generator의 기본 속성은 계산을 일시중지하고 중간결과를 산출한뒤 다시 계산을 재개할 수 있다.  
**12.4.1 The return Value of a Generator Function**  
지금까지 봐온 generator 함수는 return value가 없다. 그러나 다른 함수들 처럼 제너레이터도 리턴값을 반환할 수 있다. 만약 value property가 정의되었다면 done은 false나 undefined이다. 그러나 리턴값을 반환하는 generator의 경우, 마지막으로 부른 next는 done, value가 둘다 defined된 값을 반환한다.  
**12.4.2 The value of a yield Expression**  
앞에서 yield를 statement로 다루고 value를 가지지 않았다고 다루었지만 사실 yield는 expression이고 value를 가지고 있다. yield 에서 value는 next의 리턴값이 되는데, 여기서 generator stop executing right in the middle of evaluating the yield expression. 다음부터 next()로 전달되는 값은 value가 아닌 yield가 된다. generator return caller with yield, and caller pass value in to generator with next  
**12.4.3 The return() and throw() methods of Generator**  
next()로 input을 제공할 뿐만 아니라 return()과 throw를 통해 generator 내부의 흐름을 변경 할 수 있다.  
앞에서 언급했던것 처럼 return을 custom해 파일을 정리하는건 불가능하다. 그러나 try/finally 를 이용해 필요한 정리가 다 되었는지 확인하는 것이 가능하다.  
next()가 generator로 value를 전달하듯이 throw()는 arbitrary signal을 generator에게 보낸다. throw를 보내는 것은 generator 내부에서 exception을 발생시킨다. 만약 generator 내부에서 이를 handling 하는 코드가 작성되어 있을 경우, 문제를 handling할 수 있다.  
All Iterator must have next()  
Iterator that need to clean up should define return()  
Any Iterator may define throw()   
