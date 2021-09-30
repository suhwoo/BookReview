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
