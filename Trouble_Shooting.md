# 트러블 슈팅

## CICD 작업 이후 프로젝트 명 변경으로 인한 서버 에러 발생

> 2022년 06월 27일

```bash
REPOSITORY=/home/ubuntu/
cd $REPOSITORY

APP_NAME=marketclip
JAR_NAME=$(ls $REPOSITORY/build/libs/ | grep 'SNAPSHOT.jar' | tail -n 1)
JAR_PATH=$REPOSITORY/build/libs/$JAR_NAME

CURRENT_PID=$(pgrep -f $APP_NAME)

echo ">현재 구동 중인 애플리케이션 pid: $CURRENT_PID"

if [ -z $CURRENT_PID ]
then
  echo ">현재 구동 중인 애플리케이션이 없으므로 종료하지 않습니다."
else
  echo "> kill -9 $CURRENT_PID"
  sudo kill -15 $CURRENT_PID
  sleep 5
fi
```

- 프로젝트 명 변경으로 인해 kill 명령어가 실행되지 않아서 일어나는 오류

해결 - 단순히 EC2의 (전 프로젝트명의)프로젝트를 강제적으로 kill하고 재실행 해줬다



## 이메일 인증번호를 생성하는 방법 ( Random String )

> 2022년 06월 29일

1. random 클래스를 사용하여 97~122 사이의 난수를 생성하고 char로 변환한 다음 (a~z) StringBuilder에 하나씩 더해준다 → 힙 메모리(Heap)에 많은 임시 가비지(Garbage)가 생성돼서 사용을 지양했다. (물론 GC(garbage collection)가 처리해주긴 하지만)

```java
// 1. Random을 이용해 
int leftLimit = 97; // letter 'a'
int rightLimit = 122; // letter 'z'
int targetStringLength = 10;
Random random = new Random();
StringBuilder buffer = new StringBuild(targetStringLength);
for (int i = 0; i < targetStringLength; i++) {
    int randomLimitedInt = leftLimit + (int)
            (random.nextFloat() * (rightLimit - leftLimit + 1));
    buffer.append((char) randomLimitedInt);
}
String generatedString = buffer.toString()
System.out.println(generatedString);
```

2. UUID를 생성해 subString으로 잘라쓰는 방법

```java
import java.util.UUID;

	String uuid = UUID.randomUUID().toString();
	String randomString = uuid.substring(0,8);
```

3. ✅ **apache.commons.lang 을 사용하여 랜덤 스트링 생성** ✅ **→** 쉽고 편하게 만들 수 있다 + 길이, 숫자와 문자여부 등 변경이 자유롭다

```java
import org.apache.commons.lang3.RandomStringUtils;

	String randomString = RandomStringUtils.random(8, true, true);
```





## 예외처리

> 2022년 07월 02일
>
> GlobalExceptionHandler를 이용하여 예외 처리를 실행하는데,
> 편의를 위하여 성공한 응답을 GlobalExceptionHandler로 처리하면 어떻게 될까?

> 400 / BAD_REQUEST 예시
>
> ![실패 응답](md-images/TrobleShooting/%EC%8B%A4%ED%8C%A8%20%EC%9D%91%EB%8B%B5.png)



> 200 / OK 예시
>
> ![성공 응답](md-images/TrobleShooting/%EC%84%B1%EA%B3%B5%20%EC%9D%91%EB%8B%B5.png)



> ### 업데이트를 실행할 때, 성공한 응답을 GlobalExceptionHandler로 처리하게 되면 @Transactional이 실행이 되지 않는 문제 발생

- 해결) 성공한 응답은 ResponseEntity를 리턴하게 해결

  > - Exception을 위해 만든 클래스 정리
  > - ResponseCode => enum으로 HttpStatus와 message를 정리한 클래스
  > - CustomException => RuntimeException을 상속받고 ResponseCode를 파라미터로 받는 클래스
  > - HttpResponse => ResponseCode를 파라미터로 받아 ResponseEntity<HttpResponse>로 만들어주는 메서드가 있는 클래스
  > - GlobalExceptionHandler => @RestControllerAdvice를 사용하여 Controller로 throw되는 모든 CustomException과 이외의 Exception을 ResponseEntity<HttpResponse>로 프론트앤드에 전달하는 역할을 한다
  >   @ExceptionHandler를 사용한 메서드로 Exception을 종류별로 처리할 수 있다
  >
  > > 성공 응답시 data를 주기위하여 Object 타입으로 data를 받는 DataResponseCode 클래스를 만들어 HttpResponse의 메서드가 해당 클래스도 파라미터로 받게 설정

  

  - Response 코드만 이용하여 RuntimeException을 상속받지 않는 리턴 타입을`ResponseEntity<HttpResponse>`로 설정하여 해결



## 깃헙액션 CI/CD 에러 이유

> 2022년 07월 02일

>  [application.properties](http://application.properties) 생성 불가
>
> ![깃헙액션에러](md-images/TrobleShooting/%EA%B9%83%ED%97%99%EC%95%A1%EC%85%98%EC%97%90%EB%9F%AC.png)

이유 - .gitignore로 applications.properties를 제외하고 프로젝트를 올릴 때

./src/main/resources 의 경로에 있는 파일이 applications.properties하나이기 때문에

디렉토리들도 제외하고 올려진다 (파일이 없는 빈 디렉토리들은 무시됨)

따라서 main.yml에 작성한 cd ./src/main/resources 에서 에러가 생긴다

main.yml

```html
- name: application.properties 생성
        # branch가 main일 때
        if: true
        run: |
          # spring의 resources 경로로 이동
          cd ./src/main/resources
          touch ./application.properties
          # GitHub-Actions에서 설정한 값을 application.properties 파일에 쓰기
          echo "${{ secrets.APPLICATION_PROPERTIES }}" > ./application.properties
```

해결 - resources 디렉토리에 일단 applications.dev.properties를 추가하여 해결했지만, mvp를 구현하고 난 이후 applications.properties를 ignore하지 않고, 중요한 정보는 다른 곳에서 import받아 사용하는 방식으로 변경할 예정



## 성공 응답이 다양할 필요가 있을까

> 2022년 07월 06일

> 하나의 api요청에서 Exception은 몇 가지, 또는 몇 십 가지까지도 생길 수 있다
> 그렇기에, 빠른 Exception의 처리를 위해서 실패 응답에는
> 가능한 모든 Exception의 이유가 기술되어 있어야 한다고 생각한다.
>
> 하지만 api의 성공 응답은 거의 하나(1:1 관계)이기 때문에,
> 굳이 다양한 성공 응답을 할 필요가 없다 (시간 자원이 낭비됨)

![Success_Response_RIP](md-images/TrobleShooting/Success_Response_RIP.png)

> 특별히 이메일 인증의 성공 응답은, 하나의 api에서 두 개의 성공 응답을 주기 때문에 살아남았다
> RIP. SuccessResponses  ㅜㅜ

![Success_Response_Now](md-images/TrobleShooting/Success_Response_Now.png)



## S3 서버와 Files 테이블

> 2022년 07월 07일

> 백엔드 팀원 중 한 분이 사정이 있으셔서 하차하게 되었다
> 그분이 맡으셧던 게시판 기능(물품 관리 - Goods 테이블)을 대신 구현하게 되었는데,
> 일단 파일을 여러개 받아야 하는데 하나만 받게 처리하셔서
> Files 테이블을 만들어 Goods 테이블과 일대다 양방향 연관관계를 맺어주었다
>
> 기능들을 구현한 후 게시판 생성은 잘 작동하는데,
> 게시판 삭제 기능에서 게시판에 포함된 파일들이 S3 서버에서 지워지지 않는것을 확인했다
>
> S3 파일의 삭제에 사용하는 DeleteObjectRequest 클래스에서 bucketName과 key를 파라미터로
> 받는것을 확인하고 fileUrl이 아닌 key인 것에 힌트를 얻어
> S3 파일의 생성에 쓰였던 PutObjectRequest에서의 key가 그대로 쓰인다는 것을 알았다
>
> 애석하게도 Files 테이블에선 url만을 값으로 받고 있었고, S3Service와 GoodsService에서도 url만을 다뤘다
>
> ![S3_upload_old](md-images/S3_upload_old.png)
>
> <center>S3Service에서의 return값 `url` => bucketName+".s3.ap-northeast-2.amazonaws.com"+fileName
>
> ![Goods_Service_old](md-images/Goods_Service_old.png)
>
> <center>GoodsService에서 goods객체와 fileUrl만을 Files테이블에 저장했다
>
> ![Files_old](md-images/Files_old.png)
>
> <center>Files 테이블의 컬럼들
>
> 
>
> ## 해결
>
> 해당 url을 분석하여 fileName만을 뽑아내도 되지만, 
> S3Service에서 값을 나눠서 Map 타입으로 주는 방식을 택했다
>
> ![S3_upload_now](md-images/S3_upload_now.png)
>
> <center>변경한 S3Service => Map 타입으로 각 값들을 전달한다
>
> ![Goods_Service_now](md-images/Goods_Service_now.png)
>
> <center>GoodsService에서 bucket, region, fileName을 각각 입력받는다
>
> ![Files_now](md-images/Files_now.png)
>
> <center>bucket, region, fileName을 각각 입력받아도 fileUrl은 자동으로 구성된다
>
> **이로써 바꾼 코드에선 S3에서 파일을 삭제하거나 수정할 때**
> **Files.getFileName()으로 key값을 가져올 수 있다**



### ※수정

> 2022년 07월 09일
>
> 추가적인 구현을 하다보니 fileName보다 url을 사용 할 일이 많다는 것을 알게 되었다
> (fileName은 delete할 때 밖에 사용할 일이 없었다)
> 특히 게시했던 사진/동영상 파일들을 게시글을 수정할 때, 
> 프론트엔드에게 url을 받을 일이 많아져서 다시 한 번 로직을 수정하였다
>
> ![S3_delete](md-images/S3_delete.png)
>
> <center>삭제 로직에서만 fileName(key)이 필요하므로 삭제시 url에서 fileName을 추출한다
>
> 파일 테이블은 원래대로 fileUrl값만을 가지게 되었고, 서비스들도 url만을 이용하게 롤백하였다



## 게시판 수정하기

> 2022년 07월 08일 ~ 2022년 07월 10일

> 핵심 구현 => (사진+동영상)이미지 파일 update - 순서 있음 / S3 서버 부하 최소화
>
> 게시판 CRUD에서 Update가 Create와 다른 점은,
> Create는 모든 이미지를 파일(MultipartFile)로 보내는데
> S3 서버를 이용하여 이미지 파일을
> Update시에는 기존의 이미지를 url로, 새로운 이미지는 파일로 보내게 된다
>
> 이러한 로직을 짜게 된 데에는
>
> 프론트에서 imageURL을 이미지 파일로 변환해서 주는 경우
>
> 1. 백엔드에서 이미지 파일을 받아 다시 url로 변환 시켜야 함
> 2. 변환된 url로 기존의 url을 추적할 수 없음 (Unique를 위해서 UUID를 사용했기 때문)
>    만약 같은 이미지 파일로 동일한 url을 얻을 수 있게 한다면, Unique 성질을 위협받게 될 것 
> 3. 따라서 S3서버에 저장된 기존의 url들을 모두 삭제하고, 모두 다시 저장을 해야한다
>
> 이러한 과정을 거치게 된다면 S3서버 및 FE, BE에 부담이 커질 것이다
>
> 
>
> ![GoodsReqDTO](md-images/GoodsReqDTO.png)
>
> MultipartFile과 url을 모두 받기 위해 `List<Object>`로 데이터들을 수신하는 DTO를 작성하였다
>
> 여기서 생기는 문제는 아직도 명확한 답을 찾지 못하였는데, 프론트에서 api를 호출할 때 url의 리스트 `List<String>` 또는 이미지 파일의 리스트 `List<MultipartFile>`로 보낼때는 `List<Object>`가 잘 작동을 하여 정상적인 로직이 실행되지만,
>
> ```
> 프론트에서 spi를 호출할 때 url과 MultipartFile을 동시에 보내게 된다면 url의 값들을 받지 못하고, MultipartFile의 값만을 리스트로 받는 현상이 있었다
> ```
>
> 