## 1.Maven이란  
### 1.1. Maven이란  
자바 프로젝트의 빌드를 자동화해주는 빌드 툴이다.  
기존에는 Apache Ant가 많이 쓰였지만 스크립트 작성도 많고 라이브러리 의존관리가 되지 않기 때문에 Maven이 대체하게 되었다.  
  
### 1.2. Maven의 특징  
1.2.1.  pom을 통해 사용해야 할 라이브러리 뿐만 아니라 해당 라이브러리 작동에 필요한 라이브러리들까지 자동으로 다운받아준다.  
  
1.2.2. 중앙저장소를 통한 자동 의존성 관리(아파치 재단에서 운영한다)를 한다. 중앙 저장소는 라이브러리를 공유하는 파일 서버이고 각 회사만의 중앙 저장소를 구축할 수도 있다.  
  
1.2.3. 정해진 라이프 사이클에 의하여 작업을 수행하며 전반적인 프로젝트 관리기능을 포함한다.  
  
### 1.3. build tool  
빌드와 관련된 설정을 할 수 있는 곳이다.  
메이븐은 프레임워크이기 때문에 동작 방식이 정해져있고 미리 정의하고있는 빌드 순서가 있다.  
이를 라이프사이클 이라고 한다.  
크게 default, clean, site로 나뉘고 세부적으로 phase로 나뉜다.  
![image](https://user-images.githubusercontent.com/61738600/140637667-f5db1706-f392-455e-96fd-5ab173a96d19.png)  
모든 기능은 플러그인을 기반으로 동작하고   
플러그인에서 실행할 수 있는 각각의 작업을 골 이라고 한다.   
하나의 phase에는 하나의 골과 연결되어 있으며 하나의 플러그인에는 여러개의 골이 있을 수 있다.  
최종 빌드의 순서는 compile->test→package 순서이다.  
  
(출처 : https://jeong-pro.tistory.com/168)  
  
## 2. Maven과 Gradle의 차이   
2.1. Gradle의 경우 Groovy 언어를 사용한 Domain-specific-language를 사용한다.  
Maven의 경우, xml파일을 설정 파일로 사용하는데 동적인 요소를 xml로 정의하기엔 어려운 부분이 많기 때문에 설정내용이 길어지는 반면, Gradle의 경우, Groovy 스크립트로 플러그인을 호출하기 때문에 가독성이 높고 코드가 간결하다.  
2.2. Gradle의 빌드시간이 Maven보다 짧다.  
Gradle의 경우, 프로젝트의 어느부분이 업데이트 되었는지 알 수 있고 업데이트가 이미 반영된 빌드부분은 더이상 실행하지 않는다.  
이와 같은 차이가 생기는 이유는 Maven은 선형적인 단계의 모델을 기반으로 하는 반면 Gradle은 작업 의존성 그래프를 기반으로 하기 때문이다. task의 업데이트를 체크하고 점진적인 build를 허용하기 때문에 빌드 시간이 단축된다.  
2.3. Maven의 경우 멀티 프로젝트에서 특정 설정을 다른 모듈에서 사용하려면 상속받아야 하지만 Gradle은 설정 주입 방식을 제공한다.  
2.4. Gradle은 concurrent에 안전한 캐시를 허용한다.  
2개 이상의 프로젝트에서 동일한 캐시를 사용할 경우, 서로 overwrite되지 않도록 checksum 기반의 캐시를 사용하고 캐시를 repository와 동기화시킬 수 있다.  
## 3. pom.xml이란  
pom.xml은 build파일로  
Maven은 pom.xml을 통해 build 정보를 기술한다.  
Maven은 라이브러리, 프레임워크 등을 모두 관리해준다는 이점이 있다. 이러한 프로젝트 관리를 빌드 파일에서 해준다.  
  
pom은 project object model의 약자로 프로젝트의 다양한 정보를 처리하기 위한 객체 모델이다.  
web의 pom.xml파일을 통해 어떤 속성을 가지고 있는지 논의해보자  
  
```xml
<modelVersion>4.0.0</modelVersion> // pom 모델의 버전

<groupId>com.ahnlab.epp</groupId> // 그룹 ID 제작사나 회사를 식별
<artifactId>web-base</artifactId> // 프로젝트에 할당된 고유의 ID
<version>0.0.1</version> // 프로그램 버전
<packaging>pom</packaging> // 패키지 종류 (jar이나 zip같은거)

<name>web-base</name> // 프로그램의 이름
<url>http://www.ahnlab.com/epp</url> // 프로젝트와 관련된 web사이트의 주소

<properties>...</properties> // pom.xml에서 사용하는 속성 값 <version.jdk>1.8</version.jdk> 이런 버전이 적혀있다.

<dependencies> //의존 라이브러리의 정보를 기술
<dependency> // 정보기술 및 라이브러리 지정
<groupId>org.projectlombok</groupId> // 어떤 라이브러리를 쓰는 지
<artifactId>lombok</artifactId>
<scope>provided</scope> //라이브러리가 이용되는 범위, 어떤 때 사용하는지
</dependency>
<dependency> // 단위테스트를 위한 기능을 제공하는 라이브러리
<groupId>junit</groupId>
<artifactId>junit</artifactId>
</dependency>
...
</dependencies>

```
### build 구성  
pom 파일 내에서 여러 플러그인들의 실행에 관련된 설정들을 지정한다.  
build미트올 다수의 플러그인들을 설정할 수 있다.  
```xml
<build>
<pluginManagement>
<plugins>
<!--This plugin's configuration is used to store Eclipse m2e settings
only. It has no influence on the Maven build itself. -->
<plugin>
```
### plugins 구성  
아래와 같이 구성되어 있다.  
groupId와 artifactId,version을 가진다.  
실행시 옵션인 configuration과 언제 실행될지에 대한 설정인 execution으로 구별된다.  
```xml
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-pmd-plugin</artifactId>
<dependencies>
<dependency>
<groupId>net.sourceforge.pmd</groupId>
<artifactId>pmd-core</artifactId>
<version>${version.plugin.dependency.pmd}</version>
</dependency>
<dependency>
<groupId>net.sourceforge.pmd</groupId>
<artifactId>pmd-java</artifactId>
<version>${version.plugin.dependency.pmd}</version>
</dependency>
<dependency>
<groupId>com.ahnlab.epp.build</groupId>
<artifactId>epp-pmd-java-rules</artifactId>
<version>0.0.1</version>
</dependency>
</dependencies>
<executions>
<execution>//언제 실행될지
<id>pmd</id>
<phase>process-sources</phase>
<goals>
<goal>check</goal>
<goal>cpd-check</goal>
</goals>
<configuration> // 실행시 옵션
<targetJdk>${version.jdk}</targetJdk>
<printFailingErrors>true</printFailingErrors>
<sourceEncoding>utf-8</sourceEncoding>
<verbose>true</verbose>
<rulesets>
<ruleset>rulesets/java/epp-pmd-java-rule.xml</ruleset>
</rulesets>
</configuration>
</execution>
</executions>
</plugin>
```
### configuration  
플러그인의 goal이 실행될때 실행옵션을 설정한다.  
플러그인의 바로 아래에 있는 configuration은 모든 goal수행시 기본옵션이고  
execution 하위에 있는 configuration은 해당 execution에만 유효하다.  
```xml
<execution>//언제 실행될지
<configuration> // 실행시 옵션
<targetJdk>${version.jdk}</targetJdk>
<printFailingErrors>true</printFailingErrors>
<sourceEncoding>utf-8</sourceEncoding>
<verbose>true</verbose>
<rulesets>
<ruleset>rulesets/java/epp-pmd-java-rule.xml</ruleset>
</rulesets>
</configuration>
</execution>
```
### execution  
해당 plugin이 언제, 어떤 configuration으로 실행될지에 대한 기술이다.  
```xml
<execution>//언제 실행될지
<id>pmd</id>//해당 execution에 대한 식별자이다.
//왜냐하면 goal phase에 default로 바인딩될 수도 있고 다른 execution에서 실행될 수도 있으므로 구분이 필요하다.
<phase>process-sources</phase>//goal을 어느 phase에 바인딩할지
<goals>//플러그인이 어떤 goal을 실행할 것인지 정한다.
<goal>check</goal>
<goal>cpd-check</goal>
</goals>
<configuration>
<targetJdk>${version.jdk}</targetJdk>
<printFailingErrors>true</printFailingErrors>
<sourceEncoding>utf-8</sourceEncoding>
<verbose>true</verbose>
<rulesets>
<ruleset>rulesets/java/epp-pmd-java-rule.xml</ruleset>
</rulesets>
</configuration>
</execution>
```

