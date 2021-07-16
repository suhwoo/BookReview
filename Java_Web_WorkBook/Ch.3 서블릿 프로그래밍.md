## 3.1 웹 애플리케이션과 CGI규칙  
CGI = Common Gateway Interface  
규칙을 정하지 않으면 웹서버와 애플리케이션이 데이터를 주고 받을 수 없다.   
웹서버는 CGI규칙에 따라 웹 브라우저에게 전달하고 애플리케이션은 웹서버에 CGI규칙에 따라 짜여진 프로그램이다.  
실행유형은 두가지, 컴파일방식과 인터프리터 방식.  
애플리케이션이 C나 C++처럼 기계어로 되어있다면 바로 실행된다.  
인터프리터 방식은 소스 그 자체로 되어있다.->직접 실행될 수 없으니 스크립트 엔진이 필요하다. 이 스크립트 엔진이 바로 인터프리터이다.   
웹서버 - 스크립트 엔진 - 스크립트  
로 되어있으며 스크립트를 스크립트 엔진이 해석하고 결과를 웹서버로 넘김다.  
자바 웹 애플리케이션은 .class의 바이트 코드이므로 인터프리터 방식을 이용한다. (Java virtual machine)  
그러나 JVM는 CGI프로그램이 아니다. 그래서 웹서버에서 JVM을 실행할 수 없다. -> CGI규칙에 따라 자바 프로그램 관리 서버를 준비해야 한다  
이 자바 프로그램을 관리하는 프로그램을 "서블릿 컨테이너"라고 부른다.  
서블릿 컨테이너의 예시로는 Tomcat, Jetty,JEUS 등이 있다. 또 이걸 WAS라고 표현한다.  
## 3.2 웹 프로젝트 준비(servlet만들기)
servlet가 톰캣을 이용해 실행하려면 javax.servlet.Servlet이라는 규칙에 따라서 만들어야한다  

```java
@Override
	public void destroy() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public ServletConfig getServletConfig() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public String getServletInfo() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public void init(ServletConfig arg0) throws ServletException {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void service(ServletRequest arg0, ServletResponse arg1) throws ServletException, IOException {
		// TODO Auto-generated method stub
		
	}
```
위 다섯개의 함수를 반드시 구현해야 한다.   
getServletConfig는 Servletconfig를 return 하는데 그러므로 
```java
Servletconfig config;
@Override
	public void destroy() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public ServletConfig getServletConfig() {
		// TODO Auto-generated method stub
		return this.config;
	}

	@Override
	public String getServletInfo() {
		// TODO Auto-generated method stub
		return "HelloHello";
	}

	@Override
	public void init(ServletConfig arg0) throws ServletException {
		// TODO Auto-generated method stub
    this.config=arg0
		
	}

	@Override
	public void service(ServletRequest arg0, ServletResponse arg1) throws ServletException, IOException {
		// TODO Auto-generated method stub
		
	}
```
다음과 같이 인스턴스에 보관하자.  
메소드를 호출하는게 톰캣.  
getServeletInfo에서도 Servlet에 대한 정보를 주자.  
  
톰캣을 처음으로 init(주로 자원준비)을 호출하고 client 서비스 요청이 들어올때마다 service를 호출한다.  
톰캣서버, 웹 어플리케이션을 stop할때 destory를 호출한다.->마무리작업이라던지  
getServletConfig와 getServletInfo는 관리자가 설정을 변경하거나 정보를 알고싶을때.  
   
 servlet을 만들었다면 톰캣에게 알려줘야한다. 
 WebContent -> Web-INF -> web.xml 에서
 ```xml
  <!-- 서블릿정의 -->
  <servlet>
  	<servlet-name>Hello</servlet-name>
  	<servlet-class>lesson03.servlets.HelloWorld</servlet-class>
  </servlet>
  <!-- 서블릿과 url연결 서블릿을 언제 실행할건지! -->
  <servlet-mapping>
  	<servlet-name>Hello</servlet-name>
  	<url-pattern>/okok/Hello</url-pattern>
  </servlet-mapping>
 ```
다음을 추가해준다.  
![image](https://user-images.githubusercontent.com/61738600/125818046-8c59ae7b-737c-44b7-8bfa-f6ec97955e0c.png)  
다음과같이 넣으면  
![image](https://user-images.githubusercontent.com/61738600/125818201-56c48937-6b0f-4d5a-821c-b9f7a063ed98.png)  
다음과 같이 init과 service가 실행된다.  
이후 새로고침해도 service만 실행된다.  
![image](https://user-images.githubusercontent.com/61738600/125818450-05cb0a4f-ab25-4c54-b8a4-3e24c96a0251.png)  
서버를 종료하면 destory가 실행된다  
  
```xml
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
```
serlvet아래에 다음과 같이 welcome-file이 있는데 이러한 welcome파일은  
http://localhost:8080/web03  
다음과 같이 프로젝트 이름까지만 쳤을때 나오는 화면이다.  
index.html이 없으면 다음파일(index.htm) 없으면 그다음 ...  
이런 방식으로 위에서부터 해당 파일이 있는지 찾는다.  
## 3.3 웹 애플리케이션 배치(이건 왜 필요한거지?)  
1. 이클립스를 통한 자동 배치 -> 서버에 add and remove  
2. 톰캣서버는 배치된 폴더로 간다. workspace~이런 폴더. 프로젝트의 property밑에 resource를 보면 어디에 있는지 알 수 있다. workspace->.metadata  
class들은 WEB-INF밑에 배치된다.  
lib~index.html까지는 ROOT밑의 web03안에 그대로 있다.  
실제로 톰캣에 웹 프로젝트를 배치하면 tmp0라는 임시폴더에 프로젝트 이름으로 배치된다.
3. 톰캣 운영서버에 배치하기.  
이클립스 File옵션에서 export선택.-> Web-> War파일로 -> 이 War파일을 톰캣에 배치 -> bin에서 startup  
## 3.4 GenericServlet의 사용  
인터페이스란 무엇일까?  
호출자와 비호출자 사이의 호출규칙을 말한다.  
예를 들어 servlet interface. 호출자는 톰캣서버, 피호출자는 자바프로그램이다. 이 사이의 호출규칙을 정의한것이 servlet interface이다.  
이중 가장 중요한게 service메소드이다. 자바프로그램이 해야할 일이 여기에 들어간다.  
이 자바프로그램을 서블릿클래스라고 한다.  
사실상 service이외는 필요할때만 구현하는데 그럼에도 불구하고 5개를 모두 구현하는건 복잡하다. -> GenericServlet추상클래스를 쓴다!  
추상클래스란 subclass의 공통적인 메소드를 가지고 있는 클래스이다.  
이제 GenericServlet만 상속받아서 service만 구현하면 된다  
```java
package lesson03.servlets;

import java.io.IOException;

import javax.servlet.GenericServlet;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;



public class HelloWorld2 extends GenericServlet{
	private static final long serialVersionUID = 1L;
	@Override
	public void service(ServletRequest arg0, ServletResponse arg1) 
		throws ServletException, IOException {
			// TODO Auto-generated method stub
			System.out.println("service");
		}
}

```
xml에도 추가시킨다.  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd" id="WebApp_ID" version="4.0">
  <display-name>web03</display-name>
  <!-- 서블릿정의 -->
  <servlet>
  	<servlet-name>Hello</servlet-name>
  	<servlet-class>lesson03.servlets.HelloWorld</servlet-class>
  </servlet>
    <servlet>
  	<servlet-name>Hello2</servlet-name>
  	<servlet-class>lesson03.servlets.HelloWorld2</servlet-class>
  </servlet>
  <!-- 서블릿과 url연결 서블릿을 언제 실행할건지! -->
  <servlet-mapping>
  	<servlet-name>Hello</servlet-name>
  	<url-pattern>/okok/Hello</url-pattern>
  </servlet-mapping>
    <servlet-mapping>
  	<servlet-name>Hello2</servlet-name>
  	<url-pattern>/hihi/Hello2</url-pattern>
  </servlet-mapping>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```
그럼 http://localhost:8080/web03/hihi/Hello2 를 치면 다음과 같이 나온다  
![image](https://user-images.githubusercontent.com/61738600/125945730-c0ad4b0a-2dde-46b7-b0f4-5dde2adfcd06.png)  
계산기 servlet에는
```java
@WebServlet("/calc")
@SuppressWarnings("serial")
```
다음과같이 되어있는데 이는 @SuppressWarnings("serial")는 private static final long serialVersionUID = 1L;를 대신하여 경고를 띄우지 않도록 하고, @WebServlet("/calc")는 xml에 번거롭게 경로를 지정해줄 필요를 없앤다.  





