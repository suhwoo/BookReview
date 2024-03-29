## ch.13 컴포넌트 스캔  
스프링 빈 등록하고 의존관계 설정하기  
화면을 붙이고 싶은데 controller랑 view가 필요할거다.  
이제 화면을 붙이려면 controller가 MemberService를 통해서 데이터를 조회하고~~ 이런거 할 수 있어야 한다. 이걸 의존성이 있다고 한다.  

MemberController을 만들면 Spring 컨테이너에 이 객체가 생겨 spring에 넣어두고 spring이 관리한다.  
MemberService를 가져다 쓰려면 new를 이용해서 이용했는데 이거보다 Spring 컨테이너로 부터 가져다 쓰도록 바꿔야한다.  
다른 Controller에서도 이 서비스를 사용할 수 있는데 굳이 하나만 생성하면 되지 각 Controller마다 생성할 필요는 없다.  
->앞으로는 Spring Container에 등록을 하고 쓴다.  
```java
@Autowired
public MemberController(MemberService memberService){
    this.memberService = memberService;
}
```
이렇게 @Autowired로 묶어주면 자동으로 연결이 된다.  
이렇게만 하면 @Controller는 Annotation이 되서 Spring Container에 들어가지만  
MemberService는 그냥 java code라서 컨테이너에서 찾을 수 없다고 뜬다. -> @Service Annotation을 넣어주면 된다.  
(@Repository도 마찬가지)  
마찬가지로 MemberService에서도 MemberRepository가져오는 부분에 @Autowired해줘야 한다.  

스프링 빈을 등록하는 2가지 방법.  
1.컴포넌트 스캔과 의존관계 설정  
2.자바 코드로 직접 스프링 빈 등록하기  

@Service같은게 사실 들어가면 @Component가 들어가있다. 그래서 이러한 방식을 컴포넌트 스캔이라고 한다.  

아무데나 @Component 써도 되나요? -> 하위 패키지가 아닌 얘들은 등록되지 않는다.  
스프링 빈은 등록될때 기본으로 싱글톤(하나만)으로 등록된다.  
예제는 아래와 같다.  
```java
...

@Service
public class MemberService {

    private  final MemberRepository memberRepository;

    @Autowired
    public MemberService(MemberRepository memberRepository){
        this.memberRepository=memberRepository;
    }
...
```
![image](https://user-images.githubusercontent.com/61738600/135397656-17c8c3bd-cf66-4a16-b38a-379e77a2df57.png)  
  
## ch.14 자바코드로 직접 스프링 빈 등록하기.  
```java
package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    @Bean
    public MemberService memberService(){
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository(){
        return new MemoryMemberRepository();
    }
}

```  
- Controller는 어차피 spring이 관리하기 때문에 그대로 @Controller, @Autowired를 쓴다.  

- 생성자에 넣는 것을 생성자 주입이라고 하고 @Autowired private MemberService memberService;처럼 필드주입할 수도 있다.  
setMemberService처럼 setter주입도 할 수 있는데 public하게 노출되어 있어야한다.  
결론적으론 요즘 권장하는 스타일은 생성자 주입이다.  
- (이게 중요)실무에서는 정형화된 컨트롤러, 서비스, 레포는 컨포넌트로 상황에 따라 바껴야 하는거면 설정으로 스프링 빈에 등록한다.  
예를들어 메모리 연결 레포를 나중에 데이터베이스에 연결한다던가 하는 식으로 바꿀 때 기존 코드 수정없이 할 수 있다.  
- spring에서 관리하지 않는 (스프링 빈에 등록되지 않은 객체는) AutoWired 불가  
  
## ch.15 회원 웹 기능 홈화면 추가  
회원을 등록하고 조회할 수 있는 사이트 만들기  
html 우선순위! -> 요청이 오면 우선 스프린 컨테이너에 관련 컨트롤러가 있는지 찾고 없으면 static파일을 찾는다.  
welcome file도 마찬가지. HomeController가 있고 getmapping("/")가 있으니 static 파일인 index.html은 무시된다.  
```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {
    @GetMapping("/")
    public  String home(){
        return "home";
    }
}
```
위는 HomeController  
아래는 Home.html  
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

<body>

<div class="container">
    <div>
        <h1>Hello Spring</h1>
        <p>회원 기능</p>
        <p>
            <a href="/members/new">회원가입</a>
            <a href="/members">회원목록</a>
        </p>
    </div>
</div>

</body>
</html>
```  
  
## ch.16 회원웹기능 등록  
```java
package hello.hellospring.controller;

import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class MemberController {
    private final MemberService memberService;

    @Autowired
    public MemberController(MemberService memberService){
        this.memberService = memberService;
    }

    @GetMapping("/members/new")
    public String createForm(){
        return "members/createMemberForm";
    }
}

```
home.html에서 /members/new로 이동하면 controller에서 members/createMemberForm.html로 보낸다.  
아래는 createMemberForm.html  
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

<body>
<div class="container">
    <form action="/members/new" method="post">
        <div class="form-group">
            <label for="name">이름</label>
            <input type="text" id="name" name="name" placeholder="이름을 입력하세요">
        </div>
        <button type="submit">등록</button>

    </form>
</div>
</body>
</html>
```

이제 MemberForm을 통해 Member를 저장해보자.  
Memberform 코드
```java
package hello.hellospring.controller;

public class MemberForm {
    private String name;

    public String getName(){
        return name;
    }

    public void setName(String name){
        this.name = name;
    }
}
```
MemberController에서 다음 코드를 추가한다.  
```java
@PostMapping("/members/new")
public String create(MemberForm form){
    Member member = new Member();
    member.setName(form.getName());

    memberService.join(member);

    return "redirect:/";
}
```   
form을 통해 받은 이름을(home.html에 form으로 감싸져있다.) 새로운 member객체에 저장하고 그 member객체를 회원으로 저장한다.  
return "redirect:/" 를 통해 홈화면으로 돌아간다.  
이름을 저장하는 것은 post를 썼는데, 보통 데이터를 등록할때 post, 조회할때 get을 쓴다.  
![image](https://user-images.githubusercontent.com/61738600/135589606-c8690153-8abe-45ce-8a40-41bbe5ee7c17.png)
  
## ch.17 회원 웹 기능 조회  
MemberController에 다음과 같이 /members 경로(회원조회하는 페이지)를 mapping한다.  
```java
@GetMapping("/members")
public String list(Model model){
    List<Member> members = memberService.findMembers();
    model.addAttribute("members",members);
    return "members/memberList";
}
```
memberList.html의 코드는 아래와 같다.  
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

<body>
<div class="container">
    <div>
        <table>
            <thead>
            <tr>
                <th>#</th>
                <th>이름</th>
            </tr>
            </thead>

            <tbody>
            <tr th:each="member : ${members}">
                <td th:text="${member.id}"></td>
                <td th:text="${member.name}"></td>
            </tr>
            </tbody>
        </table>
    </div>
</div>
</body>
</html>
```
memberList.html에서 ${members}에서 모든 회원을 저장한 member을 담은 model을 받는다.  
안에 있는 모든 회원의 id와 name을 꺼낸다.  
그러나 메모리에 저장된것이기 때문에 서버를 껐다가 키면 모든 데이터가 사라진다.  





