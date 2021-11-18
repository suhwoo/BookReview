# Annotation  

## 1. Annotation이란 무엇일까 무슨 일을 하는 애일까?  
### 1.1 무엇일까  
프로그램에게 추가적인 정보를 제공해주는 메타데이터(데이터를 설명해주는 데이터)  
### 1.2 무슨일을 할까  
추가적인 정보를 제공해준다 용도는 3가지  
1)컴파일러에게 코드작성문법 에러를 체크하도록 정보를 제공한다.  
ex)메소드가 재정의되었는지 확인할수있다(@Override)내가 작성한 코드가 부모메소드에 있는가?  
2)빌드나 배치시 코드를 자동으로 생성할 수 있도록 정보를 제공한다.  
ex)자바 프로그램을 짜고 압축을 할때가 있는데 어떤 확장명으로 압축을 해라라고 지시할 수 있다.  
3)런타임 시 특정 기능을 실행하도록 정보를 제공한다.  
ex)객체가 애플리케이션 내부에서 해야할 역할을 정의하면 특별한 역할을 할 수 있다.(서블릿, 컨트롤러)  
  
## 2. 타입의 정의과 적용  
### 2.1. 타입정의  
소스파일 생성 : AnnotationName.java  
내용은  
```
public @interface AnnotationName{

}
```
### 2.2. 타입적용  
타입을 정의하고 나서 나중에 개발하는 코드에 적용하려면  
@AnnotationName 이렇게 기술한다.  
```
@Override
public void toString(){}
```
## 3. 엘리먼트 멤버  
### 3.1. 엘리먼트멤버, 값지정  
정의하고 나서 그 안의 내용을 작성해야하는데  
이걸 엘리먼트멤버를 선언한다고 한다.  
어노테이션은 하나 이상의 엘리먼트멤버를 가질 수 있다.  
이 멤버는 어노테이션을 코드에 적용할때 외부의 값을 입력받을 수 있도록 하는 역할  
```
public @interface AnnotationName{
  String elementName();
  int elementName2() default 5;
}
```
어노테이션 적용시 엘리먼트값 지정하는 법  
```
@AnnotationName(elementName="값",elementName2=3);
```
값이라는 이름을 갖고 기능을 수행한다~ 라는 의미  
### 3.2. 기본 엘리먼트 value  
Annotation의 엘리먼트 중에는 기본 엘리먼트라는게 있다.  
value element를 선언하게 되면 어노테이션을 적용할때 엘리먼트 값을 생략하는게 가능하다.  
```
public @interface AnnotationName{
  String value();
}
```
예를 들어 서블릿을 작성할때  
```
@WebServlet("/main")
public class MainServlet{}
```
이렇게 값만 줘도 value의 값으로 들어간다.  
다만 엘리먼트가 두개이상이라면 value도 value=값 현채로 작성해야 한다.    

## 4. 어노테이션 적용 대상 및 유지정책  
### 4.1. 어노테이션 적용 대상  
어노테이션 선언이 끝나면 그 어노테이션을 어디에 적용할지 적용대상을 명시해야한다.  
지정을 하기 위해 사용할 수 있는 상수는 어노테이션 선언할때 지정해야 한다.  
ElementType로 열거상수로 정의되어 있다.  
1)TYPE : 클래스, 인터페이스,열거타입  
2)ANNOTATION_TYPE : 어노테이션  
3)FIELD : 필드  
4)CONSTRUCTOR : 생성자  
5)METHOD : 메소드  
6)LOCAL_VARIABLE : 로컬변수  
7)PACKAGE : 패키지  
  
어노테이션 대상 지정 방법은 @Target을 쓴다.  
```
@Target({ElementType.TYPE,ElementType.FIELD})
public @interface AnnotationName{

}
```
이렇게 어노테이션을 설정하면 어노테이션을 적용할때 아래와 같이 지정할 수 있다.  
```
@AnnotationName // type로 지정했기 때문에
public class ClassName{
  @AnnotationName
  private String fieldName;

  @AnnotationName
  public void methodName(){} // 이건 불가. 메소드를 지정하지 않았으니까
}
```
  
### 4.2. 어노테이션 유지정책  
어노테이션 적용 코드가 어노테이션의 정보를 언제까지 얻을 수 있는지 지정하는것.  
RetentionPolicy 중 하나를 선택하면 된다.  
다음과 같이 Retention안에 선언하면 된다.  
```
@Target({ElementType.TYPE,ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME) // 원래는 value=RetentionPolicy.RUNTIME 이렇게 쓰지만 위에서 언급했듯 기본 element니까 생략이 가능하다.
public @interface AnnotationName{

}
``` 
1)SOURCE : 컴파일러는 컴파일해서 바이트코드가 되면 어노테이션을 모두 지워버린다. 사람이 소스에서 데이터를 얻을 목적으로 어노테이션을 지정할때 쓰인다.  
2)CLASS : 바이트 코드를 만들때까지는 유지를 한다. 어노테이션의 값을 class를 실행시킬때는 얻을 수 없다.  
3)RUNTIME : 마찬가지로 바이트 코드를 만들때까지 유지한다. 실제 프로그램이 실행할때도 어노테이션정보를 읽고 유지할 수 있다. 리플렉션을 사용하려면 유지정책을 RUNTIME으로 설정해야 한다.  
(리플렉션: 런타임에 클래스의 메타정보를 얻는 기능)  
#### 4.2.1 reflection이란  
- reflection이란 프로그램이 실행 중에 자신의 구조와 동작을 검사하고 조사하고 수정하는 것이다.
- java와 같은 객체지향프로그래밍언어에서 reflection을 사용하면 컴파일 타임에 인터페이스, 필드, 메소드의 이름을 알지 못해도 실행중에 클래스, 인터페이스, 필드 및 메소드에 접근 할 수 있다.
- 새로운 객체의 인스턴스화 및 메소드 호출을 허용한다.
- Java와 같은 객체지향프로그래밍언어에서 reflection을 사용하여 멤버접근 가능성 규칙을 무시할 수 있다.예를 들면 private 필드값을 변경할 수 있다던지
- Spring에서 BeanFactory라는 Container에 객체가 호출되면 객체의 인스턴스를 생성하게 되는데 이때 필요하다.
- Annotation자체는 아무런 동작을 가지지않는 단순한 표식이지만 Reflection을 이용하면 Annotatioon의 적용여부와 엘리먼트값을 읽고 처리할 수 있다.
- class에 적용된 annotation정보를 읽으려면 java.lang.class를 이용하고 필드, 생성자, 메소드에 적용된 annotation정보를 읽으려면class의 메소드를 통해 java.lang.reflect 패키지의 배열을 알아야한다.
- Reflection을 이용하면 annotation 지정만으로도 원하는 클래스를 주입할 수 있다.



