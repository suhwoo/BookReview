## 웹 어플리케이션은 왜 존재하는가??  
**1.1 웹 어플리케이션**  
데스크탑 어플리케이션의 문제가 무엇인가!  
pc에서 실행되는 것이 데스크탑 어플리케이션 : pc에 설치한 뒤 실행한다. 사용자 화면 출력, 업무관련 작업 로직, 데이터 처리도 모두 pc에서 처리한다.  
문제는 프로그램 변경이 일어나면 다시 설치해야한다->이걸 수많은 pc에 설치해야 한다면!! 너무 번거로워 진다. 또 db정보 등에 대한 접근이 쉬워서 보안에 취약하다  
**1.2 클라이언트,서버 어플리케이션**  
핵심은 데스크탑 어플리케이션을 서버와 클라이언트로 나누는 것이다.  
서버:비지니스로직, 데이터 로직  
클라이언트:프리젠테이션 로직  
이점:만약 비지니스나 데이터에 변경이 생겨도 서버만 바꾸면 된다. 클라이언트에서는 재설치 횟수를 줄일 수 있다. 보안측면에서는 서버에서 데이터베이스를 접속하기 때문에 해킹을 줄일 수 있다.   
🥕trouble shouting: 서버를 실행했는데
```
Address already in use: JVM_Bind 오류 
```
가 나온다면 현재 포트를 이미 무언가가 사용하고 있는것.  
cmd -> netstat -o -a ->써야하는 포트번호의 PID 찾기.->작업관리자->프로세스->해당 PID의 프로세스 끝내기.  
(본인은 작업관리자의 버전이 달라서인지 세부정보로 들어가야 PID를 확인할 수 있었다.)  
클라이언트, 서버 어플리케이션의 문제점은 다중 클라이언트 접속이 불가하다는 것.  
->1.3에서 해결 할 수 있다.  
**1.3 다중 클라이언트 요청처리**  
또 다른 클라이언트는 대기상태로 있게 된다  
-> thread 프로그램을 해야한다.  
서버를 요청하면 thread가 생성되고 계산을 수행한다. 
다중 클라이언트 요청을 병행 처리 -> 동시 작업 가능  
단 단점은 스레드를 써야해서 프로그래밍이 복잡하다. ->이걸 안하는 방법이 있다!  
**1.4 클라이언트 서버 아키텍처의 진화**(1.5 먼저 보고 오라고 합니다..전 1.4 먼저 봄..)  
90년대는 서버에 DBMS를 두고 클라이언트는 비지니스 로직과 UI를 담당했다.  
서버에서 데이터를 처리하니 자료의 중복과 불일치는 해결했지만 문제점은 어플리케이션 변경시 재배포 해야하고 클라이언트에서 DBMS에 접속하므로 보안문제가 있었다.  
  
이후 비지니스로직을 server단으로 보냈다.  
클라이언트는 어플리케이션 서버(비지니스 로직을 수행)에 접속하고 어플리케이션 서버는 DBMS와 상호작용하였다.  
  
웹기술을 적용하면  
웹서버가 server에 추가된다. 웹서버는 어블리케이션 서버와 상호작용하고 어플리케이션 서버는 DBMS와 상호작용한다.  
웹서버는 http 프로토콜을 통해 웹 브라우저와 상호작용한다.  
클리이언트 애플리케이션을 따로 배포할 필요가 없고 socket, multi thread에서 탈출!  
이기종 시스템간의 매끈한 연결 지원  
👍서버쪽에서 UI생성한다.  
**1.5 웹 어플리케이션 아키텍쳐의 특징**  
다중 클라이언트에서는 서버는 소켓 프로그래밍, 스레드 프로그래밍을 해야하고, 클라이언트에서는 소캣 트로그래밍을 해야했다.  
웹서버를 통해 이 일을 위임시킬 수 있다.  
웹 브라우저에서 웹서버에 요청을 보내면 웹서버는 실제 계산기 서버에게 위임한다. 웹서버는 그 결과를 http프로토콜에 따라서 웹 브라우저에 전달한다.  
즉, 웹서버는 다중 클라이언트 접속을 처리한다.  
계산기 서버는 비지니스 로직을 하고 HTML UI를 생성한다. 이 계산기 서버를 애플리케이션 서버라고 한다.  





