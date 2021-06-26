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

  
