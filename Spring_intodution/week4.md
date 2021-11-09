  ## ch.24 AOP가 필요한 상황
  모든 메소드의 시간을 측정하고 싶다면? -> 모든 메소드에 System.currentTimeMillis()를 찍어야 한다
  문제는 애초에 주요 기능(core concern)도 아니고 시간을 측정하는 로직은 공통으로 쓰이는 공통관심사항(cross-cuttong concern)이다. 유지보수도 어렵다. 뭐 하나 바꿔야한다면 각각 로직에 들어가서 바꿔야한다.

  ## ch.25 AOP적용
  위와 같은 문제를 해결하는 것을 AOP라고 한다.
  공통관심사항과 핵심관심사항을 구분한다.
  메소드에 시간측정 로직을 다 붙이는게 아니라 시간측정로직을 내가 원하는 메소드에 붙이는 것이다.
  지정만 해주면 된다.
  ```java
  package hello.hellospring.aop;

  import org.aspectj.lang.ProceedingJoinPoint;
  import org.aspectj.lang.annotation.Around;
  import org.aspectj.lang.annotation.Aspect;
  import org.springframework.stereotype.Component;

  @Aspect
  //@Component // Spring bean으로 등록 이렇게 쓰기도 하지만 보통은 Bean에 등록한다.
  public class TimeTraceAop {
      @Around("execution(* hello.hellospring..*(..))")//어디에 지정할건지
      public Object execute(ProceedingJoinPoint joinPoint) throws Throwable{
          long start = System.currentTimeMillis();
          System.out.println("START: "+joinPoint.toString());
          try{
              Object result = joinPoint.proceed();
              return result;
          }finally{
              long finish = System.currentTimeMillis();
              long timeMs = finish-start;
              System.out.println("END: "+joinPoint.toString()+" "+timeMs+"ms");

          }
      }
  }


```
혹은 Component말고 config에 Bean으로 추가한다.
```
@Bean
public TimeTraceAop timeTraceAop(){
    return new TimeTraceAop();
}
```

  어떻게 동작하는가
  원래라면 helloController에서 memberService를 호출하지만
  AOP 적용하면, Spring에서 가짜 멤버서비스인 프록시를 만든다.
  HelloController는 프록시 memberService를 호출한다.
