# The JavaScript Standard Library  
***  
서론  
***  
Array, number 보다는 덜 functional하지만 standard library라고 불리는 API에 대해 알아보자  
●	Set,Map   
●	TypedArray (binary Array)  
●	Regular expression , RegExp class  
●	Date class  
●	Error class   
●	JSON object (serialized, deserialized)  
●	Intl object (localize javaScript program)  
●	Console object (debugging program)  
●	URL class  
●	setTimeout() (execute after specified interval of time)  
***   
본론  
***   
## 🦑 Sets and Maps  
Js의 object type는 string과 arbitrary value를 map시킬 수 있다.  
Js programing에서map과 set은 자주 쓰인다. 그러나 문자열의 제한과 객체가 map이 아닌 “toString“을 상속하므로 제한이 된다.  
ES6에서는 실제 map과 set의 class를 보여준다.  
**11.1.1 The set class**  
set is collection of value. 그러나 array와 달리 not ordered, not indexed not allow duplicate  
```
let s = new Set();
let t = new Set([1,s]);
let unique = new Set([MISSIPI]) // set은 중복되는 요소를 가지고있지 않기때문에 element는 M,I,S,P
```
set은 add(), delete(),clear() 을 통해 element를 구성할 수 있다. 중복되는 요소를 넣으면 변화가 없다.  
●	add()는 single argument를 가진다. 여러개를 넣고 싶다면 chaining해야한다.  
●	delete()는 한번에 하나만 삭제한다. delete()는 boolean value를 return한다. 만약 삭제하려는 요소가 없다면 false를 return 한다.  
●	element에 대한 check는 strict하다.(===) 요소 1과 “1“은 다르게 취급된다.  
set은 set안에 요소가 있는지 확인하는 것이 중요하다. has()를 통해 set안에 요소가 있는지 확인할 수 있다.  
```
let s= new Set([1,2,3])
s.has(2) // true
s.has(“2“) //false
```
element의 갯수와 관계없이 has() method는 빠르다.  
정작 array에서 사용되는 includes()는 O(n) time이다.  
Set은 iterable하기 때문에 for loop를 이용해서 element를 열거할 수 있다.  
또한 … (spread operator)도 가능하다.  
Set은 ordered하지 않지만 JS에서의 set은 element가 언제 들어왔는지 기억한다. 그리고 iterater은 들어온 순서로 iterate한다. 이러한 이유로 forEach()를 사용하는 것이 가능하다.  
**11.1.2 The map class**  
map은 value와 key를 mapping시킨다. 각 key에는 다른 value를 가리킨다. array와 다른 점은 map은 arbitrary value를 indexed한다.  
```
let m = new Map();
let n = new Map([
  ["one",1],
  ["two",2]
])
```
[key,value] 형식이다.   
map을 만들면 key와 get()을 이용해 value를 찾을 수 있다. key와 set()을 이용하면 새로운 pair을 만들 수 있다. 만약 이미 있는 key를 이용해 set()을 한다면 value의 값만 바뀐다. has()와 delete()는 argument로 key를 넣어서 해당 key가 존재하는지 확인 할 수 있다.  
```
m.set("one",1);
m.get("one");//1
m.has("one");//true
m.delete("one")//true
m.size
m.clear
```
만약 set과 map을 함께 쓴다면 object도 key가 될 수 있다. 그러나 equilty가 아니라 identity 구별하기 때문에 properties가 같다고 해도 같은 key가 되지 않는다.  
map은 iterable이다. 그래서 spread를 쓰면 키와 element순서대로 나열이 된다.  
```
let m = new Map(["one",1],["two",2])
[...m] // [["one",1],["two",2]]
[...m.keys()]
[...m.value()]
[...m.entries()]
```
**11.1.3 WeakMap and WeakSet**  
weakMap is variant(but not actual subclass) of map that does not prevent its key from garbage callected.  
garbage collector은 접근이 불가능하거나 더이상 쓰이지 않는 object의 메모리를 해제해준다.  
weak map의 constructor은 map과 비슷하지만 몇몇 부분에 있어 다르다.  
●	weak map의 key는 object나 array이다.  
●	weakMap은 get(), set(), has(), delete()만 지원한다.  
●	가비지컬렉터가 언제나 삭제할 수 있기 때문에 size()를 쓸 수 없다.  
weak map을 씀으로써 memory lake없이 object와 value를 mapping할 수 있다.  
## 🦑 Typed Arrays and Binary Data  
Java script의 array가 동적이고 빠르긴 하지만 Typed array는 좀더 low level language array와 비슷하다.  
difference beteen array, typed array  
1.	typed array의 element는 전부 number, allowed you to spacified type and size  
2.	must specified length of typed array  
3.	typed array initialized 0 when created  
**11.2.1 typed array types**  
book page 275   
**11.2.2 Creating Typed Array**  
Simplest way: call constructor with numeric argument  
ex) let bytes = new Uint8Array(1024)  
it will initalized 0 or 0.0, 0n  
from이나 of를 통해 미리 argument 특정하는 것도 가능  
```
let white = Uint8ClamedArray.of(255,255,255,0)
```
from에는 array-like나 object가 들어가는데 string은 numer이 아니므로 허용되지 않는다.  
```
let ints = Uint8Array.from(white)
```
이렇게 쓸 수 있다.  
ArrayBuffer타입은 말그대로 memory사이에 buffer을 제공해 원하는 주소에 element를 넣을 수 있다.  
```
let buffer = new ArrayBuffer(1024*1024)
```
buffer자리에 있는 element는 read하거나 write할 수는 없다.  
**11.2.3 Using Typed Array**  
array와 마찬가지로 []를 통해 읽을 수 있다.  
regular array처럼 접근,사용가능.  
```
ints.fill(3).map(x => x*3).join(““) // “9999999“
```
**11.2.4 Typed array methods and properties**  
In addition to standard array methods, typed array also implements a few methods of their own.  
●	set() : sets multiple element or typed array once by copying elements or typed array into new typed array  
ex) let bytes = new Uint8Array(1024)  
let patteren = new Uint8Array([1,2,3,4])  
bytes.set(pattern) // [1,2,3,4]  
●	subarray : return portion of array on which it is called  
ex) let ints = new Int16Array([1,2,3,4,5,6,7])  
let last3 = ints.subarray(ints.length-3,ints.length) // [5,6,7]  
●	buffer : ArrayBuffer of array  
●	byteOffset : starting position of array data  
●	byteLength : length of array data in byte  
**11.2.5 DataView and Endianness**  
Using typed array, you can view same sequence of bytes in 8,16,32 or 64bytes.  
this exposes the endianness  
리틀엔디안에서는 byte가 array buffer에서 최소값에서 최댓값으로 정렬된다.  
예를 들어 리틀 엔디안은 0x00000001 => 01 00 00 00 (big endian => 00 00 00 01)  
네트워크, 또는 파일에서 가져온 데이터를 typed array로 쓰는경우에는 platform의 엔디안이 data의 byte order과 같다.  
Typed data와 DataView class는 이진 데이터를 처리하는 도구를 제공하고 메타데이터를 추출하는 부분에 있어 JS program을 작성할 수 있다.  

## 🦑 Pattern Matching with Regular Expression  
정규 표현식은 텍스트의 패턴을 표현한다. powerful pattern matching, text를 search and replace 할 수 있다. JS의 RegExp는 정규표현을 나타내고 String, RegExp 모두 메소드를 제공한다.  
**11.3.1 Defining Regular Expression**  
JS에서 정규식은 RegExp로 표현되지만 리터럴 구문을 이용해 표현되는 경우가 더 많다.  
```
let pattern = /s$/
```
다음과 같이 / 를 이용해 표현된다.  
이것을 RegExp로 바꾸면 다음과 같다.  
```
let pattern = new RegExp(“s$“)
```
정규표현식은 문자와 메타문자로 구성되어 있다. 여기서 문자는 찾고자 하는 단어의 포함되는 문자이다.  
또한 작동에 영향을 주는 플래그가 붙을 수 있다. 예를 들어 i를 붙이면 대소문자를 구분하지 않는다.  
```
let pattern = /s$/i
```
-Literal character  
JS 정규식에서 백슬래쉬로 시작되는 문자는 알파벳이 아닌 특정 문자를 지원한다.  
표는 p.282   
백슬래쉬는 특정 문자를 지원하므로 / 를 쓰고 싶을때는 double로 써야 한다. 마찬가지로 RegExp constructor을 쓴다면 string이 /를 쓰기 때문에 doubled로 써야한다.  
-Character classes  
[]를 써서 문자 클래스를 만들 수 있다.  
예를 들어 [abc]라고 쓴다면 a, 혹은 b 혹은 c가 들어간 문자를 찾는다.  
^를 붙여 [^abc]라고 쓰면 a 혹은 b혹은 c가 들어있지 않는 문자를 찾는다.  
-(하이픈)를 쓰면 문자의 범위를 나타낼 수 있다. [a-z] 라고 쓴다면 소문자 a부터 z까지를 나타낸다.  
이러한 표현식은 흔하게 쓰이기 때문에 JS에서는 또다른 special character과 escape sequense를 제공한다.  
표는 p.284  
special character-class escapes는 대괄호 안에서 쓰인다. []  
-Repetition  
이전의 표현방식을 통해 any digit이나 특정 단어 뒤에 오는 letters같은걸 표현할 수는 없다.  
이는 정규식의 요소가 반복할 수 있는 횟수를 나타내는 것으로 표현된다.  
표는 p.285  
ex) let r = /\d{2,4}/ // match between two and four digit  
repetition specifier은 단일 문자에만 적용되고 있는데 만약 다중 문자에 적용할 경우, group with parentheses로 적용해야 한다.  
*과 ?를 쓸때는 포함되는 숫자에 0이 포함되어 있음을 주의해야 한다. 예를 들어 /a*/ 는 a를 0번이상 포함하므로 bbb도 답이 될 수 있다.  
-Non-greedy repetition  
이전의 repetition character list는 match as many times as possible while still allowing any following parts of regular expression to match => 이걸 greedy 라고 부른다.  
뒤에 ?를 붙이면 non-greedy way가 된다. 예를 들어 “aaa”에 /a+/를 적용했다면 모든 세 문자에 match되지만, /a+?/ 를 적용한다면 단일문자 a가 된다.  
-Alternation, grouping, reference  
정규표현식에는 alternatives, grouping subexpression, referring previous subexpression이 있다.  
예를 들어 /ab|cd|ef/ 는 ab 이거나 cd이거나 ef이다. 찾는 순서는 왼쪽에서 오른쪽이다. 만약 왼쪽에서 match되는 문자를 찾으면 대체문자는 무시된다.  
괄호는 정규식에서 몇몇 목적으로 쓰인다. 그 중 하나로 item들을 묶어 하나의 +나 | 등으로 작동할 수 있게 한다. 예를 들어 /(ab|cd)+|ef/ 는 한개 이상의 ab나 cd 혹은 하나의 ef를 의미한다.  
괄호의 다른 목적은 완전한 패턴 내에서 하위패턴을 정의하는 것이다. 예를 들어 알파벳 뒤에 나오는 숫자를 중심으로 본다고 할때 (/[a-z]+(/d+)/) 다음과 같이 패턴을 정의할 수 있다.  
표는 p.288  
-Specifying match position  
they anchor the patteren to a specify position in the search string , called regular expression anchors  
제일 많이 쓰는것은 문자 시작을 연결하는 ^와 끝을 연결하는 $이다.  
만약 java만 찾고 싶다면 /^java$/ 다음과 같이 명시한다. 반면 문장안에 java가 있는 문자를 찾고 싶다면 /\sjava\s/다음과 같다. 그러나 여기에는 두가지 문제점을 갖는다.  
1.양쪽에 공백이 있는 경우에만 일치한다.  
2.match되는 부분을 찾으면 선행과 후행에 공백을 포함하여 리턴한다.  
이러한 문제를 해결하기 위해 \s가 아닌 \b로 단어경계를 일치시킨다.  
/\B[Ss]cript/ 는 javascript는 match하지만 script나 Scripting 은 match되지 않는다.  
Regular expression anchor character table is p.290  
-Flag  
정규식은 하나이상의 플래그를 통해 동작을 변경할 수 있다.  
총 6개가 있고 specified after the second, second argument of RegExp()  
●	g : indicate regular expression is global, 문자내 모든 일치 항목을 찾는데 사용된다.  
●	i : 패턴의 일치가 대/소문자를 구별하지 않도록 한다.  
●	m : matching should be done in “multiline“ mode, ^ and $ anchor should match both biginning and end  
●	s : m과 마찬가지로 new-line을 포함하는 text에서 작업할때 유용하다. “.“ character이 line을 포함해서match된다.  
●	u : 유니코드를 의미한다. 정규식이 16비트를 일치시키는 대신 유니코드 코드포인트를 일치시킨다.  
●	y : indicate that regular expression is “sticky“, and should match at the begining of string or the first character following the previous match. 마치 앞부분에 고정시키기 위해 ^를 쓰는것 처럼 처리한다.  
**11.3.2 String Methods for Pattern Matching**  
이러한 정규식이 실제로 자바스크립트에서 어떻게 사용되는지 알아보자.  
이 절에서는 정규식을 이용하여 패턴을 일치, 검색, 바꾸는 작업을 수행하는 문자열 방법을 설명한다.  
●	search()  
String은 정규식에 대해 4가지 method를 제공한다. search()는 정규식을 인수로 사용하고 일치하는 첫번째 문자의 위치 또는 -1을 반환한다.  
"JavaScript".search(/script/ui) // => 4  
만약 인수가 정규식이 아닌 경우 RegExp에 전달한다.  
search는 global search를 지원하지 않으므로 g flag는 무시된다.  
●	replace()  
performs a search and replace operation  
찾아야할 정규식은 첫번째 인자로, 대체될 문자열은 두번째 인수로 들어간다.  
g플래그를 가질 경우 모든 첫번째 인자를 바꾸고, g flag가 없다면 첫번쨔로 발견된 문자열만 바꾼다.  
만약 인수로 정규식이 들어가있지않다면 RegExp에서 문자열을 검색하여 대체한다.  
text.replace(/javascript/gi,"JavaScript");  
●	match()  
인수는 정규식이고 결과는 배열이나 null로 반환된다.  
 만약 flag g를 쓰지 않는다면 첫번째로 일치하는 문자열만 반환한다.  
"7 plus 8 equals 15".match(/\d+/g) // => ["7","8","15"]  
만약 g가 없다면 배열의 첫번째 요소는 일치 문자열이고, 나머지는 괄호화된 캡처 그룹과 일치하는 하위 문자열이다.  
g없이 y를 플래그로 쓸 경우, match()는 단일 항목을 찾는다. 그리고 일치항목은 문자열의 시작부분이다.  
●	matchAll()  
match에서 일치하는 하위 배열을 리턴하는 대신 non-RegExp를 쓸때 iterator를 반환한다. 모든 일치항목을 반복할 수 있는 가장 쉬운 방법이 된다.  
●	splite()  
마지막 method는 splite이다.   
 string을 잘라서 substring의 array로 반환한다. 인수로 separator을 쓴다.  
"123,456,789".splite(",") // =>["123","456","789"]    
또한 seperator대신 정규식을 쓸 수도 있다.  
"1,2,3,\n4,5".splite(/\s*,\s*) // =>["1","2","3","5"]  
**11.3.3 The RegExp Class**  
여기서는 RegExp의 constructor, properties, patteren matching method를 소개한다.  
-constructor  
takes one or two string arguments and create new RegExp object.  
첫번째 인수는 정규식을 포함한 string이다. \ is escape sequence이므로 \를 쓸때는 \\로 써야한다.  
두번째 인수는 옵션으로 flag를 나타낸다.  
```
let zipcode = new RegExp("\\d{5}","g")
```
RegExp는 동적으로 생성되고 정규식으로 표현할 수 없는 리터럴을 쓸 때 유용하다.  
첫번째 인수에 문자열이 아닌 RegExp객체를 넣을 수도 있다.  
-properties  
1.	source :readonly, 정규식의 원본 텍스트  
2.	flag : readonly, set letter of represent flag  
3.	global : readonly, boolean property if true => g is set  
4.	ignoreCase : readonly, boolean, true => i is set  
5.	multiline : readonly, boolean, true => m is set  
6.	dotAll : readonly, boolean, true => s is set  
7.	unicode : “ ,true => u set  
8.	sticky : “ , true => y set  
9.	lastIndex : read,write, g나y flag가 있는 경우, 다음 search가 어디부터 시작되어야 하는지 character posotion을 정한다. 아래의 exec() method와 test() method에 의해 쓰인다.  
-method  
●	test()  
정구식을 이용하는데 가장 간단한 방법. string하나를 인수로 받고 match되면 true를 return한다.  
단순히 exec()를 call하는데 non-null값이면 true를 반환한다.  
●	exec()  
가장 일반적이고 강력한 방법이다. 단일 문자열 인수를 사용하고 일치항목을 찾는다.  
global이 아니라면 match() method를 사용한다. 배열의 [0]에는 정규식에 일치하는 string이 있고 다른 하위 array에는 substring이 담겨져 있다. 반환된 array에는 인덱스속성, 입력속성,스룹속성이 포함되어 있다.  
## 🦑 Dates and Times  
JS API working with dates and times  
-Date constructor  
create Date with no argument : current time  
one argument : number of millisecond since 1970  
two or more : year, month,day,hour,minute,second,millisecond in your local time zone  
주의해야 할 점은 month의 첫 달은 0부터 시작이고 날짜는 1부터 시작이다. 시간 중 0은 자정을 의미한다.  
날짜를 설정하면 컴퓨터의 로컬 시간으로 표시되는데 만약 UTC로 표기하고 싶다면 toUTCString을 이용하면 된다.  
만약 인수로 string을 넣었다면 Date object는 string을 parse해 date와 time으로 나누어본다.  
날짜는 get()과 set()을 통해 가져오거나 바꿀 수 있다.  
가져오는 방법은 getFullYear(),getUTCFullYear(),setFullYear(),setUTCFullYear()이다.  
```
let d = new Date();
d.setFullYear(d.getFullYear()+1)
```
Year을 Month나 Day, Hours등으로 바꿔서 달이나 날짜, 시간을 바꿀 수 있다. 주의할 것은 Day는 readonly이므로 set을 할 수 없다.  
**11.4.1 Timestamp**  
JS는 1970년를 기준으로 밀리세컨드를 쓸 수 있다. 다음과 같은 코드로 날짜에 30초를 추가할 수 있다.  
d.setTime(d.getTime()+30000)  
이러한 밀리세컨드가 timestamps라고 불린다.  
Date.now를 이용해 타임스탬프를 얻어 코드의 작동시간이 어떻게 되는지 볼 수 있다.  

**11.4.2 Date Arithmetic**  
날짜를 <,>,<=,>=으로 비교연산이 가능하다.  
더하기나 빼기 연산자의 경우 밀리세컨드를 통해 연산이 가능하다. 혹은 setDate()를 이용해 n일을 추가하거나 n시간을 추가하는 것이 가능하다. 또한 만약 날짜가 오버플로가 나도 (13월이 된다던가)올바르게 작동한다 (년도+1 , 1월이 된다)  
```
let d = new Date()
d.setMonth(d.getMonth()+3,d,getDate()+14)
```
**11.4.3 Formatting and Parsing Date Strings**  
만약 Date를 보여줘야 할때 Date 타입에서 string으로 바꿔줄 수 있다.  
-toString() : uses the local time zone but does not format date, time lacal-aware way  
-toUTCString() : uses UTC time zone does not format date in local-aware way  
-toISOString() : print year-month-day-hours format of ISO-8601 type  
-toLocalString() : uses local time appropriate for user local  
-toDateString() : format only date portion, local time zone, not appropriate formatting  
-toLocalDateString() : format only date, local time zone, appropriate format  
-toTimeString() : only time, local time zone, not local-aware  
-toLocalTimrString() : format time, local-aware  
## 🦑 Error Class  
JS는 try catch문으로 error를 던질때 Error object를 던진다. 이때의 좋은 점은 오류를 생성할때 예외가 발견되지 않으면 스택추적이 가능하기 때문이다. 에러메세지를 통해서 debuging이 가능하다.  
Error object have two properties : message and name, toString()  
message : Error생성자에게 전달된 값.  
name: Error  
toString() : 필요하다면 message를 string으로 바꿔준다.  
하위 클래스로 EvalError, RangeError등이 있다.  
## 🦑 JSON Serialization and Parsing  
데이터를 저장하거나 전송할때 in -memory data structure을 나중에 본래 데이터로 parsing할 수 있는 string of bytes로 바꾸어야 한다. 데이터 구조를 바이트, 문자 스트림으로 변환하는 과정을 serialization이라고 한다.  
데이터를 직렬화하는 방식 중 가장 쉬운것은 JSON이다. 객체와 배열로 구성된 데이터 구조를 문자열로 변환하기 위해 JS객체와 배열 리터럴 구문을 사용한다.  
JS는 JSON.stringify()와 JSON.parse() 두가지 기능으로 직렬화 및 역직렬화를 제공한다.  
개체를 JSON.stringify하면 직렬화하고 JSON.parse()를 통해 원본 데이터로 되돌린다.  
**11.6.1 JSON Customizations**  
만약 JSON형식으로 지원되지 않는 값을 직렬화하려고 하면 해당 값안에 JSON() method가 있는지 확인하고 그 반황값을 문자열화한다. 다만 다시 생성된 데이터가 원래 데이터와 완전히 동일하지는 않다.  
만약 그 object를 다시 만들어야 한다면 reviver function을 JSON.parse()의 두번째 인자로 보낸다.  
reviver invoked once for each primitive value parsed from the input string. it has two argument, property name, primitive value of object property or array element  
## 🦑 The Internationalization API  
allow us to 날짜 및 시간을 local-appropriate하게 format해주고 문자열을 비교할 수 있게 해준다.  
Intl.NumberFormat  
Intl.DateTimeFormat  
Intl.Collator  
**11.7.1 Formatting Numbers**  
전세계 사람들은 다른 숫자 포맷형식을 갖고 있다. (예를 들어 단위로 . 을 쓰는 나라도 있지만 , 를 쓰는 나라도 있다.)  
Intl.NumberFormat는 format()을 정의함으로써 모든 가능성들을 고려하였다. 두가지 argument가 있다. 첫번째는 숫자형식을 지정하는 local을 지정하는 것이고, 두번째 인수는 숫자가 어떻게 formatting 되어야 하는지에 대한 detail이다. 첫번째는 문자열을 통해 지정하고 (ex)en-US) 두번째 인수인 생성자는 아래 중 하나 이상을 정의한다.  
style : 필요한 숫자 형식의 종류를 지정한다. (예를 들면 십진수)  
currency : 통화의 경우, three-letter ISO currency code를 사용한다 (USD)  
currencyDisplay : 통화의 경우, 통화표시방법을 지정한다.  
useGrouping : false if you don’t want numbers to have thousands separators  
minimumIntegerDigits : 숫자의 정수부분을 표시하는데 사용될 최소 자릿수  
minimumFractionDigits, maximumFractionDigits : 분수부분의 형식  
minimumSignificantDigits, maximumSignificantDigits : control the number of significant digits  
Intl.NumberFormat object를 만들었다면 format에 digit을 넣어 사용할 수 있다.  
**11.7.2 Formating Dates ans Times**  
argument는 formatting number과 같다.  
year : Use numeric 4 or 2 digits.  
month : numeric for “1“, two digit for ”01” long for “January“, short for “Jan“  
day : 한자리 혹은 두자리 숫자의 day   
weekday : long for “monday“,short for “mon“  
era : specified whether date should be formatted with era like BCE  
hour, minute,second : numeric for 2-digit  
timeZone : desired for which date should be formatted  
timeZoneName : specifies desired how time zone should be desplayed  
hour12 : boolean property. whether use 12 hour time  
hourCycle : midnight이 0으로 써지는지 12로 써지는지 24로 써지는지  
**11.7.3 Comparing Strings**  
문자열을 비교하는 것도 나라마다 문자열의 순서가 다르기 때문에 나열하는 것이 어렵다. 그러므로 sort()하나로 정렬할 수 없다. 그러나 Intl.Collator object를 만들면 내부의 compare()를 sort()로 전달하여 local에 맞는 나열을 할 수 있다.  
usage : specified how the collator object is to be used. default value is sort  
sensitivity : specifies whether the collator is sensitive to letter case and accents  
ignorePunctuation : 공잭과 구두점을 무시하려면 true  
numeric : 만약 숫자가 포함되어 있고 문자가 아닌 숫자를 기준으로 정렬하고 싶다면 set true  
caseFirst : 대문자가 먼저와야 하는지 소문자가 먼저와야 하는지. upper이면 A가 먼저, lower이면 a가 A보다 먼저온다.  
## 🦑 The Console API  
console.log()을 통해 stdout에 인수를 인쇄하고 터미널창에서 사용자에게 출력을 나타낸다.  
Console API는 다음과 같은 function을 정의한다.  
console.log() : 인수를 문자열로 변환하고 콘솔에 출력    
console.debug(),http://console.info (),console.warn(),console.error() : stdout 보다 stderr로 내보낸다. prefixed by an icon that indicates its level or severity  
console.assert() : 첫 인수가 true이면 아무것도 하지 않는다. 만약 false면 console.error()와 같이 작동한다.  
console.clear() : clears the console when that is possible  
console.table() : useful in Node programs that need to produce output that summarizes data in tabular form.  
console.trace() : console.log() + output with stack trace  
console.count() : string argument, logs followed by number of times it has been called  
console.countReset() : reset counter for that string  
console.group() : 콘솔에 출력한뒤, set the internal state of console => 관련된 메세지 그룹을 시각화할 수 있다.  
console.groupCollapsed() : works like console.group() in web browser  
console.groupEnd() : no argument, no output, ends the indentation  
console.time() : single string argument, make note of time  
console.timelog() : first argument → console.time(), second argument → console.log()  
console.timeEnd() : single string argument passed to console.time, after called function,it is no legal to call console.timeLog() without first calling console.time()  
**11.8.1 Formatting Output with Console**  
argument를 출력하는 console function이 다음과 같은 two-chracter를 포함한다면 첫 argument를 format string처럼 다룬다.  
%s :the argument is converted to string  
%i and %d : converted to number and then truncated to an integer  
%f : converted to a number  
%o , %O : treated as object property ame, values are displayed  
%c : argument interpreted as a string od CSS styles  
## 🦑 URL API  
create URL object with URL() constructor, argument is absolute URL string  
Once create object, various properties allow you to query various parts of URL.  
그 중 href속성은 URL의 모든 부분을 string으로 호출한것처럼 새 문자열에서 URL parser가 다시 실행된다.  
## 🦑 Timers   
JS 초기부터 web browser은 두가지 function을 지원했다.  
setTimeout(), setInterval() => n 시간이 지난 후에 invoke하도록 한다. 혹은 특정 interval을 기준으로 반복한다.  
setTimeout => first argument is function, second argument is milliseconds. millisecond만큼 elapse하고 function invoke  
ex) setTimeout(() => {console.log(“run!“)},1000);  
default value =0 it means as soon as possible  
setTimeout invoke once. if you want invoke function repeatedly, use setInterval()  
*** 
<고민할 시간을 주시면 답변을 가지고 돌아오겠습니다🦜 >   
***    
Q1. regular expression 번역본으로 다시 보기.  
Q2. match()에서 만약 g가 없다면 배열의 첫번째 요소는 일치 문자열이고, 나머지는 괄호화된 캡처 그룹과 일치하는 하위 문자열이다. => 직접 예시 들어 결과보기….그냥 match를 번역본으로 다시 보기  
Q3.p308 reviver function  
Q4.what is local-appropriate? => A4. 말그대로 사용자의 local에 맞는 시간대  
