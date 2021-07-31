# Server-Side JavaScript with Node  
***  
서론  
***  
Node는 operating system과 binding된 JavaScript로 JS program이 파일을 읽고 쓸수있게 하고 child process를 작동시킬 수 있고 네트워크를 통해 통신할 수 있게 한다.  
Node useful as a:  
- bash나 다른 unix shell의 난해한 구문에 시달리지 않는 shell script에 대한 방안
- 신뢰할 수 있는 프로그램을 실행하기 위한 범용 프로그래밍 언어, 웹 브라우저에 의해 보안 제약 조건의 영향을 받지 않는다.
- 쓰는데 효과적이고 웹서버와의 동시성이 높다.
Node의 특성은 single-threaded event-based concurrency이며 비동기식 API를 사용할 수 있다.  
***  
본론  
***  
## 🦑 Node Programming Basics  
how Node programs are structured and how they interact with operating system  
**16.1.1 Console Output**  
console.log() is not just for debugging but is easiest way to display message to user  
they are lowest way to write to stdout  
Node에서 console.error를 쓸때 아이콘이 표시되진 않지만 console.error()는 stderr stream에 써지기 때문에 console.log()와 차이를 보인다.  
**16.1.2 Command-Line Arguments and Environment Variables**  
Node 도 unix와 마찬가지로 commend-line(from array of strings process.argv) 그 다음으로 environment variables에서 input을 받아온다. 첫번째 인수는 노드 실행경로파일이다.  
- process의 첫번째, 두번째 요소는 노드 실행파일과 실행 중인 JS파일에 대한 파일 시스템 경로가 된다.  
- 노드 실행 파일에 의해 사용되는 Commend-line arguments는 process.argv에 표시되지 않는다.  
Node는 또한 unix style의 environment value로 부터 input을 가져올 수도 있다.  
다음과 같이 치면 환경변수 목록을 볼 수 있다.  
```
$node -p -e 'process.env'
{
  SHELL : '/bin/bash',
  USER : 'david',
  PATH : '/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin',
  PWD : '/tmp',
  LANG : 'en_US.UTF-8',
  HOME : 'Users/david',
}
```
**16.1.3 Program Life Cycle**  
기본적으로 지정 Javascript 코드의 위에서 아래로 실행된다. 일부는 마지막 코드를 실행한 뒤 종료되지만 노드 프로그램은 종종 initial file이 실행된 후에도 실행되기도 한다. 노드 프로그램은 자주 콜백 및 이벤트 핸들러를 기반으로 하고 비동기적 이다. 노드 프로그램은 initial file 실행이 끝나고, 모든 콜백들이 호출되고, pending된 이벤트가 없을 때 종료된다. 그러므로 Node-based server program은 이벤트를 계속 기다리므로 끝나지 않는다.  
다만 process.exit()를 통해 강제로 프로그램을 종료할 수 있다. Node 프로그램은 ctrl-c를 통해 종료할 수 있다. handler function에 process.on(“SIGINT“,()=>{})를 등록하면 ctrl-c를 눌러도 종료되지 않는다.  
만약 catch구문이 없고 exception이 throw된다면 stack trace를 출력하고 exit된다. Node 프로그램은 비동기적 특성을 가졌기 때문에 콜백이나 이벤트 핸들러에서 발생한 예외는 로컬에서 처리되거나 아예 처리되지 말아야한다.  
**16.1.4 Node Modules**  
Node 13 이전에는 자바스크립트가 모듈 시스템을 갖추기 전에 노드가 생성되었기 때문에 노드도 생성해야했다. Node 13이후로 표준 ES6 모듈 과 required-based modules를 지원한다. 두 모듈 시스템이 완전히 호환되진 않는다.  
로드 중인 모듈 종류를 알려주는 가장 간단한 방법은 파일확장자에서 정보를 인코딩 하는 것이다. .mjs로 끝나는 파일에 javascript코드를 저장하면 Node는 항상 이 코드를 ES6 모듈로 로드한다. 그리고 .cjs로 끝나는 파일에 코드를 저장하면 노드는 항상 CommonJS module로 간주한다.  
만약 두 파일 모두 없다면 노드는 package.json파일을 찾는다. 각 파일은 directory에 들어있다. 가장 가까운 패키지 파일을 찾아서 JSON object의 type property를 확인한다. 만약 type가 “module“이라면 ES6 module로 가져온다. 만약 “commonjs“라면 파일을 CommonJS로 로드한다.  
**16.1.5 The Node Package Manager**  
노드를 설치할때 일반적으로 npm 프로그램도 제공된다. npm은 Node Package Manager이며 프로그램이 필요로하는 libraries를 다운로드하고 다루는걸 도와준다. npm은 package.json에서 다른 프로그램 정보를 포함한 종속성을 trace한다.  
npm을 시작하려면 프로젝트에 directory를 만들고 해당 directory에 npm init을 입력하면 npm이 프로젝트 이름, 버전 명을 물어보고 응답을 기반으로 package.json 파일을 생성한다.  
## 🦑 Node Is Asynchronous by Default  
JavaScript는 general-purpose programming language이기 때문에 CPU-intensive program작성이 가능하다. 그러나 Node는 I/O intensive하다. 또한 동시간에 여러 요청을 처리할 수 있다.  
그러나 많은 다른 언어와 달리 Node는 멀티스레드 프로그래밍이 불가하다. 노드는 단일 스레드 자바스크립트 모델을 채택하고 있다.  
Node는 API 비동기화 및 nonblocking을 통해 단일 스레드를 이용하면서도 높은 동시성을 제공한다. Node는 network로 부터의 read, write를 비동기적으로 할 뿐만 아니라 로컬 파일 시스템에 대해서도 비동기적 non-blocking function을 정의한다.  
Node API의 일부 기능은 동기적이면서 nonblocking이다. 대부분의 흥미로운 함수는 입력이나 출력을 수행하며 아주 작은 차담도 피할 수 있는 비동기함수이다. 노드가 Promise이전에 생성되었으므로 비동기 Node API는 콜백기반이다. 보통 마지막 인수는 콜백이다. Node는 error-first callback을 이용한다. 두개의 인수를 가지는 데 첫번째는 보통 오류가 없으면 null이 들어간다. 두번재는 호출되는 원래의 비동기함수에 의한 데이터나 응답이다.  
위에서 노드 프로그래밍 모델이 기본적으로 비동기라고 언급했다. 그러나 편의성을 위해 노드는 많은 함수(특히 filesystem modules에서) blocking, synchronous variants를 정의한다.이러한 함수 끝에는 보통 끝에 Sync라는 label을 붙인다.  
```javascript
const fs = require("fs");
function readConfigFileSync(path){
  let text = fs.readFileSync(path,"utf-8");
  return JSON.parse(text);
}
```
노드에 내장된 nonblocking은 operating system의 콜백과 event handler에 의해서 작동한다. 이 기능 중 하나를 호출하면 노드가 작업을 수행이 완료되면 알림을 받도록 이벤트핸들러가 운영체제에 등록한다. 노드 함수의 콜백은 매부에 저장되므로 운영체제가 이벤트를 노드에 전달하면 노드를 콜백을 호출할 수 있다.  
이러한 동시성을 이벤트 기반 동시성이라고 한다. 핵심은 노드가 “event loop“를 실행시키는 단일 스레드가 있다는 것이다. 노드 프로그램이 실행되면 어떤 코드를 돌라고 지시했든 이 코드는 언제나 실행된다. 이코드는 적어도 하나의 nonblocking function을 호출해 콜백 또는 이벤트 핸들러가 운영체제에 등록하게끔 한다. 혹은 동기적 노드 프로그램으로 노드가 끝에 도달하면 종료하면 된다. 프로그램의 끝에 도달하면 이벤트가 일어날때까지 block한다. 노드는 OS 이벤트를 콜백과 매핑하고 그 함수를 호출한다. 콜백 함수의 작업이 끝나면 노드는 다시 sleep으로 돌아가고 이 과정이 반복된다.  
## 🦑 Buffers  
파일 또는 네트워크에서 데이터를 읽을 때 노드에서 가장 자주 사용하는 데이터타입은 버퍼 클래스이다.  
Buffer과 Uint8Array의 차이는 버퍼에 있는 바이트가 string으로 초기화될 수 있는 것과 문자열로 변환될 수 있다는 것이다. 텍스트 문자열과 문자 인코딩이 주어지면 문자열의 문자를 byte sequence로 인코딩할 수 있다. 그리고 byte sequence와 문자 인코딩이 주어지면 문자로 디코딩할 수 있다. 노드의 버퍼 클래스에는 인코딩과 디코딩 모두를 수행할 수 있는 method가 있다.  
supported encoding :  
“utf8“ : 인코딩이 정해지지 않은 경우 default값.  
”utf16le” : two byte unicode character, with little-endian ordering  
”latin1” : suitable for western european languages. encoding is knwon as “binary“  
”ascii” : The 7-bit English-only ASCII encoding  
”hex” : converts each byte to pair oof ASCII hexadecimal digits  
”base64” : converts each sequence of three bytes into a sequence of four ascii characters  
## 🦑 Events and EventEmitter  
위에서 언급되었듯, Node API는 기본적으로 비동기적이다. 그러나 더 복잡한 API 중 일부는 event-based이다. 이는 API가 함수대신 객체 근처에 설계되거나 콜백함수가 여러번 호출되어야 하는 경우, 필요한 콜백함수가 여러 타입이 있는 경우에 해당된다. net.Sever 클래스는 처음 연결을 들을 때 “listening“ event를 호출한다. “connection“ event는 client와 연결되어 있고 더이상 듣지 않을때 “close“ event가 내보내진다.  
Node는 이러한 이벤트를 instance of EventEnitter이나 subclass of EventEmitter로 내보낸다.  
```
const EventEmitter = require("events");
 const net = require("nets")
 let server = new net.Server();
 server instanceof EventEmitter //=>true : Servers are EventEmitter
 ```
EventEmitter의 가장 큰 특징은 on() method를 통해 event handler에 등록하는걸 가능하게 한다는 것이다. EventEmitter은 여러 타입의 이벤트를 emit할 수 있는데 event types는 name으로 구별된다. event handler에 등록하기 위해서는 on() method에 event type의 name과 그런 type을 가진 이벤트가 발생했을때 호출되는 function을 적는다.  
```
const net = require("net")
let server = new net.Server();
server.on("connection",socket => {
  socket.end("Hello World","utf8");
});
```
보다 명시적인 이름을 짓기 위해서 addListener()을 쓸 수 있다. 혹은 이전 event listener을 지우기 위해 off()나 removeListener()을 쓸 수 있다. 특정 타입의 이벤트가 EventEmitter에서 발생하면 Node는 해당 이벤트 타입에 대해 EventEmitter에 등록되어있는 모든 handler function을 호출한다. 순서는 등록된 순서이다. handler function이 두개 이상인 경우 단일 스레드에서 순서대로 호출된다. 중요한 것은 이벤트 처리가 비동기적이 아닌 동기적으로 실행된다는 것이다.이것은 emit()이 나중에 호출될 event handler를 queue에 넣지 않는다는 것이다. emit()은 모든 핸들러를 차례대로 호출하고 마지막 핸들러가 돌아올때까지 반환되지 않는다.  
이것은 기본으로 제공되는 API에서 이벤트를 보낼때 해당 API가 이벤트 핸들러에서 차단된다는 것을 의미한다. fs.readFileSync()과 같은 차단 기능을 호출하는 이벤트 핸들러의 경우, 동기식 파일읽기가 끝날 때까지 추가 이벤트 처리가 이루어지지 않는다. 네트워크 서버과 같이 응답성이 필요한 경우에는 이벤트핸들러를 차단하지 않고 유지하는 것이 중요하다.  
EventEmitter은 등록된 이벤트 핸들러를 호출하도록 하는 emit()를 정의한다. emit()는 첫 인수로 이벤트 타입의 name을 받는다. 이외의 인수는 등록된 이벤트 핸들러 함수의 인수가 된다.  
이벤트 핸들러로부터 반환된 모든 value는 무시된다. 그러나 exception을 throw하면 emit()에서 propagates되어 그 뒤에 등록된 핸들러 함수를 방지한다.  
Node의 callback-based API는 error-first callbacks을 사용하고 있기 때문에 항상 오류가 일어났는지 확인하기 위해서 첫번째 callback argument를 확인하는 것이 중요하다.  
## 🦑 Streams  
데이터를 처리하는 가장 쉬운 방법은 모든 데이터를 메모리로 읽고 처리하는 것이다.  
```
const fs = require("fs")
function copyFile(sourceFilename,destinationFilename,callback){
  fs.readFile(sourceFilename,(err,buffer)=>{
    if(err){callback(err)}
    else{fs.writeFile(destinationFilename,buffer,callback)}
  })
}
```
차단되지도 않고 동시 프로그램에 적합하지만 만약 저장해야 하는 내용이 많으면 메모리에 저장하는 것에 실패할 수도 있다.  
이 문제의 해결방법은 프로그램으로 데이터가 “flow“되고 처리되고, 프로그램에서 흘러나오는 streaming algorithm을 사용하는 것이다. 데이터를 작은 청크로 잘라 처리하기 때문에 모든 데이터를 메모리에 저장하지 않는다.  
Node는 다음의 네가지 기본 stream 타입을 지원한다.  
- Readable : source of data
- Writable : sinks or destination for data. return value of fs.createWriteStream()
- Duplex : combine a Readable stream and Writable stream into one object. 만약 소켓을 쓰는 경우, 데이터는 네트워크를 통해 소켓이 연결된 모든 컴퓨터로 전송된다.  
- Transform : also readable and writable, but differ from Duplex stream in an important way : data written to a Transform becomes readable from same stream. 예를 들어 crypto.creatCipheriv()는 작성된 데이터를 암호화하거나 복호화하는 변환 스트림을 반환한다.  
기본적으로 스트림은 버퍼를 읽고 쓴다. Readable 스트림의 setEncodeing() method를 호출하면 버퍼 객체 대신 디코딩된 문자열이 반환된다. 그리고 Writable 버퍼에 문자열을 쓰면 버퍼 디폴트 인코딩에 의해 자동으로 인코드 된다.  
Readable stream은 어딘가에서 데이터를 읽어오고 Writable stream은 어딘가에 써야한다. 그러므로 모든 스트림은 두 개의 끝을 갖는다(input, output/ source, destination) 스트림의 양 끝은 서로 다른 속도를 가진다. 예를 들어 데이터를 읽는 것보다 데이터를 쓰는게 더 빠를 수 있다. Buffering은 buffer에 더 쓸 수 있는지 혹은 쓰여져있어서 읽을 수 있는지를 확인하는데 도움을 준다.  
**16.5.1 Pipes**  
stream에서 data를 읽고 그 data를 다른 stream에 똑같이 써야 할때가 있다. reading,writing code를 쓰는 대신 두 socket에 pipe를 연결하고 Node가 다루게 할 수 있다.  
```
const fs = require("fs");
function pipeFileToSocket(filename,socket){
  fs.createReadStream(filename).pipe(socket)
}
Transform Stream을 이용하면 pipes를 이용하는데 유용하다, 또한 두개 이상의 pipes를 사용할 수 있다.
const fs = require("fs");
const zlib = require("zlib");

function gzip(filename, callback){
  let source = fs.createReadStream(filename);
  let destination = fs.createWriteStream(filename + ".gz")
  let gzipper = zlib.createGzip();
  
  source
    .on("error",callback)
    .pipe(gzipper)
    .pipe(destination)
    .on("error",callback)
    .on("finish",callback)
}
```
**16.5.2 Asynchronous Iteration**  
Node12 이후로 Readable stream은 asynchronous iterators 이다. 그리고 이 의미는 async function을 이용하면 for/await을 이용해 스트림에서 string이나 Buffer을 청크로 읽을 수 있다.  
```javascript
async function grep(source,destination,pattern,encoding = "utf8"){
  source.setEncoding(encoding)
  destination.on("error",err => process.exit())
  let imcompleteLine=""
  
  for await(let chunk of source){
    let lines = (incompleteLine + chunk).splite("\n")
    incompleteLine = lines.pop()
    for(let line of lines){
      if (patteren.test(line)){
        destination.write(line + "\n", encoding)
      }
    }
  }
  if(patteren.test(incompleteLine)){
    destination.write(incompleteLine + "\n",encoding)
  }
}

let patteren = new RegExp(process.argv[2])
grep(process.stdin, process.stdout, pattern)
  .catch(err =>{
    console.error(err)
    process.exit()
  })
```
**16.5.3 Writing to Streams and Handling Backpressure**  
위의 예제코드는 또한 write() method를 이용해서 Wrtiable stream에 data를 간단하게 전달할 수 있다는 것을 보여준다.write() 는 버퍼 또는 문자열을 첫번째 인수로 갖는다. 버퍼를 전달하면 버퍼의 바이트가 직접 기록되며 문자열의 경우,쓰기 전에 바이트로 인코딩된다.  
write()는 옵션으로 세번째 인수를 콜백함수로 갖는다.data가 쓰여졌지만 inner buffer에 writable stream이 없을때 호출된다.  
write()는 쓰기가 끝난뒤 버퍼가 다 차지 않았을 경우 true를 반환한다.버퍼가 가득 차거나 더이상 저장할 수 없으면 false를 반환한다.만약 write()를 호출한다면 버퍼의 크기를 늘릴 수 있다.  
A return value of false from the write() method is a form of backpressure : 스트림에서 데이터를 처리할 수 있는 것보다 더 빠르게 기록되었다는 메세지이다. 이 메세지에 대한 적절한 반응은 스트림이 “drain“ event를 내보낼때까지 write() 호출을 중지하는 것이다.  
```
function write(stream,chunk,callback){
  let hasMoreRoom = stream.write(chunk)
  if(hasMoreRoom){ // return true
    setImmediate(callback)
  }else{
    stream.once("drain",callback)
  }
}
```
만약 backpressure이 failed한다면 internal buffer 에 있는 Writable stream이 오버플로가 나고 점점 커질때 메모리가 더 많이 쓰일 수 있음에 주의해야 한다.  
**16.5.4 Reading Streams with Events**  
Node의 Readable stream은 두가지 모드가 있고 각 모드에는 읽기를 위한 API가 있다.  
1.	Flowing Mode  
Readable data 가 도착하면 바로 “data“ event 로 방출한다.이 모드에서 stream으로 부터 읽어들이려면 “data“ event를 위한 이벤트핸들러를 등록하면 스트림이 데이터 청크를 사용할 수 있게되는 즉시 사용자에게 푸쉬한다. flow모드에서는 read()를 호출할 필요 없고 “data“ event만 처리하면 된다.  
write를 하기 위해선 backpressure를 다루어야 한다.false가 반환되었을 경우 pause()를 호출하여 데이터 이벤트를 일시적으로 중지시킨다.그리고 Writable stream에서 “drain“event가 발생하면 Readable stream에서 resume()을 호출하고 데이터 이벤트를 다시 시작한다.  
스트림이 끝에 도달하면 “end“ event를 발생시키고 오류가 발생했을 경우, “error“ event를 발생시킨다.  
2.	Paused Mode  
”data” event 를 이벤트 핸들러에 등록하지 않고 pipe method를 호출하지 않는 경우,Readable stream은 pause mode로 유지된다. read() 를 통해 stream에서 데이터를 가져온다.Readable stream에서 스트림에서 데이터를 읽을 수 있으면 “readable“ event를 방출한다. 데이터가 없으면 null을 반환한다.null이 반환될 때까지 read()를 반복한다.  
마찬가지로 스트림이 종료되면 “end“ event를 발생시키고 오류가 나타나면 “error“ event를 발생시킨다.  
## 🦑 Process, CPU, and Operating System Details  
The global Process objects has a number of useful properties and functions that generally relate to the state of the currently running Node process.  
some properties and functions in p.601  
some os modules in p.602  
## 🦑 Working with Files  
Node의 “fs“모듈은 디렉토리 작업을 위한 포괄적인 API이다. 파일이나 디렉토리와 관련된 작업을 하는 utility작업을 하는 “path”모듈에 의해 보완된다.그러나 모듈에 있는 function들은 유닉스 시스템 호출에 대한 low-level의 JsvaScript 바인딩이다. loe-level language와 비슷하지만, 파일호출“fs“ API의 일부가 더 무의식적이라는 것을 알 수 있다. 예를 들어 파일을 삭제하는 메소드는 unlink()이다.  
“fs“ 모듈은 큰 API를 정의하는데 그 이유는 각 연산에서 multiple variants를 가지고 있기 때문이다. 예를 들어 대부분의 function들은 nonblocking, callback-based, asynchronous 이지만 각 function은 동기적이고 블로킹의 변수를 가지고 있고 노드 10 이상부터는 promise-based 비동기 변수를 가진다.  
**16.7.1 Paths, File Descriptors, and FileHandles**  
fs 모듈을 이용해서 파일을 작업하려면 작업하려는 파일의 이름을 지정해야 한다.보통은 path를 이용해서 정의된다. 만약 path가 절대적이라면 루트까지의 경로가 지정되는 것이다. 상대적이라면 현재 위치해 이는 디렉토리부터 지정한다.  
**16.7.2 Reading Files**  
stream과 달리 Node는 파일의 내용을 한번에 모두 읽어들인다.  
default로 you’ll et bytes of the file as a buffer, 하지만 인코딩을 정의한다면 디코드된 문자열을 갖게 될 것이다.  
```
const fs = require("fs")
let buffer = fs.readFileSync("test.data")
let text = fs.readFileSync("data.csv","utf8")

fs.readFile("test.data",(err,buffer)=>{
  if(err){
    
  }else{
  
  }
})

fs.promises
  .readFile("data.csv","utf8")
  .then(processFileText)
  .catch(handleReadError)
  
async function processText(filename, encoding = "utf8"){
  let text = await fs.promises.readFile(filename,encoding)
}
```
만약 low-level에서 읽어야 한다면 file descriptor을 통해서 파일을 열 수도 있다.  
If you want to read chunk of data from file, you can use synchronous API  
**16.7.3 Writing Files**  
reading과 거의 비슷하지만 몇가지 디테일에서 다르다. 그 중 하나는 새로운 파일을 만드는 방법 중 하나가 아직 존재하지 않는 파일 이름에 writing함으로써 만들수 있다는 것이다. 만약 파일이 이미 존재한다면 end point 부터 text를 append한다.(즉, 덮어쓰지 않는다.)  
만약 buffer이나 string에 모든 내용이 다 들어가있다면 내용을 한번에 write할 수 있다. read와 비슷하게, fs.writeFile()(callback-based),fs.writeFileSync()(synchronous), fs.promises.writeFile()(PRomise-based)로 호출할 수 있다.  
만약 multiple chunk를 이용해 file에 write하고 싶거나 각 chunk들의 위치를 알고 제어하고 싶다면 use the resulting file descriptor with fs.write(),or fs.writeSync()  
만약 array of Buffer에 write하고 싶다면 fs.writev() 나 fs.writeSync()를 쓴다.  
You can chop off the end of a file with fs.truncate(), fs.truncateSync(), or fs.promises.truncate().  
**16.7.4 File Operations**  
이전의 예제로 copyFile()이라는 function이 있었지만 사실 이미 fs.copyfile()이라는 method가 있다.  
argument: name of original file, name of copy, integer(flags that control copy operation), callback function or error argument  
fs.rename() : moves and renames file  
fs.link() : behave like fs.copyFile, create hard links and symbolic links  
fs.unlink() : deleting file.  
**16.7.5 File Matadata**  
fs.stat(),fs.statSync(),fs.promise.stat()  
```
const fs = require("fs")
let stats = fs.statSync("book/sh15.md")
stats.isFile()
stats.isDirectory()
stats.size()
stats.atime()
```
fs.lstat() : if specified file is a symbolic link, Node will return metadata for the link itself  
fs.fatat() for opened file without having specify the filename again if you opened a file to produce file descriptor or FileHandle object  
fs.chmod() : set “mode“ or permissions of file or directory  
fs.chown() : set owner and group for file or directory  
fs.utimes(), fs.futimes() : set access time, modification time  
**16.7.6 Working with Directories**  
To create a new directory in Node, use fs.mkdir()  
argument : path of directory to be create, integer specify mode or pass optional mode  
fs.mkdirSync("dist/lib",{recursive :true})  
fs.rmdir()을 통해 directory를 지울 수 있다. 지우기 전에 디렉토리는 비어있어야 한다.  
“fs” module은 directory로 부터 content를 읽기 위해 두개 의 API를 제공한다.  
1. fs.readdir() : read the entire directory all at once and give you an array of strings or an array og Direct objects. Filename returned by these function are just the local name of file.  
2. fs.opendir() : 수천개의 entries가 있는 디렉토리들을 list한 것을 원할때 fs.opendir을 쓸 수 있다.한번에 하나의 Dir object를 읽어들일 수 있다. callback을 read()에 넣어 호출할 수 있고 생략한다면 Promise를 return한다.  
## 🦑 HTTP Clients and Servers  
Node의 “http“,”https”,”http2” modules는 HTTP 서버 및 클라이언트를 구현하기 위한 포괄적인 API를 정의한다.  
HTTP GET을 하는 가장 간단한 방법은 http.get()이나 https.get()이다.  
argument : URL to fetch, callback that will invoke with IncommingMessage object(Readable stream)  
Also, allows you to write servers that respond to those requests. Basic approach is  
- Create a new Server object  
- Call its listen method to begin listening for requests  
- Register an event handler for “request“ events, use handler to read client’s request.  
## 🦑 Non-HTTP Network Servers and Clients  
HTTP를 쓰지 않으면서 client와 server를 쓰는게 가능하다.  
”net” module은 Server와 Socket class를 정의한다. Server를 만드려면 net.createServer()를 호출한다. 그리고 listen()을 호출해서 어떤 포트로 연결할 것인지 정한다. 만약 client가 해당 포트로 연결되었다면 Server object가 “connection“event를 호출한다.  
client를 만드는 것은 훨씬 쉽다. port number과 hostname을 net.createConnection()으로 넘긴다. 그리고 socket을 이용해서 read,write하면 된다.  
## 🦑 Working with Child Process  
Node에서 “child_process“는 다른 프로그램을 child process로 실행하는 함수가 몇 있다.  
**16.10.1 execSync() and execFileSync()**  
child_process.execSync()을 통해 쉽게 다른 다른 프로그램을 run시킬 수 있다. 이 함수는 child process를 만들고 그 process에서 shell을 run시킨다. 그리고 그 shell을 전달된 command를 실행시킨다.  
만약 command에서 error와 함께 exit된다면 execSync()에서 exception을 throw한다.  
return 되는 default value는 buffer이다. 그러나 두번째 argument에 encoding을 정의하다면 string으로 받을 수 있다.  
execSync()가 full Unix shell에서 실행된다는 것은 command를 보낼때 여러개의 command를 세미콜론을 통해 보낼 수 있다는 것이다. 이것은 또한 함부로 신뢰하지 못하는 source를 통해서 command를 보내서는 안된다는 것을 의미한다.  
**16.10.2 exec() and execFile()**  
execSync()와 execFileSync()는 함수명에서 볼 수 있듯이 synchronous하다. : they block and do not return until the child process exit. 이 기능을 사용하면 일련의 명령을 한번에 하나씩 실행할 수 있다. 심지어 이런 작업이 서로 종속되지 않는 경우, 병렬화하여 여러 명령을 동시에 수행할 수 있다. 이러한 비동기적 작업을 child_process.exec()와 child_process.execFile()을 통해 실행할 수 있다.  
callback함수는 child process가 존재할때 호출된다. 그리고 세개의 argument가 호출된다. 첫번째는 error이다. 두번째 argument는 child process로부터 반환된 collected output이다. 마지막 argument는 child로부터 반환된 error이다.  
ChildProcess로부터 반환된 exec(), execFile()로부터 child process를 중단시킬 수 있다.  
만약 여러개의 child processes를 동시에 써야한다면 “promisified“ version의 exec()를 쓰는것이 가장 쉽다.(Promise객체를 반환한다.)  
**16.10.3 spawn()**  
지금까지의 exec는 child process를 이용해 빠르고 output을 적게 생성하였다.  
child_process.spawn()을 아용하면 심지어 process가 돌고있을떄도 child process의 output에 접근할 수 있다.또한 child process에 write할수도 있다. 즉, child process와 동적으로 상호작용하는 것이 가능하다.  
spawn()은 default로 shell을 사용하지 않기 때문에 execFile()등을 호출해주어 run이 가능하도록 만들어야 한다.  
spawn()을 이용한 ChildProcess는 event emitter을 반환한다. child process가 exit한다면 “exit“ event를 반환받는다. ChildProcess는 또한 세가지 stream properties를 갖는다.  
1. stdout  
2. stderr  
3. Readable stream  
ChildProcess는 또한 pid라는 id를 갖는데 child process를 중지시키길 수 있는 kill()에 정의되어 있다.  
**16.10.4 fork()**  
child_process.fork()는 child Node process에 정의된 함수이다. fork()의 첫번째 인수는 file의 경로여야 한다는 점에서 spawn()과 차이가 난다. 또한 fork()로 만들어진 child는 보다 parent와 child processes들과 communication이 쉽다.  
fork()로 만든 childProcess에는 send() 함수가 있는데 object에 child process를 copy해서 ChildProcess object를 반환할 수 있다.또한 “message“ event를 통해 parent로부터 오는 message를 들을 수 있다.  
```
const child_process = require("child_process");
let child = child_process.fork(`${_dirname}/child.js`)
child.send({x:4,y:3});
child.on("message",message =>{
  console.log(message.hypotenuse);
  child.disconnect();
})
process.on("message", message =>{
  process.send({hypotenuse : Math.hypot(message.x,message.y)});
})
```
child process를 시작하기 위해서 많은 계싼을 해야하고 비용도 많이 든다. 수신 이벤트에 대한 응답성이 높고 시간 소모적인 계산을 해야하는 프로그램을작성할때 child process를 작성해 이벤트 루프를 차단하지 않고 parent process의 응답률을 줄일 수 있다.  
send()에는 JSON형식으로 지원되는 값만 들어간다 두번째 argument는 child process에 있는 Socket과 Server Object를 transfer할 수 있는 것을 허용한다.  
## 🦑 Worker Threads  
Node는 single thread, event-based  
version10부터는 Node도 multi thread 지원한다.  
Difficult한 이유는 한정된 memory를 thread들이 나눠야하기 때문이다. 그러나 Javascript에서 thread들은 default로 not shared하다. => memory를 sharing하기 대신 message passing을 한다.  
main thread가 worker thread에서 postMessage()로 메세지를 보낸다. worker thread도 postMessage()로 parent에게 메세지를 보낸다. 그럼 parent가 “message“ event handler로 듣는다.  
*Node에서 thread가 필요한 3가지 이유  
1. multi task가 있고 CPU core 하나보다 많은 계산을 필요로 할때, thread가 일을 분배해서 할 수 있다.  
2. Even if your application is not using full power of one CPU, you may still want to use threads to maintain the responsiveness of main thread  
3. workers allow us to turn blocking synchronous operations into non-blocking asynchronous operation.  
1**6.11.1 Creating Workers and Passing Messages**  
worker thread의 모듈은 worker_threads이다.  
const threads = require("worker_threads");  
threads.Worker()를 이용해 worker thread를 만들 수 있다.  
```
const threads = require("worker_threads");
if (threads.isMainThread){
  module.exports = function reticulateSplines(splines){
    return new Promise((resolve,reject)=>{
      let reticulator = new threads.Worker(__filename);
      reticulator.postMessage(splines);
      reticulator.on("message",resolve);
      reticulator.on("error",reject);
    });
  };
}else{
  threads.parentPort.once("message",splines => {
    for(let spline of splines){
      spline.reticulate ? spline.reticulate() : spline.reticulated = true;
    }
    threads.parentPort.postMessage(splines);
  });
}
```
Worker() constructor의 첫번째 인수는 thread내에서 돌 Javasript 코드이다.  
또한 두번째 인수로 object를 가질수도 있다.그리고 object의 properties가 optional configuration을 준다.  
Node는 clone algorithm을 통해 object들에게 postMessage()를 copy해서 전달한다.  
**16.11.2 The Worker Execution Environment**  
Worker thread의 javaScript코드는 main thread와 비슷하게 실행되지만 다른 차이점이 있다.  
1.	threads.isMainThread에서 main thread는 true이지만 worker은 false  
2.	In worker thread, threads.parentPort.postMessage()를 사용하여 parent thread에게 메세지를 보내고 parent 로부터 온 메세지를 이벤트 핸들러에 등록하기 위해 threads.parentPort.on을 사용할 수 있다.  
3.	In Worker thread, threads.workerData는 Worker() constructor의 두번째 인수에 의해 결정된다.  
4.	By default, worker thread의 process.env는 parent thread의 process.env의 복사본이다.  
5.	By default, worker의 process.stdin stream은 readable data가 없다.  
6.	By default, worker안에 있는 process.stdin이나 process.stderr은 piped to corresponding stream in parent thread.  
7.	worker이 process.exit()을 하면 thread가 종료된다. process는 종료되지 않는다.  
8.	worker thread는 process의 shared state를 바꿀 수 없다.  
9.	운영체제의 시그널은 main thread에게만 전달된다.  
**16.11.3 Communication Channels and MessagePorts**  
worker thread가 만들어지면 communication channel이 같이 만들어진다. 이 채널은 worker와 parent간의 메세지 전달에 쓰인다.  
worker thread의 API는 또한 web browser에서 쓸수있도록 communication channel을 custom할 수 있다.  
MessageChannel() constructor을 통해 새로운 메세지 채널을 만들 수 있다. 두가지 properties를 가진다. port1, port2. 하나의 port는 “messasge“ event를 발생시켜 다른 port가 Message object로 부터 구조를 clone해 오도록 한다.  
```
const threads = require("worker_threads");
let channel = new threads.MessageChannel();
channel.port2.on("message",console.log);
channel.port1.postMessage("hello")
```
이후 close()를 통해 connection을 닫을 수 있다.  
**16.11.4 Transferring MessagePorts and Typed Arrays**  
postMessage() 는 clone algorithm을 사용하고 SSocket과 Stream은 copy할 수 없다.  
postMessage()는 옵션으로 두번째 인수를 받는데, 복사되지 않고스레드 간 전송되는 개체의 배열이다.  
한번 전송되면 postMessage()를 호출한 thread에서는 더이상 사용할 수 없다.  
```
const threads = require("worker_threads");
let channel = new threads.MessageChannel();

worker.postMessage({command:"changeChnnel",data:channel.port1},
                  [channel.port1]);
channel.port2.postMessage("Can you hear me now")

channel.port2.on("message",handleMessagesFromWorker);
```
MessagePort는 정달될 뿐만 아니라 postMessage()에 의해 배열을 복사할 수도 있다.  
**16.11.5 Sharing Typed Arrays Between Threads**   
thread끼리 배열을 전송할 뿐만 아니라 배열을 공유할 수 있다.  
그러나 JavaScript는 스레드의 안전을 보장하지 않기때문에 이 방법이 권장되지는 않는다.  
```
const threads = require("worker_threads");

if (threads.isMainThread){
  let sharedBuffer = new SharedArrayBuffer(4);
  let sharedArray = new Int32Array(sharedBuffer);
  
  let worker = new threads.Worker(__filename,{workerData:sharedArray})
  worker.on("online",() => {
    for(let i = 0 , i<10_000_000;i++) sharedArray[0]++;
    worker.on("message",() => {
      console.log(sharedArray[0]);
    });
  });
}else{
  let sharedArray = threads.workerData;
  for(let i =0;i<10_000_000;i++) sharedArray[0]++;
  threads.parentPort.postMessage("done");
}
```
공유되는 버퍼가 합리적인 상황은 두 스레드가 공유 메모리의 완전히 별개의 섹션에서 작동하는 경우이다. 
그러나 만약 동일한 구역에 접근해야 하는 경우,Atomic에서 정의한 함수를 이용하여 좀더 안전하게 접근 할 수 있다. 예를 들어 Atomics.add()는 array의 element를 읽고 특정 값을 더하고 array에 그 값을 write한다.  
<생각중인 문제입니다. 곧 답변을 가지고 돌아오겠습니다🦜 >  
Q1. 이벤트기반 동시성 이미지등을 통해 cycle 정확하게 이해하기.  


