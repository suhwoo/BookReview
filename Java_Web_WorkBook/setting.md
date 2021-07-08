## 1. JDK설치  
www.oracle.com 으로 이동 후, JAVASE 다운로드 및 설치(jdk 16.0.1 설치했습니다)  
설치 후, 환경변수로 등록  
내컴퓨터 -> 속성 -> 고급설정 -> 환경변수 -> 시스템 변수에 JAVA_HOME으로 경로설정(보통 program file-> java에 있음)  
PATH 설정 : PATH에 %JAVA_HOME%\bin 추가.  
잘 설치됐는지 확인 : 
```
$echo %JAVA_HOME%
//결과는 jdk 위치
$java --version
java 16.0.1 2021-04-20
Java(TM) SE Runtime Environment (build 16.0.1+9-24)
Java HotSpot(TM) 64-Bit Server VM (build 16.0.1+9-24, mixed mode, sharing)
$javac --version
javac 16.0.1
```
## 2. Eclipse 설치  
eclipse IDE for JAVA EE developer 설치(이미 설치해놨기 때문에 따로 다운받지 않음)  
c 드라이브에 폴더 javaide만들고 거기에 압축풀기.(설치된 이클립스는 eclipse라는 폴더에 품.)   
workspace는 자바프로그램을 개발하는 동안 프로젝트를 만들 위치. 영상에서는 javaide에 위치.  
  
이클립스에서 jdk 제대로 가리키고 있는지. :  
windows -> perferance -> java -> jdk 잘 설치되어 있는지. ->이때 jre를 가리키고 있으면 안된다 -> add를 통해 jdk폴더를 선택해주고 default로 설정한다.  

general -> workspace ->chacterset->UTF-8  

Web -> CSS,HTML,JSP 모두 UTF-8로 설정. (JSP가 없다면 developer로 설치된것이 아니다.)  
  
시작하기 : file->project->Java project -> JRE는 default JRE  
  
ps) 처음에 시작하면 this kind of project is assoicated with the Java perspective 이런 팝업이 뜬다. 내용은 java 프로젝트를 처음으로 만들었기 때문에 java개발에 맞춰서 이클립스의 각 배치를 바꿔줄까요? 라는 뜻이다.  
  -> yes를 눌러준다.  
    
->src -> new-> class -> default 에 체크.  
## 3.Tomcat 설치 
trouble shouting. 원래 톰캣이 깔려있었지만 localhost:8080을 할때 tomcat이 나타나지 않아 삭제하던중,
```
no service name specified to uninstall
```
이런 에러 팝업을 보았다.  
해결방법은 cmd에서 Tomcat9폴더에 들어가 
```
Uninstall.exe -ServiceName="<Tomcat9>" 
```
이라고 치니 해결되었다.  
www.apache.org에 들어가 tomcat을 선택후 (현재 기준 9.0을 선택했다.)  
core에서 64bit zip을 다운로드 받아주었다.  
이후 압축을 풀고 startup 을 클릭했다. (여전히 글자는 깨진다.)  
http://localhost:8080 을 주소창에 입력했을때 톰캣이 뜨는걸 확인 할수 있다.(만약 실행이 안된다면 이미 8080을 쓰고 있는 프로그램이 있다는 것이다. conf->server.xml->port에서 "8080"으로 설정된 포트 번호를 바꿔주면 된다.)  
**이클립스에 tomcat설정**  
이클립스에서 window->perference->server에서 add눌러서 tomcat 선택, browser에서 tomcat위치 선택.  
java ee로 바꾸고 밑에 창에 server가 나타날거임. (없으면 window에서 추가하기.)  
오른쪽키->new->server로 만들면 tomcat에서 conf애 있던 파일이 그대로 가져와진다.  
server 실행하기 위해서는 서버 위에서 오른쪽 키 -> start  
trouble shouting:  
```
Several ports (8005, 8080) required by Tomcat v9.0 Server at localhost are already in use. The server may already be running in another process, or a system process may be using the port. To start this server you will need to stop the other process or change the port number(s).
```
이런 에러가 뜬다면 이미 그 포트를 이용하고 있는 창이 있다는것. -> 닫아주면 됩니다  
이대로 localhost를 한다면 아예 페이지가 안뜰텐데 project explorer에서 dynamic wed projrct를 만들어준다.   
web project->webcontent에 html만든다.  
서버에서 add and remove -> http://localhost:8080/TestWeb/index.html  
## 4.이클립스에 groovy,gradle설치  
이클립스에서 헬프->마켓 플레이스에서 groovy  
영상에서는 groovy/grail 을 설치했지만 현재 해당 프로그램이 없어서 groovy4.1을 대신 설치했다.  
마찬가지로 마켓플레이스에서 gradle 역시 해당 프로그램이 없어 gradle ide pack 3.8로 대신 설치했다.  
## 5.Mysql 설치  
본인은 비밀번호를 잊어먹어서 삭제후 재설치 했다...  
삭제 방법은   
1. 제어판에서 mysql관련 모두 삭제  
2. programfiles, programfiles x86, prgraom data폴더에서 mysql 모두 삭제  

설치방법은  
1. www.mysql.com 
2. community에 들어가서 server 선택, 운영체제 선택 후 다운로드
3. 강의에서는 custom을 통해 java위주로 선택했지만... 버전이 달라 custom 방법이 달라져서 그냥 default developer 선택함
4. root 비밀번호 정하고, database 사용할 유저 정하고 계속 다음
5. workbrench가 열리면 유저가 사용할 schema를 만들어준다
6. 유저가 database에 접근할 수 있도록 user privillage 선택. 
7. 유저 선택하고 schema privillage선택 
8. add entry선택후 사용할 db선택.
9. 그 db에 대해 어떤 권한을 가질 것인지. =>여기서는 select all 
10. study라는 유저가 들어갈 수 있게 하나 connection만들고 로그인 
11. table만들고 번개표시(run) 돌리면 table이 만들어진다.(session에 table안보이면 오른쪽 클릭후 refresh~)  

## 6.exerd설치  
exerd.com에서 이클립스 플러그인 설치를 선택 후 주소 복사,  
이클립스로 들어가서 help->new software install -> add ->이름(exerd.com), 주소는 아까꺼 붙여넣기 -> Contact~~~ 라고 써진거 체크 취소(다운로드 속도가 느려진다.)->이클립스 리스타트  
## 7.실습소스 가져오기  
https://github.com/eomjinyoung -> repo에서 javawebprogramming  
http 복사하고 이클립스에서 window->show view->other->git->git repo->아까복사했던 http가 있을것이다.  
repo복사한다.  
복사하면 git repo창에 다운된 파일이 나오는데 거기서 lession1을 오른쪽 클릭, import하면 된다.  
touble shouting : 처음 Lession01web을 import받으면 
```
genericServlet cannot be resolved to type
The import javax.servlet cannot be resolved 
```
이런 에러가 떴다.  
해결방법:이클립스에 등록되지 않은 톰캣을 쓴 것이 문제! -> 프로젝트의 properties에서 target runtime -> 톰캣 버전을 현재 사용중인 톰캣 버전으로 바꾸어 apply하면 에러가 사라진다.  
touble shouting : Lession01web을 실행시키면 http://localhost:8080/Lesson01web/calc 가 뜨면서 
```

java.lang.NumberFormatException: null
	java.lang.Integer.parseInt(Integer.java:542)
	java.lang.Integer.parseInt(Integer.java:615)
	lesson01.servlets.CalculatorServlet.service(CalculatorServlet.java:21)
	org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)

```
이런게 뜰텐데... http://localhost:8080/Lesson01web/calculator.html 로 이동하면 해결된다...  
컴파일러, 혹은 톰캣 버전 문제인줄 알고 한참 헤맸다..  

**여기까지가 기본 세팅 끝**








  
