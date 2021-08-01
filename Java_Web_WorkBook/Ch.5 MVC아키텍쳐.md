# 5장 MVC 아키텍쳐  
[코드는 여기에](https://github.com/suhwoo/show_member_list_with__view)   
## 5.1 MVC 이해하기  
기존의 웹프로그래밍 방식은 올인원방식, 클라이언트요청을 서블릿 혼자서 처리하는 방식이다.  
원래 서블릿이 했던 일은 요철 파라미터를 가공하고 비지니스 로직 및 데이터를 가공하고 결과 데이터를 생성한다.  
  
MVC아키텍쳐는 서블릿이 담당하던 일을 모델과 뷰가 나누어서 처리한다.  
서블릿으로부터 모델은 호풀을 받고 결과를 서블릿에 다시 return한다. 그러면 서블릿은 뷰에 화면 생성을 요청하고 뷰는 결과화면을 서블릿에서 return한다.  
즉, 서블릿은 클라이언트 요청을 받아서 어느 모델, 어느 뷰에 던질지 결정하는 흐름 제어를 당담한다.  
모델은 비지니스 로직 및 데이터를 처리하고 뷰는 겨로가로 화면을 생성한다.  
  
**등장배경**  
세계화가 되면서 글로벌 경쟁이 가속된다. -> 제품 생명주기를 단축 (계속 새로운 걸 내야하니까) -> 조직과 업무가 계쏙 바뀜 -> 프로그램도 자주 바뀜. ->기능추가나 변경이 쉬워야 함 -> MVC 아키텍쳐  
  
**MVC 아키텍쳐의 특징**  
역할을 나누고 의존성을 최소화한다.->변경이 쉽고 기존 코드 재사용성을 높인다.  
**MVC 흐름**  
![image](https://user-images.githubusercontent.com/61738600/127312467-82c3cc61-ea78-4c9c-bd05-914b09409db1.png)  
흐름은 다음과 같다.  
다음의 그림에서 값 객체라는 곳에 모델객체가 생성한 결과값을 담는다. 담고 서블릿에 return하게 된다.  
그럼 뷰가 값 객체를 참고해서 결과화면을 만든다.  
## 5.2 뷰 컴포넌트와 JSP  
뷰 컴포넌트는 JSP로 만든다 -> 왜? -> 화면 만들기가 쉽다!  
원래였으면 java 내에서 
```java
out.println("<html><head><title>회원등록</title></head></html>");
```
이런식으로 일일히 출력해야 했다.  
JSP를 이용하면 그대로 출력할 내용을 작성하면 된다.  
  
그렇다면 JSP가 어떻게 서블릿이 될까?  
Hello.jsp가 JSP엔진을 통해 <Java Source>인 Hello_jsp.java가 되고 자바컴파일러가 이걸 컴파일해서 Hello_jsp.class로 만들어 웹 브라우저에 리턴한다.  
Servlet에서 상속받은 JspPage인터페이스에 HttpJspPage 인터페이스가 있고 이걸 상속받는다.  
또한 GenericSerlvet의 HttpServlet추상 클래스도 상속받는다.  
결국 JSP가 생성한 클래스는 servlet이다.  
  
## 5.3 JSP의 주요 구성요소  

1. 템플릿 데이터  
Servlet class를 만드는데 탬플릿으로 쓰인다.  
타이밍한것들이 out.write();로 생성된다.  
예를 들어
```jsp
<h2>계산기</h2>
```
는 
```java
out.write("<h2>계산기</h2>\n");
```
으로 생성된다.  
2. JSP 지시자  
```jsp
<%@page
  language = "java"//JSP페이지에서 사용할 언어.
  contentType="text/html;charset=UTF-8"//다음의 자바 코드를 생성. response.setContentType("text/html;charset=UTF-8");
  import="java.util.*,java.io.*"//JSP페이지에서 사용할 자바 패키지 임포트 코드를 생성. import java.util.*;
%>
```
3. 스크립트릿  
<%...%> 다음과 같은 곳에 java코드가 들어가 있으면 서블릿 클래스에 _jspService()안에 그대로 복사된다.  
4. JSP선언문  
<%!...%> 안에 클래스 선언부에 들어갈 변수나 메소드 선언.  
즉, for문이나 if문을 바로 넣을 수 없음.  
5. JSP 표현식  
<%=...%> out.print()를 생성한다. out.write("v1") 이렇게 들어가는게 아니라 out.print(v1) 이렇게 그대로 들어간다.  

🔑무엇이 어떻게 변환되는지를 알아야함!  
  
## 5.4 서블릿에서 뷰 분리하기  
  원래는 서블릿이 컴트롤러+모델+뷰  
  이제 뷰를 분리해보자.  
  **뷰 분리**  
  서블릿이 DBMS로 부터 가져온 결과를 값객체(DTO)에 넘겨줘야 한다.  
  그리고 JSP가 값객체로 부터 값을 가져와야 한다.  
  ![image](https://user-images.githubusercontent.com/61738600/127369218-ad796e56-ae13-4c4c-a231-0ca2200230f0.png)  
    
## 5.5 포워딩과 인클루딩
  뷰와 같이 화면 생성을 위임하는 것을 including이라고 한다.
  어느 jsp로 보낼지 지정하면 된다.
  Dispatcher가 배달자라고 보면 된다. 
  즉, 서블릿A가 서블릿B에게 실행을 위임시키고 서블릿B에서 위임된 일을 끝내면 다시 제어권을 A에게 넘긴다.
  
  반면 포워딩은 서블릿A가 B에게 위임해준뒤 서블릿B의 실행이 끝나도 서블릿A로 돌아오지 않는다.
  예를 들어 MemberListServlet에서 에러를 처리하기 위해 Error.jsp로 위임하면 다시 servlet으로 돌아올필요가 없다. 반면 MemberList.jsp에서 header등을 출력하는것을 위임할때는 인클루딩을 써야한다.
  ## 5.6 데이터 보관소
  서블릿에서 제공하는 데이터보관소는 4가지이다. 
  - ServletContext
  - HttpSession
  - ServletRequest
  - JspContext
  ServletContext는 웹 애플리케이션이 시작될때 만들어지고 웹 애플리케이션이 종료될때 소멸된다. 그러므로 웹 애플리케이션 당 1개를 갖는다.
  HttpSession은 브라우저에서 최초로 Servlet컨테이너를 요청할때 생성되고 일정시간동안 요청이 없으면 자동으로 소멸된다. 세션ID를 통해서 사용자별로 관리된다. 100명의 사용자==100개의 세션. 한사람이 브라우저를 100개를 띄워도 하나이다.
  ServletRequest는 클라이언트 요청이 시작되어서 클라이언트 응답이 끝날때까지 유지된다. 요청을 청하는 동안 서블릿끼리 데이터를 공유할때(인클루딩,포워딩) 적합하다.
  JspContext는 JSP페이지를 사용할때 만들어진다. JSP를 실행하는 동안 존재한다. JSP가 만든 데이터를 태그 핸들러가 사용하고자 할때 사용한다.
  
  **ServletContext**
  이전에는 Servlet에서 데이터를 사용하려고 하면 Connection객체를 생성해야 했다.
  매번 데이터가 바뀔때마다 Connection객체를 생성하고...사용하고...Connection객체를 닫고...
  ServletContext를 이용해서 디비 Connection객체를 공유하게 한다.
  AppInitServlet이 Connection객체를 만들고 나머지 Servlet들이 여기서 가져다 쓴다.
  AppInitServlet처럼 서블릿이 공통으로 사용하는 자원을 준비하는 servlet은 web.xml에 servlet태그를 작성한다. 단, 클라이언트에서 요청하진 않아야하므로 mapping은 안한다. 대신 <load-on-startup>을 이용해서 init()를 호출한다. 낮은 숫자부터 생성된다.
  
  기존의 서블릿도 변경해야한다. 직접 Connection하지 말고 ServletContext에서 꺼내쓴다.
  **HttpSession**
  서블릿 컨테이너가 세션식별자를 쿠키에 담아 클라이언트로 보낸다. 클라이언트는 식별자를 메모리에 보관한다. 다음부터는 서버에 요청할때마다 세션 식별자를 보낸다.
  로그인을 요청하게 되면 데이터에서 해당 아이디를 찾아서 HttpSession에 보관한다.
  저장된 객체를 Header.jsp가 가져다가 쓴다.
  로그아웃 요청이 올때는 세션을 무효화시킨다. session.invaildate() 다음에 로그임 요구할때는 새로운 세션이 만들어질 것이다.
  
  
  
  
  

  


  
  




