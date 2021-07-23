# Asynchronous Programming
대부분의 프로그램이 실제현실을 반영하기 위해서는 비동기식 처리가 필요하다 => 결과가 나오기까지 계산을 pause하던지 혹은 어떤 이벤트가 발생되기까지 기다리던지    
이 챕터에서는 세가지 용어를 소개한다.    
1.Promise    
2.async await    
3.for/await    
## 🦑 Asynchronous Programming with Callbacks  
asynchronous programming in JavaScript is done with callbacks  
callback is a function that you write and then pass to some other function and then that function invoke your function when event occurs.  
**13.1.1 Timers**  
가장 간단한 비동기는 몇초후 실행되는 코드이다. 이러한 코든 setTimeout()을 통해 구현될 수 있다.  
```
setTimeout(checkForUpdate,60000);
```
여기서 checkForUpdates는 60초가 지난뒤(조건만족) 불리는 callback 함수이다.  
setTimeout()은 callback함수를 딱 한번만 부르는 함수이다.(with no argument)  
그러므로 만약 실제로 update를 확인해보려면 반복적으로 실행되어야 할 것이다.  
setInterval()을 통해 여러번 반복할 수 있다.  
**13.1.2 Events**  
Client-side JavaScript는 유저의 동작을 기다리고 동작에 따라 프로그램이 실행되기 때문에 Event-driven속성을 가지고 있다. 예를 들어 클릭을 기다리거나 키보드 input을 기다리는 등의 동작이 있다.  
addEventListener()을 통해 이벤트를 듣고 function을 invoke한다.  
```
let okay = document.querySelector('#confirmUpdateDialog button.okay')
okay.addEventListener('click',applyUpdate);
```
**13.1.3 Network Events**  
다른 비동기식 JS programming은 network request이다.  
```
function getCurrentVersionNumber(versionCallback){
  let request = new XMLHttpRequest();
  request.open("GET","http://www.ezample.com/api/version");
  request.send();
  request.onload = function(){
    if (request.status === 200){
      let currentVersion = parseFloat(request.responseText);
      versionCallback(null,currentVersion);
    }
    else{
      versionCallback(response.statusText,null)
    }
  };
  request.onerror = request.ontimeout = function(e){
    versionCallback(e.type,null)
  }
}
```
위의 코드는 server’s respones가 올때까지 기다리다가 request.onload에서 respones를 받으면 callback이 invoke된다.(JS는 비동기식이므로 callback으로 respones를 받은 다음 function을 invoke한다 라는 순서를 준다) 만약 server로 부터 error를 받는다면 request.onerror가 invoke되어 error타입과 null을 callback해준다.  
**13.1.4 Callbacks and Events in Node**  
server-side 의 JS코드는 비동기식이다. API는 event와 callback function을 쓴다.  
```
const fs = require("fs")
let options = {
  //default options
}
fs.readFile("config.json","utf-8",(err,text)=>{
  if(err){
    console.warn("Could not read config file:",err)
  }else{
    Object.assign(options,JSON,parse(text));
  }
  startProgram(options);
})
```
파일을 읽고 callback함수를 부른다. 만약 file에 error가 날시 callback에 err가 전달된다  
## 🦑 Promises  
A Promise is an object that represents the results of an asynchronous computation.  
you can only ask the Promise to call a callback function when value is ready  
Promise는 callback에서 문제가 되었던 부분을 해결해준다.  
첫번째 문제는 callback이 여러개가 되면 nested가 되어 읽기가 힘들어 진다 (콜백지옥) Promise는 Promise chain을 통해 이러한 문제를 해결하여 가독성을 높인다.  
두번째 문제는 callback으로 error handling이 어렵다는 것이다. Promise는 Promise chaining을 이용해 error handling을 한다.  
Promise는 반복적인 연산은 할 수 없다. 대신 single 연산이 가능하기 때문에 setTimeout과 XMLHttpRequest에서는 load를 대체해볼 것이다.  
**13.2.1 Using Promise**  
Promise-returning utility function:  
```
getJSON(url).then(jsonData => {
});
```
getJSON 내부에서 callback을 호출하는게 아니라 then으로 넘겨준다. 만약 promise object에 여러번 then을 호출한다면 각각의 function은 promise computation이 끝날때 호출된다.  
function you pass to then() is asynchronously.  
Handling errors with Promise  
=>비동기에서 error를 handling하기 위해서 rebust code가 써져있어야 한다.  
Promise에서 then에 두번째 function을 전달함으로써 error handling을 할 수 있다.  
```
getJSON("/api/user/profile").then(displayUserProfile,handleProfileError)
```
기존 computation이 Promise object가 return 된 다음 수행되기 때문에 computation에서 value를 return 하던가 혹은error를 throw하고 catch할수 없다. Promise-based asynchronous computation에서는 exception을 두번째 function으로 전달한다. 그러나 만약 getJSON이 아닌 displayUserProfile에서 에러가 날때 handling할 수 없다. 이를 더 쉽게 해결할 수 있는 방법이 아래와 같이 catch를 쓰는 것이다.  
```
getJSON("/api/user/profile").then(displayUserProfile).catch(handleProfileError)
```
catch는 getJSON의 exception과 deisplayUserProfile의 exception 모두 passed된다.   
**13.2.2 Chaining Promises**  
callback처럼 nested할 필요없이 chaining을 해서 쓸 수 있다.  
```
fetch(docimentURL)
  .then(response => response.json())
  .then(document => {return render(document);})
  .then(rendered => {cacheInDatabase(rendered);})
  .catch(error => handle(error)) 
```
위와 같이 XMLHttpRequest를 쓸 수 있다. fetch()를 통해 URL을 전달하고 Promise를 반환한다. 심지어 fetch를 통해 text()와 json() method를 쓸 수 있다. 그러나 fetch()가 fulfilled되어도 response의 body가 아직 도착하지 않아서 쓸 수 없을 수 있는데 이를 위해 response.json()을 씀으로써 해결할 수 있다.  
```
fetch("/api/user/profile")
  .then(response => {return response.json();})
  .then(profile => {displayUserProfile(profile);});
```
다음과 같이 .then().then()으로 method가 두개 이상 있는 것을 method chain이라고 부른다. 각각의 then()은 새로운 Promise object를 return한다.then()이 완료되기 전까지 new Promise object는 fulfilled되지 않는다.  
fetch -> theurl -> http status,header → fulfill promise1 → callback1 → task2 begin → promise 2 …  
**13.2.3 Resolving Promise**  
위의 코드에서 promise들을 반환했는데 마지막으로 promise가 한번더 반환된다. 그리고 이것은 promise가 resloved되어야 한다는 것을 의미한다.  
c: callback, p:promise, v:value  
c → then → p → c → v → p  
when promise call callback, callback function이 계산을 하고 v를 반환한다.  
이 v가 non-promise라면 p is fulfilled  
v가 promise라면 p is resolved not yet fulfilled → can not settle until v settle  
v fulfilled → p fulfilled  
v rejected → p rejected  
so, resolved means, promise associated with another promise  
**13.2.4 More on Promises and Errors**  
앞으로 then과 catch를 쓰면서 error handling에 주의를 기울여야 한다.  
The catch and finally methods  
p.then(null, c )와 p.catch(c) 는 동일하다. 그러나 catch가 더 선호되는데 try catch와 비슷하고 비동기가 가능하기 때문이다. 무언가 문제가 발생했을때 catch까지 도달하기 위해 chain을 타고 내려가는 것을 trickling down the chain이라고 한다.  
finally()는 try catch finally 구문과 비슷한 역할을 한다.Promise가 fulfilled되거나 reject되거나 callback은 그러한 정보를 보여주지 않는다. 정보는 알수없지만 clean up을 하고 싶을때(열었던 파일을 닫는다던가) .finally()를 쓸 수 있다.  
If finally() callback throws an exception, promise return by finally() will reject with that value.  
만약 .catch()를 chain의 마지막에만 넣는다면 recovery가 가능한 error도 대응하지 못하고 그대로 종료될 것이다. (예를 들어 url에서 페이지를 불러오지 못했을때 recovery될 수 있음에도 그대로 프로그램이 종료될 것이다.) 그러므로 만약 다른 chain이 그대로 실행되어야 한다면 .catch()를 chain 중간에도 추가하는 것이 좋은 방법이 된다.  
```
startAsyncOperation()
  .then(doStageTwo)
  .catch(recoverFromStageTwoError)
  .then(doStageThree)
  .then(doStageFour)
  .catch(logStageThreeAndFourErrors)
```
만약 doStageTwo가 제대로 실행된다면 중간의 catch는 skip될 것이다.  
만약 doStageTwo에서 문제가 발생했다면 recoverFromStageTwoError 에서 error를 handling하고 그 결과를 doStageTree로 넘긴다.  
**13.2.5 Promises in Parallel**  
여러 비동기 작업을 병렬로 실행하려 할때 Promise.all()을 사용한다. input은 array of Promise object, output은 promise이다. 만약 하나의 promise라도 reject되면 return되는 promise는 reject된다.  
array element는 non-promise일 수도 있는데 이때는 이미 fulfilled promise로 여겨진다.  
Unlike Promise.all(), Promise.allsettled() never rejects the returned Promise, and it does not fulfill that Promise until all of the input Promise settled  
**13.2.6 Making Promise**  
Promise relied on hypothetical Promise-returning functions getJSON() and wait()  
-Promise based on other Promise :  
이미 만들어진 Promise에 then을 붙이면 새로운 Promise를 만들 수 있다.  
예를 들어 이미 fetch()라는 Promise가 있다면 다음과 같이 새로운 Promise를 만들 수 있다.  
```
function getJSON(url){
  return fetch(url).then(response => response.json());
}
```
(사실 저 코드는 trivial한데, 왜냐면 이미 fetch에 json() method가 이미 들어있기 때문이다.)  
-Promise based on synchronous values:  
간혹 그 동작이 필요하지 않더라도 Promise-based API를 통해 Promise를 생성해야 한다.  
보통은 Promise.resolve()와 Promise.reject()가 그 역할을 한다.  
Promise.resolve() takes value as single argument and returns a Promise that will immediately be fulfilled to that value.  
Promise.reject() takes single argument and returns a Promise that will be rejected with that value as the reason.  
It’s fairly common to have synchronous special cases within asynchronous function, and you can handle these special cases with Promise.resolve() and Promise.reject()  
for example, error condition can be handle by returning a Promise created with Promise.reject()  
-Promise from scratch:  
case : can’t use Promise-returning function as start-point  
=>use the Promise() constructor to create new Promise object that you have complete control over.  
Promise() ← function as only argument, function(reslove,reject) {return new Promise}, does not have to be asynchronous  
```
function wait(duration){
  return new Promise((resolve,reject) => {
    if(duration < 0){
      reject(new Error("Time travel not yet implemented"));
    }
    setTimeout(resolve,duration);
  });
 }
```
**13.2.7 Promises in Sequence**  
Promise.all()을 이용하면 병렬적으로 수행할 수 있지만 순차적으로 실행하는 것은 어렵다.  
예를 들어 array에 url들을 넣고 모두 fetch()할려 할때, 길이가 arbitrary하고 unknown context이기 때문에 미리 Promise chain을 쓸 수 없다. 그러므로 dynamically하게 만들어야한다.  
Promise를 미리 만들고 그 뒤에 chain을 붙인다.  
또다른 접근법으로는 Promise를 미리 만들고 뒤로 chain을 붙이는 게 아닌 callback for each Promise.  
create and return the next Promise like matryoshka  
code can return first Promise.  
## 🦑async and wait  
Promise-based, asynchronous code를 synchronous하게 보이게 한다.  
Hide the Promise as, easy to read  
**13.3.1 await Expressions**  
takes Promise return value or thrown exception  
await promise 는 주어진 promise가 올때까지 wait한다.  
주어진 promise는 fulfilled value나 rejection value를 return한다.  
그러나 await이 code를 synchronous하게 block하거나 하진 않는다.  
**13.3.2 async Functions**  
await keyword는 aync kayword로 declared된 function에만 쓸 수 있다.  
```
async function getHighScore(){
  let response = await fetch("/api/user/profile");
  let profile = await ewsponse.json();
  return profile.highScore;
}
```
async을 선언하는 것은 심지어 code가 Promise related code가 아니더라도 Promise를 반환해준다. 만약 return value가 있다면 function은 value를 resolve해줄것이고 exception이 throw된다면 return값은 reject될것이다.  
getHighScore function은 async keyward를 썼기때문에 await을 통해 가져올 수 있다. (return 값이 Promise)  
displayHighScore(await getHighScore());  
**13.3.3 Awaiting Multiple Promise**  
만약 url1과 url2를 동시에 fetch()하려 할때  
```
async function getJSON(url){
  let response = await fetch(url)
  let body = await response.json();
  return body
}

let value1 = await getJSON(url1);
let value2 = await getJSON(url2);
```
로 코드를 쓰면 value2는 url1의 fetch가 끝날때까지 가져오지 못 한다.  
두 url을 동시에 가져오기 위해서는 Promise.all()을 써야한다.  
```
let [value1,value2] = await Promise.all([getJSON(url1),getJSON(url2)])
```
## 🦑 Asynchronous Iteration  
이전에 언급되었듯 반복적으로 event를 부르기 위해서는 setInterval()을 써야했다. 그러나 Promise는 비동기 event를 순차적으로 실행시킬 수 없기때문에 이 상황에서 async, await는 쓸수없다.  
**13.4.1 The for/await Loop**  
Node 12 makes its readable streams asynchronously iterable  
```
const fs = require("fs");

async function parseFile(filename){
  let stream = fs.createReadStream(filename,{encoding:"utf-8"})
  for await (let chunk of stream){
    parseChunk(chunk);
  }
}
```
for/await loop Promise-based.  
asynchronous iterator가 Promise를 생성하고 for/await이 promise가 fulfill 될때까지 기다린다. fulfill된 value를 loop value로 넘기고 loop body를 실행시킨다. 그리고 asynchronous iterator가 새로운 Promise를 만들고 위의 과정을 반복한다.  
**13.4.2 Asynchronous Iterator**   
iterable object는 for/of loop에서 쓰일 수 있다. 그리고 Symbol.Iterator 이라는 method로 정의되어 있다.  
Symbol.Iterator은 iterator object를 return한다. 또 iterable object에는 next() method가 있는데, 이걸로 value를 반복적으로 받아올 수 있다.  
Asynchronous iterator은 거의 비슷하지만 두가지가 다르다.  
1. implements a method with the symbolic name Symbol.asyncIterator instead of Symbol.iterator  
2. next() method of an asynchronous iterator return a Promise  
**13.4.3 Asynchronous Generators**  
asynchronous iterator를 쓰는 가장 쉬운 방법은 async라고 명시한 generator를 쓰는 것이다.  
await와 yield를 쓸 수 있지만 yield한 value는 Promise로 자동으로 감싸진다.  
```
function elapsedTime(ms){
  return new Promise(resolve => setTimeout(resolve,ms))
 }
 async function* clock(interval, max = Infinity){
  for(let count =1; count <= max; count++){
    await elapsedTime(interval);
    yield count;
  }
 }
 async function test(){
  for await(let tick of clock(300,100)){
    console.log(tick);
  }
 }
```
**13.4.4 Implementing Asynchronous Iterators**  
it is also possible to implement async generators directly by defining an object witn a Symbol.asyncIterator() method that return object with next() method that returns a Promise that resolves to an iterator result object  
```
function clock(interval max = Infinity){
  function until(time){
    return new Promise(resolve => setTimeout(resolve,time - Date.now()))
  }
  return {
    startTime : Date.now(),
    count:1,
    async next(){
      if(this.count > max){
        return {done : true}
      }
      let targetTime = this.startTime + this.count + * interval;
      await until(targetTime)
      return {value : this.count++}
    },
    [Symbol.asyncIterator](){return this;}
  }
}
```
