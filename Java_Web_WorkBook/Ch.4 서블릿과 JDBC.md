## 4.1 JDBC와 데이터베이스  
DBMS등장전에는 직접 파일 입출력 API를 이용해서 직접 하드웨어에서 데이터를 저장해야 했다.  
DBMS가 등장하면서 DBMS가 하드웨어에서 데이터를 꺼내고 애플리케이션 개발자는 DBMS만 사용하면 됐다.  
이 DBMS을 사용할때 규칙이 필요한데 이것이 SQL이다.  
애플리케이션과 DBMS사이에는 네트워크 통신이 필요하다.근데 이 통신이 DBMS마다 다르다  
이 통신이 소캣프로그래밍인데 복잡하고 기술도 비공개이다.  
그럼 어떻게 DBMS를 사용하지? -? DBMS개발사에서는 API를 제공한다.(Vendor API라고 부른다)  
DBMS에 종속된다? -> API를 호출하게 되면 애플리케이션들은 해당 DBMS에 종속된다. -> 다른 DBMS에서 접근이 불가하다.  
그래서 등장한게 ODBC(Open database connectivity)  
DBMS에 접근하기 위한 표준 인터페이스.  
각각의 DBMS회사가 이 OBMS에 따라 API를 만든다.이걸 ODBC 드라이버라고 부른다.->DBMS 종속에서 탈피할 수 있다.  
ODBC의 속도가 문제가된다. ODBC가 직접 데이터베이스에 접근하는게 아니라 ODBC가 Vendor API에 접근하는것이기 때문.  
Java 애플리케이션에서 DBMS에 접근할 경우, ODBC는 C,C++로 접근되어 있으므로 접근불가->JDBC Java 애플리케이션을 위한 DBMS접속 인터페이스  
JDBC Type 1 드라이버: 내부적으로 ODBC API를 호출한다. JRE에 기본으로 포함되어 있고 ODBC를 사용하므로 속도가 느리다. 대신 Text데이터 접근할 때 유용한다.  
Type 2:각각의 DBMS에서 제공. 내부적으로 Vendor API를 호출한다.->ODBC가 불필요하다. 별도 다운로드 필요, ODBC사용하지 않기 때문에 type 1보다 빠르다. Native API호출  
Type 4: DBMS의 전용 프로토콜을 사용한다. 별도의 다운로드 필요, DBMS와 직접 통신하기 때문에 빠르다(퓨어자바)  
## 4.2 데이터베이스에서 데이터 가져오기  
(여담 : 본인은 또 root 비밀번호 잊어먹어서 새로 설치함..)  
Lesson04에 있는 SPMS.sql을 복사해서 mysql work branch에 복사.   
🥕JDBC driver이 필요한데, Programfile x82에 mysql에 보면 있다^^(버전이 달라서 계속 헷갈린다. C++같은거는 ProgramFile에 있음)따로 설치할 필요는 없다. 특히 Window라면 아예 처음부터 설치하기 때문에..   
WEB-INF->lib 밑에 배치해준다.  
```java
package spms.servlets;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

import javax.servlet.GenericServlet;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;

import com.sun.corba.se.pept.transport.Connection;

@WebServlet("/member/list")

public class MemberListServlet extends GenericServlet{

	@Override
	public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		java.sql.Connection con =null;
		Statement stmt=null;
		ResultSet rs = null;
		try {
			//1. 사용할 JDBS 드라이버 등록
			DriverManager.registerDriver(new com.mysql.jdbc.Driver());
			//2. 드라이버를 사용하여 Mysql 서버와 연결하라
			con = DriverManager.getConnection(
					"jdbc:mysql://localhost/studydb",
					"study","study");
			//3. 커넥션 객체로 부터 SQL을 던질 객체를 준비해라
			stmt = con.createStatement();
			
			//4. SQL을 던지는 객체를 사용하여 서버에 질의하라.
			rs = stmt.executeQuery("select MNO,MNAME,EMAIL,CRE_DATE" + 
			" from MEMBERS" + 
			" order by MNO ASC");
			//5. 서버에서 가져온 데이터를 사용하여 HTML을 만들어서 웹브라우저로 출력하라.
			response.setContentType("test/html; charset=UTF-8");
			PrintWriter out = response.getWriter();
			out.println("<html><head><title>회원목록</title></head><body>");
			while(rs.next()) {
				out.println(rs.getInt("MNO") + "," + 
						rs.getString("MNAME")+","+
						rs.getString("EMAIL")+","+
						rs.getDate("CRE_DATE")+"<BR>");
			}
			out.println("</body></html>");
			
		}catch(SQLException e) {
			throw new ServletException(e); //이건 톰캣이 처리한다.
		}finally {
			try {rs.close();}catch(Exception e){}
			try {stmt.close();}catch(Exception e) {}
			try {con.close();}catch(Exception e) {}
		}
	}

}

```
다음과 같이 짜준다. 실행시키면 데이터베이스에서 가져온 정보가 출력되는 것을 알 수 있다.  
🥕trouble shouting: 코드를 짜고 처음 실행했을때  
![image](https://user-images.githubusercontent.com/61738600/126076079-9d3c9701-7359-400e-9890-93441fe4a2e9.png)  
다음과 같은 에러가 떠서 당황했다. 문제점은 @WebServlet("/member/list")인데 @WebServlet("member/list")라고 썼다. 저런 에러가 나온다면 이 부분 확인해보기!  
🥕trouble ~shouting~: 강의에서는 브라우저에 이쁘게 출력되는 반면 직접 쳐보니 list라는 파일이 다운받아졌다. 열어보면 데이터는 맞는데.. explorer에서 띄우면 글자가 깨진다. charset utf-8로 했는데 왜 깨지는지 의문... 이 부분은 아직 해결하지 못했다.  


