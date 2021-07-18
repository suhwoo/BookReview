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



