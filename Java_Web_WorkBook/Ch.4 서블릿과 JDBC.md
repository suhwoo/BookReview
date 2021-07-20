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
## 4.3 HttpServlet으로 GET요청다루기  
HttpServlet은 GenericServlet을 상속받고 class는 HttpServlet을 상속받는다.  
클라이언트가 요청한 방법에 따라 doGet(),doPost()등으로 override한다.  
링크를 클릭하면, get요청이 일어난다.  
```java
package spms.servlets;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@SuppressWarnings("serial")
@WebServlet("/member/add")
public class MemberAddServlet extends HttpServlet {
	@Override
	protected void doGet(
			HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		response.setContentType("text/html; charset=UTF-8");
		PrintWriter out = response.getWriter();
		out.println("<html><head><title>회원 등록</title></head>");
		out.println("<body><h1>회원 등록</h1>");
		out.println("<form action='add' method='post'>");
		out.println("이름: <input type='text' name='name'><br>");
		out.println("이메일: <input type='text' name='email'><br>");
		out.println("암호: <input type='password' name='password'><br>");
		out.println("<input type='submit' value='추가'>");
		out.println("<input type='reset' value='취소'>");
		out.println("</form>");
		out.println("</body></html>");
	}
}

```
![image](https://user-images.githubusercontent.com/61738600/126267076-44279d4f-3a62-4f48-8815-c0d3f5ae7f2a.png)  
다음 페이지가 나오는데 추가를 눌렀을때는 post함수가 호출되므로 405에러가 뜬다.  
```java
package spms.servlets;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@SuppressWarnings("serial")
@WebServlet("/member/add")
public class MemberAddServlet extends HttpServlet {
	@Override
	protected void doGet(
			HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		response.setContentType("text/html; charset=UTF-8");
		PrintWriter out = response.getWriter();
		out.println("<html><head><title>회원 등록</title></head>");
		out.println("<body><h1>회원 등록</h1>");
		out.println("<form action='add' method='post'>");
		out.println("이름: <input type='text' name='name'><br>");
		out.println("이메일: <input type='text' name='email'><br>");
		out.println("암호: <input type='password' name='password'><br>");
		out.println("<input type='submit' value='추가'>");
		out.println("<input type='reset' value='취소'>");
		out.println("</form>");
		out.println("</body></html>");
	}
	
	@Override
	protected void doPost(
			HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		Connection conn = null;
		PreparedStatement stmt = null;

		try {
			DriverManager.registerDriver(new com.mysql.jdbc.Driver());
			conn = DriverManager.getConnection(
					"jdbc:mysql://localhost/studydb", //JDBC URL
					"study",	// DBMS 사용자 아이디
					"study");	// DBMS 사용자 암호
			stmt = conn.prepareStatement(
					"INSERT INTO MEMBERS(EMAIL,PWD,MNAME,CRE_DATE,MOD_DATE)"
					+ " VALUES (?,?,?,NOW(),NOW())");
			stmt.setString(1, request.getParameter("email"));
			stmt.setString(2, request.getParameter("password"));
			stmt.setString(3, request.getParameter("name"));
			stmt.executeUpdate();
			
			response.setContentType("text/html; charset=UTF-8");
			PrintWriter out = response.getWriter();
			out.println("<html><head><title>회원등록결과</title></head>");
			out.println("<body>");
			out.println("<p>등록 성공입니다!</p>");
			out.println("</body></html>");
			
		} catch (Exception e) {
			throw new ServletException(e);
			
		} finally {
			try {if (stmt != null) stmt.close();} catch(Exception e) {}
			try {if (conn != null) conn.close();} catch(Exception e) {}
		}

	}
}

```
doPost()를 추가하면 아래와 같이 post요청에 대해 결과가 나온다.  
![image](https://user-images.githubusercontent.com/61738600/126267331-914a513f-2c19-4db6-bfe3-3b989153dcc6.png)   
위의 코드를 보면 Statement가 아니라 PreparedStatment를 사용했는데. 이 preparedstatement를 사용해서 ?로 데이터가 들어가야할 자리를 만들고 나중에 stmt.setString(1, request.getParameter("email")); 와 같이 파라미터를 줌으로써 실행할 수 있다.  
한글로 넣으면 list안에 글자가 깨져서 들어가는데 애초에 깨진걸 보내줘서 그런거기 때문에
```java
package spms.servlets;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@SuppressWarnings("serial")
@WebServlet("/member/add")
public class MemberAddServlet extends HttpServlet {
	@Override
	protected void doGet(
			HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		response.setContentType("text/html; charset=UTF-8");
		PrintWriter out = response.getWriter();
		out.println("<html><head><title>회원 등록</title></head>");
		out.println("<body><h1>회원 등록</h1>");
		out.println("<form action='add' method='post'>");
		out.println("이름: <input type='text' name='name'><br>");
		out.println("이메일: <input type='text' name='email'><br>");
		out.println("암호: <input type='password' name='password'><br>");
		out.println("<input type='submit' value='추가'>");
		out.println("<input type='reset' value='취소'>");
		out.println("</form>");
		out.println("</body></html>");
	}
	
	@Override
	protected void doPost(
			HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		request.setCharacterEncoding("UTF-8");
		Connection conn = null;
		PreparedStatement stmt = null;

		try {
			DriverManager.registerDriver(new com.mysql.jdbc.Driver());
			conn = DriverManager.getConnection(
					"jdbc:mysql://localhost/studydb", //JDBC URL
					"study",	// DBMS 사용자 아이디
					"study");	// DBMS 사용자 암호
			stmt = conn.prepareStatement(
					"INSERT INTO MEMBERS(EMAIL,PWD,MNAME,CRE_DATE,MOD_DATE)"
					+ " VALUES (?,?,?,NOW(),NOW())");
			stmt.setString(1, request.getParameter("email"));
			stmt.setString(2, request.getParameter("password"));
			stmt.setString(3, request.getParameter("name"));
			stmt.executeUpdate();
			
			response.setContentType("text/html; charset=UTF-8");
			PrintWriter out = response.getWriter();
			out.println("<html><head><title>회원등록결과</title></head>");
			out.println("<body>");
			out.println("<p>등록 성공입니다!</p>");
			out.println("</body></html>");
			
		} catch (Exception e) {
			throw new ServletException(e);
			
		} finally {
			try {if (stmt != null) stmt.close();} catch(Exception e) {}
			try {if (conn != null) conn.close();} catch(Exception e) {}
		}

	}
}

```
다음과 같이 request.setCharacterEncoding("UTF-8");를 넣어줘서 들어오는 데이터가 UTF-8이라는 것을 명시해주면 된다.  


