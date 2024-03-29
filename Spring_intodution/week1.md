## ch.1 프로젝트 환경 설정  
https://start.spring.io  
spring boot 기반으로 spring project로 만들어준다.  
Maven,Gradle:필요한 라이브러리 불러오고 라이프 사이클까지 관해주는 tool  
버전은 ()이 안써진걸로했다. 2.5.5  
Dependence:어떤 라이브러리 끌어다 쓸거냐  
일단 spring web과 thymeleaf로 한다.  
Generate를 눌러 다운받는다.  
intellij에서 open project에서 아까 다운받은거에서 build.Gradle  
tomcat로그에서 포트번호 확인하고 http://localhost:8080/쳤을때 에러페이지 나오면 세팅 끝  
IJ2021부터는 preference가 아니라 setting입니다.  
  
## ch2. 라이브러리 살펴보기  
Maven, Gradle같은게 또 의존성을 관리해준다. 의존이 필요한 라이브러리들을 다 가져온다.  
Gradle에서 dependence를 보면 무엇을 의존하고 있는지 알 수 있다.  
print로 출력하면 안되고 log로 출력해야 로그를 모아볼 수 있다.  
spring boot starter을 통해서 spring boot라던지 spring core가 땡겨진다.  
테스트 라이브러리는 요즘은 junit이나 mockito같이.  
  
## ch.3 view환경설정  
spring boot는 resource/static/index.html 을 만들면 이게 welcome page가 된다.  
```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Title</title>
</head>
<body>
HELLO
<a href="/hello">hello</a>
</body>
</html>
```
spring.io -> project에 spring boot->reference document->spring boot feature 같은 곳에서 정보를 알아볼 수 있다.  
매뉴얼에서 검색할 수 있어야.  
  
템플릿엔진을 쓰면 루프를 넣거나 해서 모양을 바꿀 수 있다.(thymeleaf)  
```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {
    @GetMapping("hello")
    public String hello(Model model){
        model.addAttribute("data","hello!!");
        return "hello";
    }
}

```
위 코드는 controller. mapping이 8080/hello 라고 쳤을때 나오는 화면이고  
return "hello" 가 hello라는 html로 이동시키는 것이다. 이 과정을 해주는게 뷰 리졸버  
모델에서 "data"라는 키를 가진 데이터를 hello.html로 넘긴다.  
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
    <title>Title</title>
</head>
<body>
<p th:text="'안녕하세요. ' + ${data}">안녕하세요 손님</p>
</body>
</html>
```
index는 static에 있고 hello는 template에.  

## ch.4 빌드하고 실행하기.
window는 cmd에서.. gradlew build 치면 build 파일이 만들어진다. build->libs->java -jar SHOT.jar 실행시키면 서버 실행된다.  
배포할때 jar 파일 하나만 집어넣고 실행시키면 끝난다.  
build 파일 없앨때는 gradlew clean  

## ch.5 스프링 웹개발 기초 - 정적컨텐츠
1. 정적컨텐츠 - 파일을 그대로 웹브라우저에 내려주는것  
2. MVC와 템플릿엔진 - html을 동적으로 바꿔서 내리는것. controller,view,Model  
3. API - 서버입장에서 JSON이라는 데이터구조 포맷으로 클라이언트에 전달한다.  
세가지 방식.  
  
먼저 오늘은 정적컨텐츠.  
  
static 폴더에서 html 작성.  
그냥 서버에서 전달한다.  
url도 그냥 .html 파일명 그대로 들어오면 된다.  
대신 어떤 프로그래밍을 할 수는 없음  
그냥 내장 톰캣서버에서 해당 html이 있는지 확인하고 있으면 반환해주는 식.  
  
## ch.6 MVC와 템플릿 엔진  
MVC란 모델 뷰 컨트롤러이다.  
뷰는 화면을 그리는데 집중, 모델이랑 컨트롤러는 비지니스 로직.  
뷰는 정말정말 유지보수가 힘들다... 수많은 jsp...  
  
처음에는 에러가 나는데 그 이유는 name이라는 param이 전달이 안되었기 때문.  
@RequestParam("name")의 옵션에 required가 있는데 이게 true로 되어있으면 param이 꼭 필요하다는 뜻이다.  
default가 required=true.  
이걸 false로 하거나 http://localhost:8080/hello-mvc?name=spring 이렇게 param을 할당해 주면 된다.  
컨트롤러 코드  
```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class HelloController {
    @GetMapping("hello")
    public String hello(Model model){
        model.addAttribute("data","hello!!");
        return "hello";
    }
    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model){
        model.addAttribute("name",name);
        return "hello-template";
    }
}

```
뷰 코드
```html
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello' + ${name}">hello! empty</p>
</body>
</html>
```
<p th:text="'hello' + ${name}">hello! empty</p> 이 부분에서 empty는 절대경로로 접근했을때 name대신 저렇게 뜬다.

## ch.7 API
```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {
    @GetMapping("hello")
    public String hello(Model model){
        model.addAttribute("data","hello!!");
        return "hello";
    }
    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model){
        model.addAttribute("name",name);
        return "hello-template";
    }
    @GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name){
        return "hello " + name;
    }
}

```
@ResponseBody 는 http에서 body부에 이 데이터를 넣어주겠다 라는 뜻이다.  
뷰 이런게 없고 그냥 이 문자가 내려간다.  
  
근데 만약 문자가 아니라 데이터를 가져와야 한다면  
```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {
    @GetMapping("hello")
    public String hello(Model model){
        model.addAttribute("data","hello!!");
        return "hello";
    }
    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model){
        model.addAttribute("name",name);
        return "hello-template";
    }
    @GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name){
        return "hello " + name;
    }
    @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name){
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
    }

    static class Hello{
        private  String name;

        public String getName(){
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
}

```
출력은 json방식으로 된다  
브라우저에서 호출을 하면 톰캣서버에서 일단 hello-api있는지 확인.  
있어서 봤는데 ResponseBody 가 붙어있으면 http에 그대로 이 데이터를 넘겨야 한다고 판단한다.  
객체가 반환이 되면 spring입장에서 default가 json 방식으로 data를 만들어서 반환해야겠다고 판단.  
HttpMessageConverter가 동작하고 데이터가 문자인지 json객체인지 판단한다.  
기본 문자이면 StringHttpMessageConverter, 객체면 MappingJackson2HttpMessageConverter.  
  
## ch.8 비지니스 요구사항 정리  
회원관리예제만들기 start~  
데이터:회원Id, 이름  
기능:회원등록, 조회  
(시나리오)아직 데이터저장소가 선정되지 않았다.  

컨트롤러: 웹 MVC 컨트롤러 역할   
서비스: 핵심 비지니스 로직 (중복가입이 안된다던가..)  
리포지토리: DB에 접근  
도메인: 데이터베이스에 저장되고 관리되는 비지니스 도메인 객체   

일단 db가 결정되지 않았으므로 memory에 저장할거고 나중에 변경 가능하도록 인터페이스를 만들것이다.  
  
## ch.9 회원도메인과 리포지토리 만들기  
interface
```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.List;
import java.util.Optional;

public interface MemberRepository {
    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();
}

```
Optional은 무슨 뜻이냐면 null을 반환하는 경우.  
요즘에는 null을 반환할때 Optional로 감싸서 반환한다.  
save로 멤버저장  
findAll로 모든 회원 조회  
```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.*;

public class MemoryMemberRepository implements MemberRepository{

    private static Map<Long,Member> store = new HashMap<>();
    private static long sequence = 0L;

    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member->member.getName().equals(name))
                .findAny();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }
}

```
null이 반환될 가능성이 있는경우 Optional로 감싼다. 클라이언트 쪽에서 처리됨.  
  
## ch.10 회원 리포지토리 테스트 케이스 작성  
자바의 main 메소드를 통해 실행해보거나 또는 컨트롤러를 통해 실행해보거나 -> 준비하는 시간도 오래걸리고 반복테스트로 어렵고  
JUnit이라는 프레임워크를 써서 테스트코드를 만든다.  
assertEquals(member, result); 강의에서 이 부분을 assertThat으로 바꾸었는데 정작 할때 저부분이 안나와서 그냥 equals로 했다.  

testcode
```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

public class MemoryMemberRepositoryTest {

    MemoryMemberRepository repository = new MemoryMemberRepository();

    @AfterEach
    public void afterEach(){
        repository.clearStore();
    }

    @Test
    public void save(){
        Member member = new Member();
        member.setName("spring");

        repository.save(member);

        Member result = repository.findById(member.getId()).get();
        assertEquals(member, result);

    }

    @Test
    public void findByName(){
        Member member1 = new Member();
        member1.setName("s1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("s1");
        repository.save(member2);

        Member result = repository.findByName("s1").get();

        assertEquals(result,member1);
    }

    @Test
    public void findAll(){
        Member member1 = new Member();
        member1.setName("s1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("s1");
        repository.save(member2);

        List<Member> result = repository.findAll();

        assertEquals(result.size(),2);
    }

}

```
전체 test를 돌리면 error가 나는데 이 이유는 각 test 메소드가 순서대로 실행되는게 아니기 때문이다.  
findId->findName->findAll 순서가 아니라 순서가 어떻게 될지 모르기 때문에 이전에 저장됐던 데이터가 나와 false가 된것이다.  
=>이 부분 해결하기 위해서는 test돌리고 나서 데이터를 clear해줘야 한다.  
  
## ch.11 회원서비스 개발  
실제 비지니스 로직 작성  
```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;

import java.util.List;
import java.util.Optional;

public class MemberService {

    private  final MemberRepository memberRepository = new MemoryMemberRepository();

    //회원가입
    public Long join(Member member){
        //같은 이름인 중복이름은 안된다.
        validateDuplicateMember(member);

        memberRepository.save(member);
        return member.getId();
    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m ->{
                        throw new IllegalStateException("이미 존재하는 회원입니다.");
                });
    }

    //전체회원조회
    public List<Member> findMembers(){
        return memberRepository.findAll();
    }

    public  Optional<Member> findOne(Long memberId){
        return memberRepository.findById(memberId);
    }
}

```
좀더 비지니스적인 네이밍을 갖고 있다.  
요구사항에서 무언가 문제가 생겼을때 어느부분에서 문제가 났는지 바로 알아볼 수 있게  
  
## ch.12 회원서비스 테스트코드  
testcode 쉽게 만들기 -> ctrl+shift+t 하면 test만들기가 나온다 JUnit5선택하고 만들고 싶은 test선택.  
```java
   MemberService memberService = new MemberService();
   MemoryMemberRepository memberRepository = new MemoryMemberRepository();

```
store을 clear하기 위해 MemoryMemberRepository를 가져오면 다른 인스턴스이기 때문에 메모리공간이 static이 아니면 사실상 다른 데이터베이스에 저장되는 것이다.  
같은 인스턴스를 쓰게 바꿔주기 위해서는  
```java
private  final MemberRepository memberRepository;

public MemberService(MemberRepository memberRepository){
    this.memberRepository=memberRepository;
}
```
MemberService를 다음과 같이 바꾸고
```java
MemberService memberService;
MemoryMemberRepository memberRepository;

@BeforeEach()
public void beforeEach(){
    memberRepository= new MemoryMemberRepository();
    memberService = new MemberService(memberRepository);

}
```
다음과 같이 객체를 생성해서 넣어주면 된다. 이것을 DI라고 한다.  
아래는 전체 테스트 코드  
```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemoryMemberRepository;
import org.junit.jupiter.api.*;

import static org.junit.jupiter.api.Assertions.*;

class MemberServiceTest {

    MemberService memberService;
    MemoryMemberRepository memberRepository;

    @BeforeEach()
    public void beforeEach(){
        memberRepository= new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);

    }

    @AfterEach
    public void afterEach(){
        memberRepository.clearStore();
    }

    @Test
    void join() {
        //given
        Member member = new Member();
        member.setName("hello");
        //when
        Long saveId = memberService.join(member);
        //then
        Member findMember = memberService.findOne(saveId).get();
        Assertions.assertEquals(member.getName(),findMember.getName());
    }

    @Test
    public void 중복_회원_예외(){
        //given
        Member member1 = new Member();
        member1.setName("spring");

        Member member2 = new Member();
        member2.setName("spring");
        //when
        memberService.join(member1);
        IllegalStateException e = assertThrows(IllegalStateException.class,()->memberService.join(member2));
        assertEquals(e.getMessage(),"이미 존재하는 회원입니다.");
        /**
        try{
            memberService.join(member2);
            fail();
        }catch(IllegalStateException e){
            assertEquals(e.getMessage(),"이미 존재하는 회원입니다.");
        }**/
        //then
    }

    @Test
    void findMembers() {
    }

    @Test
    void findOne() {
    }
}
```


