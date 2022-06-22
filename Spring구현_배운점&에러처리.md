int와 Integer

> https://includestdio.tistory.com/1



자바 Optional 바르게 쓰기

> [Java Optional 바르게 쓰기 - 뒤태지존의 끄적거림 (homoefficio.github.io)](https://homoefficio.github.io/2019/10/03/Java-Optional-바르게-쓰기/)



S

[SPRING SECURITY + JWT 회원가입, 로그인 기능 구현 (tistory.com)](https://webfirewood.tistory.com/115)



## (SpringSecurity) antMatchers와 mvcMatchers의 차이

[spring - Difference between antMatcher and mvcMatcher - Stack Overflow](https://stackoverflow.com/questions/50536292/difference-between-antmatcher-and-mvcmatcher)

`antMatchers(String antPattern)` - Allows configuring the `HttpSecurity` to only be invoked when matching the provided ant pattern.

`mvcMatchers(String mvcPattern)` - Allows configuring the `HttpSecurity` to only be invoked when matching the provided Spring MVC pattern.

> 요약
>
> mvcMatcher가 antMatcher보다 더 안전하다. 그 이유는
> antMatchers("/url")은 `/url`에만 매치되고
> mvcMatchers("/url")은 `/url,` `/url/`, `/url.html`, `/url.xyz`에도 매치되기 때문
>
> 또한, mvcMatchers가 더 최근에 나온 API이다





## Unique column의 Exception처리방법

> https://dadadamarine.github.io/java/spring/2019/04/19/spring-sqlException-%EC%B2%98%EB%A6%AC.html



image 파일 보내고 받기

> [Multipart/form-data란? – JungHyun Baek – Developer from South Korea (junghyun100.github.io)](https://junghyun100.github.io/Multipart_form-data/)



## Form-data

>[HTTP multipart/form-data 란? (velog.io)](https://velog.io/@shin6403/HTTP-multipartform-data-란)



## Multipartfile이란?

> A representation of an uploaded file received in a multipart request.



multipartFile 받는 법

multipartFile 변환하기

multipartFile 반환하기

[명월 일지 :: [Java\] Base64 인코딩, 디코딩하는 방법 (tistory.com)](https://nowonbun.tistory.com/476)



@RequestHeader를 받아서 token을 확인하나?



### java에서 Byte[]와 byte[]

[OKKY - 자바 byte와 Byte의 차이점](https://okky.kr/article/408892)

### byte[]의 길이 문제

com.mysql.cj.jdbc.exceptions.MysqlDataTruncation: Data truncation: Data too long for column 'image_byte' at row 1

https://helloworld.kurly.com/blog/jpa-uuid-sapjil/



## Java 8 변경점

> 면접 단골 질문



## Builder Pattern

> 빌더 패턴을 사용해야 하는 이유
>
> https://mangkyu.tistory.com/163
>
> https://lemontia.tistory.com/483
> https://lemontia.tistory.com/445
>
> [[Lombok\] @Builder :: 정리 중... (tistory.com)](https://royleej9.tistory.com/entry/Lombok-Builder)



빌더 패턴의 효과

1) 필요한 데이터만 설정할 수 있음

2) 유연성을 확보할 수 있음

3) 가독성을 높일 수 있음

4) 변경 가능성을 최소화할 수 있음

   > Setter 사용을 지양하자
   >
   > https://github.com/cheese10yun/spring-jpa-best-practices/blob/master/doc/step-06.md



- 예시 클래스

```java
public class UserInfo {
   private String name;
   private int age;
   private String addr;

   public UserInfo(String name, int age, String addr){
       this.name = name;
       this.age = age;
       this.addr = addr;
   }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getAddr() {
        return addr;
    }

    public void setAddr(String addr) {
        this.addr = addr;
    }

    public String getUserInfo(){
       return String.format("name: %s, age: %d, addr: %s", name, age, addr);
    }
}
```

- 빌더 패턴 적용

```java
public class UserInfoBuilder {
    private String name;
    private int age;
    private String addr;

    public UserInfoBuilder setName(String name){
        this.name = name;
        return this;
    }

    public UserInfoBuilder setAge(int age){
        this.age = age;
        return this;
    }

    public UserInfoBuilder setAddr(String addr){
        this.addr = addr;
        return this;
    }

    public UserInfo build(){
        return new UserInfo(name, age, addr);
    }
}

```

builder를 사용한 객체 생성

```java
UserInfoBuilder userInfoBuilder = new UserInfoBuilder();
UserInfo userInfo3 = userInfoBuilder
        .setName("테스터3")
        .setAddr("주소")
        .setAge(26)
        .build();
```



### 롬복의 @Builder

> 해당 클래스에 어노테이션을 붙여주면 된다.
>
> 조건 - 모든 필드가 생성자에 포함되어 있어야한다



### static - 정적 선언 사용

https://coding-factory.tistory.com/524

> Static 키워드를 통해 생성된 정적멤버들은 Heap영역이 아닌 Static영역에 할당됩니다.
> Static 영역에 할당된 메모리는 **모든 객체가 공유하여 하나의 멤버를 어디서든지 참조**할 수 있는 장점을 가지지만 Garbage Collector의 관리 영역 밖에 존재하기에 **Static영역에 있는 멤버들은 프로그램의 종료시까지 메모리가 할당된 채로 존재**하게 됩니다. 그렇기에 Static을 너무 남발하게 되면 만들고자 하는 시스템 성능에 악영향을 줄 수 있습니다.



## AOP사용

https://javachoi.tistory.com/263



## 정규표현식 regax

> [정규 표현식, re 모듈 — Duck9s' (tistory.com)](https://duckgugong.tistory.com/155)
>
> [[ Java \] 자바 정규식 (Regular expressions) (tistory.com)](https://gogo-jjm.tistory.com/63)



## S3 사용해보기





## N+1 문제

[[JPA\] N+1 문제 원인 및 해결방법 알아보기 — 슬기로운 개발생활 (tistory.com)](https://dev-coco.tistory.com/165)



## 즉시로딩 VS 지연로딩

[[JPA\] 즉시로딩과 지연로딩 알아보기(FetchType.EAGER, LAZY) — 슬기로운 개발생활 (tistory.com)](https://dev-coco.tistory.com/139)



## SQL 조인이란?

[조인(SQL Server) - SQL Server | Microsoft Docs](https://docs.microsoft.com/ko-kr/sql/relational-databases/performance/joins?view=sql-server-ver16)

> 조인을 사용하면 **테이블 간의 논리적 관계를 기준으로 둘 이상의 테이블에서 데이터를 검색할 수 있습니다**. 조인은 SQL Server에서 특정 테이블의 데이터를 사용하여 다른 테이블의 행을 선택하는 방법을 나타냅니다.