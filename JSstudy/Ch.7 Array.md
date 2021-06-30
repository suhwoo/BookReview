# Array  
***  
서론
***  
ordered collection of values, values는 element라고 부른다.  
Array는 untyped → element는 어떤 타입이든 될 수 있다.  
Js의 array는 zero-based, 32bit index를 쓴다.  
index는 0부터 2^23 -2 까지  
dynamic하다. 필요할때마다 grow나 shrink 할 수 있다. => 미리 배열의 사이즈를 정의할 필요가 없다.  
length property가 있다  
Array는 Array.prototype를 상속받는다. => 대부분의 method는 generic하다. =>work not only for Array but Array like object  
***  
본론  
***  
### 🦑 Create Array  
```
let empty = []; //가장 간단한 방법은 [element]
let o = [1,2,3];
let count = [1, ,3]; // 이거도 가능. but index 1 은 undefined로 length는 여전히 3이다.
let count = [,,] //length는 2
```
Object처럼 spread operator 가능 but 나중에 modified는 안됨.  
let letter[…”hello”] => ['h','e','l','l','w']  
혹은 array 이용해서 만든다.  
```
o = new Array() // 빈 array 생성
o = new Array(10) // length가 10인 빈 array
o = new Array(1,2,3,4,5) // 요소가 1,2,3,4,5 인 arrray. 요소가 둘 이상이면 요소가 된다.
Array.of() => invoked one numeric argument
Array.of(1,2,3) => [1,2,3]
Array.from(iterable) = […iterable] // return new Array
```
### 🦑 Read write Array  
read  
```
let a= [“world“]
let value = a[0] // ''w”
```
write
```
a[0] = 3.14
```
혹은 빈 { } object안에 a[1] = “one“ 이런식으로 넣을 수 도 있다.  
### 🦑 Sparse Array
```
let a1 = [,] //인덱스 0에 no element
let a2 = [undefined] // 인덱스 0에 undefined value
```
	❓ Sparse Array vs Dense Array
		배열을 선언할 때 Array함수, 생성자를 사용하면 Sparse Array를 얻는다.
		Sparse Array는 공간이 한정되어 있는데 Dense Array는 여러 공간에 undefined가 되어있는건가?? 
### 🦑 Array.length  
a = [1,2,3,4,5]  
a.length = 3 이면 a = [1,2,3]  
a.length = 0 이면 a = []  
a.length = 5 이면 a의 length는 5지만 비어있다. == new Array(5) 와 같다.  
### 🦑 Adding , Deleting  
add  
```
let a = []
a[1] = “one“ //[,”one”]
a.push(“two“)//[,”one”,”two”]
```
delete
```
delete a[2] // [,”one”]
a.length // 여전히 3. delete는 length에 영향을 주지않는다. -> [, “oen”, ]
```
### 🦑 Iterating Array  
1. for/of로 접근가능  
for (let letter of letters)  
2. entries()를 이용하면 index도 알 수 있다.  
for(let [index, letter] of letters.entries())  
entries()는 배열의 각 인덱스에 대한 키/값 쌍을 가지는 새로운 Array Iterator 객체를 반환  
3. forEach로도 접근가능  
let value  
letters.forEach(letter => {value = letter});  
value  
4. 아니면 그냥 for/length을 통해 접근해도 된다.  
for(let i=0, len = letters.length; i < len ; i++)  
### 🦑 Array method
1. Array Iterator Method  
invoked function once for each element of array  
if it sparse, function pass which is nonexist  
invoked for three argument,  
 - value of array element  
 - index if array element  
 - array itself  
 - forEach()  
**forEach**는 세가지를 return 한다. value of array element, index if array element, array itself.  
parameter에 세가지를 쓰면 차례대로 return 해주는데 만약 value만 필요하다면 parameter을 하나만 적어주면 value만 받는다.  
```
a = [1,2,3]
data.forEach(function(v,i,a){ a[i] = v + 1}) // [2,3,4]
```
**map()**  
map도 array의 각 요소에 정해진 function을 수행한다.  
```
a = [1,2,3]
a.map(x => x*x) // [1,4,9]
```
이때 map은 새로운 array를 return 한다. 즉, 원래의 array에는 영향을 주지 않는다.  
새로운 array는 length가 a와 같고, 만약 a의 요소 중 빈 요소가 있다면 return 되는 array 역시 해당 index가 비어있다.  
**filter()**  
subset array를 return 한다.  
filter의 function은 true 혹은 false를 반환하는 predicate이다.  
predicate에서 true로 반환되는 element를 subset에 포함시킨다.  
```
a = [1,2,3,4,5]
a.filter(x => x>3)
```
**find() , findIndex()**
filter()과 거의 같지만, 두 method는 조건에 맞는 value를 찾으면 하나만 return 한다. 만약 찾지못하면 find()는 undefined를, findIndex()는 -1을 return 한다.
```
a = [1,2,3,4,5]
a.findIndex(x => x===3) //=>2
a.find(x => x>2) // =>3
```
**every() , some()**
두 메소드 모두 predicate이다. every의 경우, 모든 element들이 조건에 맞는지, some의 경우 하나라도 맞는지를 확인한다. true 혹은 false를 return한다.
```
a = [1,2,3,4,5]
a.every(x =< x<10) //true
a.some(isNaN) //false
```
**reduce() , reduceRight()**
reduce는 argument를 두개를 갖는다. 첫번째 argument는 element에 대한 function이고 두번째 value는 initial value이다.
```
a = [1,2,3,4,5]
a.reduce((x,y) => x+y , 0)
0+1 => 1+2 => 3+3 => 6+4 => 10+5 =15
```
reduceRight()의 경우, reduce와 동작은 같지만 동작하는 index가 오른쪽에서 왼쪽으로 동작한다.  
2. Flattening arrays  
ES6부터 flat을 이용해 array를 flattened 시킬 수 있다.  
```
a = [1,2,[3,4]]
a.flat() => [1,2,3,4]
```
argument가 없으면 one level로만 flatten시킨다.  
```
const arr5 = [1, 2, , 4, 5];
arr5.flat(); // [1, 2, 4, 5]
```
num이 argument로 들어가면 그 num번 만큼의 level로 flatten된다.  
```
a = [1,2,[3,[4]]]
a.flat(1) // [1,2,3[4]]
a.flat(2) // [1,2,3,4]
a.flat(3) // [1,2,3,4]
```
flatMap()은 a.map().flat()와 같다.  
```
let p = [“hello world“,”the definitive guide”]
let word = p.flatmap(p => p.split(“ “));
word => [“hello“,”world”,”the”,”definitive”,”guide”]
	
const newArr = arr.flat([depth])
```
3. Adding arrays  
concat()은 concat을 발생시킨 array를 새로운 element를 더해 새로운 array로 return한다.  
```
a = [1,2,3]
a.concat(4) => [1,2,3,4]
a.concat([5,6]) => [1,2,3,4,5,6]
a.concat(4,[6,[7,8]]) => [1,2,3,4,5,6,[7,8]]
```
4. Stack and Queue  
pop(), push(): 배열의 맨 끝에서 추가 제거  
```
let stack = [];
stack.push(2) // [2]
stack.push([2,3]) // [2,[2,3]]
stack.pop() // [2] return [2,3]
stack.pop() // [] return 2
stack.push(…values) // 이것도 가능
myArray.push('Bradford', 'Brighton'); //여러 값을 넣어도 됨
```
**shift(), unshift()**: 배열의 처음 부분의 원소를 추가 제거  
shift는 ← 이쪽으로 한칸 밀기.  
unshift는 → 이쪽으로 한칸 밀기  
```
let q = []
q.push(1,2,3,4,5) // [1,2,3,4,5]
q.shift() // [2,3,4,5] return 1
q.unshift() // [2,3,4] return 5
```
5. Subarray 
**slice()**  
two argument, (start point, end point+1)  
```
a = [1,2,3,4,5]
a.slice(1,3) // return [2,3]
```
**splice**  
element를 insert하거나 delete하기위해 쓰인다.  
다른 method와 달리 실제로 array에 영향을 준다.  
```
a = [1,2,3,4,5,6,7,8]
a.splice(4) // 인덱스 4부터 마지막 인덱스까지. return [5,6,7,8] a=[1,2,3,4]
a.splice(1,2) // 인덱스 1부터 2까지 .return [2,3] a=[1,4]
```
**fill**  
specific value로 array를 채운다.  
```
a.fill(8,2,-1) // 8로 채워라, index 2부터 LastIndex-1까지. (LastIndex가 없다면 끝까지.)
```
**copyWithin**  
a.copyWithin(어디에, 어디서부터, 어디까지)  
```
a = [1,2,3,4,5]
a.copyWithIn(1) // parameter이 채워지지않으면 자동으로 첫 인덱스는 0, 마지막은 인덱스 끝으로 채워진다. [1,1,2,3,4]
a.copyWithin(2,3,5) // [1,1,3,4,4]
```
6. Array searching and Sorting Methods  
**indexOf(), lastIndexOf()**
```
a = [0,1,2,1,3]
a.indexOf(1) // 1 가장 먼저 찾은 value의 index를 return
a.lastIndexOf(1) // 조건과 맞는 value의 가장 마지막 index
만약 찾는 valuse가 없으면 -1 return
```
value와 index 둘다 찾고 싶으면 a.indexOf(a,index) 두개를 받으면 된다.  
**includes()**
만약 array에 value있으면 true 없으면 false.  
index는 반환하지 않는다.  
그럼 indexOf를 쓰면되는데 include를 왜 쓰냐! => include는 NaN도 있는지 없는지 알 수 있다. indexOf는 sparse이기때문에 NaN은 -1로 반환한다.  
**sort()**  
sort()가 argument가 없으면 알파벳 순서로 sorting해준다.  
예를 들어 숫자를 순서대로 하고 싶다.  
a = [33,4,111,222] 라고 해도 a.sort는 알파벳 순서이기 때문에 [1111,222,33,4] 가 나온다.  
이런걸 없애려면 a.sort(function(a,b){return a-b})처럼 argument로 function을 지정해야 한다.  
7. Array to String  
join() method는 array를 string으로 바꿔준다.  
a = [1,2,3] 일때  
a.join() = “1,2,3“  
join argument가 들어가면 콤마를 대신한다.  
a.join(“ “) = “1 2 3“  
toSting과 같은 method는 join() - no argument 와 같다.  
8. Array.isArray()를 사용하면 array인지 알수있다. return은 true, false  
### 🦑 Array-Like Object  
list like objects 리스트 같은 객체  
Array의 특징 :  
 - length property 존재.
 - setting length to smaller value truncates the array
 - Array.prototype를 상속받는다.
 - Array.isArray() return true
그러나 이 모든 조건이 필요충분조건은 아니다. 이 조건을 채우지 않아도 array처럼 쓸 수 있다.  
### 🦑 String as Array  
```
let s = “test“
s.charAt(0) => ”t”
s[1] =>”e”
```
string은 array는 아니지만(isArray의 경우 false가 나온다.) charAt() 등을 통해 string처럼 쓸 수 있다.  

<임시question입니다, 생각할 시간을 주시면 답변(A)를 가지고 돌아오겠습니다🦜 >  
Q1. let count = [,,] //length는 2 이때 length가 2가 되는 이유가 ,가 어떤 특성을 가지고 있어서라고 설명되는데 이부분 이해가 잘 안가서 나중에 다시 돌아오기로..  
Q2. map과 flatmap의 차이 p170 replay
