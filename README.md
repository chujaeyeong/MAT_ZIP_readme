# [팀프로젝트] 맛.JAVA - 맛.ZIP
#### 💡 맛.ZIP은 “진짜 믿고 먹을 수 있는 맛집” 을 공유하는 플랫폼입니다.
* 맛.JAVA 팀은 맛집 탐방에 누구보다 진심인 사람들이 뭉친 팀입니다. 🍔
* 평소에 모두가 겪고 있던 부정한 광고, 믿을 수 없는 후기 속에서 소비자들이 믿고 방문할 수 있는 맛집을 모아 볼 수 있는 사이트의 필요성을 느꼈습니다.
* 그래서, 영수증 2회 이상 인증된 맛집만 등록되도록 해서 신뢰도 및 만족도가 높은 맛집만 선별하여 소비자에게 제공하는 목적으로 개발을 진행했습니다.
* 국내 운영 중인 맛집 추천 사이트, 대형 포털 지도 사이트의 사례 분석을 통해, 웹사이트 기능의 방향성을 "진정성 있는 맛집 공유"로 초점을 맞췄습니다.
* 맛집을 좋아하는 사람들 뿐만 아니라, 맛집을 좋아하는 사람들의 방문을 원하는 요식업계 사장님들도 타켓팅한 사장님 전용 구독 서비스 및 노출 배너 광고를 BM으로 설정했습니다.


<br>

## 1. 제작 기간
#### `2023년 4월 28일 ~ 6월 9일 (1개월)`

<br>

## 2. 사용 기술
### `Back-end`
* Java 8
* Spring Framework 5.0.1
* Maven
* Mybatis
* MySQL 8.0.32

### `Front-end`
* HTML
* CSS
* JavaScript
* JQuery 3.6.4
* BootStrap 4.1

### `Server`
* tomcat 8.5
* AWS

<br>

## 3. 기능 구현
* #### `회원가입, 로그인, 마이페이지, AI챗봇`
  * (각자 본인의 기능 간략하게 작성바람)

* #### `영수증 등록, 검색기능`
  * (각자 본인의 기능 간략하게 작성바람)

* #### `음식점 상세페이지`
  * (각자 본인의 기능 간략하게 작성바람)

* #### `회원 커뮤니티`
  * 맛집에 관심이 있는 소비자가 이용하는 커뮤니티로, 리뷰 / 사진 / 지유게시판으로 나누어 유저 용도에 따라 세부 메뉴를 분류함.
  * 리뷰게시판은 영수증 등록 여부를 체크하여 영수증 등록을 한 유저만 리뷰를 남길 수 있도록 제약사항을 추가하여 리뷰의 신뢰도를 강화함.

* #### `사장 커뮤니티`
  * (각자 본인의 기능 간략하게 작성바람)

* #### `포인트 시스템, 랭킹 시스템(또슐랭 가이드)`
  * (각자 본인의 기능 간략하게 작성바람)

* #### `캘린더`
  * (각자 본인의 기능 간략하게 작성바람)

<br>

## 4. 핵심 기능 설명 & 트러블 슈팅
#### 1. 포인트 시스템
<details>
  <summary>📌핵심 기능 설명</summary>
	
  ##### `1. OCR을 활용한 영수증 등록 시 포인트 적립`
  * 먼저 OCR을 통한 영수증 등록 로직을 처리하는 DataValidationService에 포인트 적립 로직을 처리하는 PointSaveHistoryService를 @Autowired를 이용해 의존성 주입.
  * (DataValidationService에 있는 로직을 통해 영수증 등록의 가능여부를 판단한 이후 PointSaveHistoryService 로직이 동작하여, 따로 유효성 검사 로직을 사용하지 않았음)
  * PointSaveHistoryService에서는 @Autowired로 PointSaveHistoryDAO를 주입해 메서드 호출.
  * PointSaveHistoryDAO에 주입된 mybatis의 SqlSessionTemplate을 이용해서 pointMapper.xml에 있는 쿼리문을 수행.
  * **‼결과‼** 영수증 등록 시 등록에 성공하면 포인트 적립. DB 테이블에 포인트 내역 저장.
  * [👉이미지로 전체 흐름 확인하기](https://user-images.githubusercontent.com/84839167/161678010-5aac77c5-1f72-4ae2-a74b-af5bed0deb9f.png)

  ##### `2. 네이버 클라우드 SENS API를 활용한 포인트 교환(기프티콘)`
  * 기프티콘 교환 API를 사용하려 했으나 개인의 테스트 용도로 사용이 불가능하여, 네이버 SENS API를 이용해 원하는 상품 선택 시 해당 상품의 이미지를 MMS로 전송해주는 방법사용
    최대한 기존의 기프티콘 교환 방식과 비슷하게 구현.
  * 마이페이지에서 포인트 교환 페이지로 이동 -> 원하는 상품 선택 -> PointExchangeHistoryController에 Service 레이어 호출(세션에 저장된 user_id와 AJAX의 요청 data를 매개변수 전달)
  * Service 레이어에서 보유 포인트를 확인 후 상풍의 가격과 비교해서 보유 포인트가 적을 시 예외처리를 했습니다.
  * 보유 포인트를 확인 후 사용한 포인트를 DB에 저장하고, SENS API를 통해 MMS를 전송하게 됩니다.
  * @Transactional을 통해 예외 발생시 포인트 내역에 저장되기 전으로 롤백하도록 처리했습니다.(root-context에 Exception 설정을 추가해서 모든 예외 발생시 롤백되도록 설정했습니다)
  * **‼결과‼** 보유 포인트가 충분하고, 상품 교환에 성공 시 팝업창을 통해 결과를 알려주고, 회원의 핸드폰번호로 MMS가 전송되게 됩니다.  
  * [👉이미지로 전체 흐름 확인하기](https://user-images.githubusercontent.com/84839167/161677883-4e4976b7-81ee-480f-98e8-ba1563627b0b.png)
	
  ##### `3. 포인트 상세이력 관리`
  * 배민 우아한기술블로그 참고(https://techblog.woowahan.com/2587/) 도메인 로직을 참고해서 설계했습니다.
  * 적립을 할 때 Insert만 존재하는 도메인 모델로 구현하였습니다.
  * 포인트를 사용하고 남은 포인트의 유효기간이 만료되면, 만료된 포인트만 처리해야 하는데 단순한 Insert 모델에서는 처리가 어려워 상세 테이블을 추가하였습니다.
  * 포인트 적립 시 상세 테이블에도 적립 기록이 저장되며, 사용 시 저장된 적립 이력을 큐(Queue)를 이용해서 빠른 시간순으로 정렬된 적립 기록을 불러옵니다.
  * poll을 이용해 List에 저장된 포인트를 상품의 가격과 비교하여 다시 상세 테이블에 저장하고, 상품의 가격이 0원이 되면 종료되는 로직을 구현했습니다.
  * 유효기간만료 이벤트가 발생하면 테이블의 적립아이디를 기준으로 GROUP BY해서 남은 금액을 만료 처리 하면됩니다.
  * 이렇게 하면 기존의 update 로직보다 상세한 이력관리가 가능합니다.
</details>
<details>
  <summary>⚽트러블 슈팅</summary>

<br>
	
  ##### `1. 포인트 교환 예외발생 시 트랜잭션(transaction) 롤백 미작동`
  * 첫 번째 시도 : 아이디 중복 검사 클래스, 비밀번호 일치 검사 클래스를 addValidators() 메서드를 사용해 각각 바인딩 -> ⭕정상작동!  
  * 두 번째 시도 : 두 클래스를 하나의 클래스로 구현해도 될 것 같다는 생각에 JoinCkValidator클래스를 만들어 코드를 합친 후 바인딩할 객체가 하나이기 때문에 setValidator() 메서드로 변경 -> ❌비정상작동
    * 하고자 했던 바인딩을 통한 유효성 검사는 잘 되었지만, 잘 되던 데이터 형식 유효성 검사가 작동하지 않았다.
  * 세 번째 시도 : 객체가 하나이지만 혹시나 하는 마음에 addValidators() 메서드로 다시 변경 -> ⭕정상작동!
<details>
  <summary>👉코드확인</summary>

  <div markdown="1">    

  ```java
	  //첫 번째 코드 - 정상작동
	  @InitBinder
	  public void validator(WebDataBinder binder) {
		  binder.addValidators(IdDuplCkValidator);
	  	  binder.addValidators(passMctCkValidator);
	  }
	  
	  //두 번째 코드 - 비정상작동
	  @InitBinder
	  public void validator(WebDataBinder binder) {
		  binder.setValidator(joinCkValidator);
	  }

	  //세 번째 코드 - 정상작동
	  @InitBinder
	  public void validator(WebDataBinder binder) {
		  binder.addValidators(joinCkValidator);
	  }
  ```
  </div>
</details>
	
객체가 하나인데 setValidator() 메서드가 아닌 addValidators() 메서드를 사용했을 때 정상 작동하는 이유가 무엇일까?  
사실 정확한 이유는 찾지 못했지만, 아래 로그인 시 유효성 검사까지 완료해보니 짐작 가는 부분이 생겼다.  
	
  ##### `2. 로그인 시 유효성 검사 미작동`
  * 첫 번째 시도 : validator가 아닌 Model을 사용해서 아이디, 비밀번호가 존재하지 않으면 메시지를 전송하는 방식을 적용 -> ⭕정상작동!
  * 두 번째 시도 : 로그인도 validator를 적용해보고 싶다는 생각에 아이디, 비밀번호 존재 여부를 검사하는 클래스를 만든 후 회원가입과 똑같이 addValidators() 메서드 사용 -> ❌비정상작동
    * 회원가입 시에 필요한 MemberDto객체의 데이터 형식 검사를 진행하며 에러 발생
  * 세 번째 시도 : setValidator() 메서드로 변경 -> ⭕정상작동!
<details>
  <summary>👉코드확인</summary>

  <div markdown="1">    

  ```java
	  //첫 번째 코드 - 정상작동 (login메서드)
	  if(memberService.login(memberDto) != 1){
		  model.addAttribute("loginFailMsg", "아이디 또는 비밀번호가 올바르지 않습니다.");
		  return "/member/loginForm";
          }
	  
	  //두 번째 코드 - 비정상작동
	  @InitBinder
	  public void validator(WebDataBinder binder) {
		  binder.addValidators(LoginCkValidator);
	  }
	  
	  //세 번째 코드 - 정상작동
	  @InitBinder
	  public void validator(WebDataBinder binder) {
		  binder.setValidator(LoginCkValidator);
	  }
  ```
  </div>
</details>

<br>
	
로그인 시에는 데이터 형식을 검사하길 원하지 않았는데 두 번째 시도에선 데이터 형식을 검사하며 에러가 발생했다.  
문득 setValidator() 메서드가 아닌 addValidators() 메서드를 사용하고 있었다는 사실을 깨닫고, setValidator() 메서드로 수정해주었다.  
이 과정에서 회원가입 두 번째 시도와 같이 데이터 형식 검사를 하지 않는다는 것을 알아냈고,  힌트를 얻을 수 있었다.

위 두 경우를 보면 회원가입 유효성 검사에서는 addValidators()메서드를,  
로그인 유효성 검사에서는 setValidator()메서드를 사용해야 정상작동하는 것을 알 수 있다.  
회원가입도 검사할 객체가 하나인데 왜 addValidators() 메서드만 정상작동하는걸까?  
내가 찾은 답은 `'어노테이션을 통한 데이터 유효성 검사 또한 하나의 유효성 검사 객체(?)로 인식한다.'` 이다.  

`그렇다면?`  
회원가입에서는 ①데이터 형식 유효성 검사와 ②커스텀 유효성 검사 두 개가 이루어지니 **addValidators()메서드를**,  
로그인은 ①커스텀 유효성 검사만 하면 되니 **setValidator()메서드를** 사용하면 된다는 것이 내가 찾은 결론이다.  
또한 검증객체가 하나 이상일 땐 제약조건 어노테이션으로 정의한 데이터 형식 유효성 검사가 우선적으로 이루어진다는 것을 예상할 수 있다.
	
</details>

<br>




#### 04. 회원 커뮤니티
<details>
  <summary>📌 핵심 기능 설명</summary>
	
  ##### `1. 유저의 영수증 등록 여부를 체크한 리뷰 작성 기능`
  * 회원 커뮤니티 내 리뷰 게시판은 유저가 리뷰를 작성하고, 다른 사람들의 리뷰에 댓글을 남길 수 있는 구조의 게시판 페이지로 구현.
	* 리뷰에 신뢰도를 높이기 위해, 유저의 영수증 등록 여부 판단이 필요함.
	* 영수증을 다수의 식당에 등록하고 리뷰를 작성할 때, 리뷰를 남기고 싶은 영수증을 선택하는 form으로 먼저 이동이 필요함.
  * 리뷰게시판 (Review...) 게시물 등록 로직을 처리하기 위해 registerAndSearch 패키지 안에 있는 MZRegisterInfoVO 와 RestaurantVO의 사용이 필요함. 두 model 모두 다른 패키지에 있지만, public 메소드로 작성되어있기 때문에 board 패키지에 동일 model을 만들지 않고 MZRegisterReceiptDTO 만 생성하여 mzRegisterInfoVO와 restaurantVO를 사용할 수 있도록 함.
  * ReviewMapper.xml에서 mzregisterinfo 와 restaurant 테이블을 join 해서 영수증 정보와 식당 정보를 추출하는 getReceiptWithRestaurant 쿼리 작성. (mzregisterinfo 테이블의 storePhoneNumber 컬럼 데이터와 restaurant 테이블의 tel 컬럼 데이터가 일치하는 restaurant 테이블의 name 컬럼 데이터를 상호명으로 추출)
  * 영수증 1장으로 리뷰를 다회 작성을 막기 위해 cs_review 테이블에 receipt_id 컬럼 (mzregisterinfo의 no 컬럼 데이터를 가져와서 저장) 데이터를 제외하고 영수증 정보와 식당 정보를 추출.
  * **‼결과‼** writeReview로 이동하면 getReceiptWithRestaurant 쿼리를 수행하여 영수증의 상호명 + 주소 가 radio form으로 브라우저에 출력, 유저가 리뷰를 작성할 영수증을 선택하고 리뷰 작성 form으로 이동하도록 구현. (영수증을 등록하지 않은 유저가 writeReview 으로 이동하면 alert 창을 보여주고 리뷰게시판으로 리다이렉트됨.)
	
  ##### `2. 리뷰 본문에서 특정 키워드 추출하여 이모티콘 조회 기능`
  * 유저가 리뷰를 작성 후 제출하기 전에, 이모티콘 조회 버튼을 클릭하면 식당 방문 시 참고할 수 있는 주요 키워드 (ex. 주차, 맛, 청결, 가성비 등) 를 검색해서 이모티콘을 출력해주는 기능을 리뷰 작성 form 에 추가.
  * 기존에는 네이버 Sentiment API를 활용하려고 했으나, 긍정/부정 파트를 퍼센트로 판단해주는 기능이라 다양한 키워드를 검색 후 출력이 필요한 지금 상황에는 API가 약간 맞지 않다고 판단하여 MySQL에 키워드와 이모티콘을 저장한 emojiMap 테이블을 DB에 생성하여 키워드를 저장하는 작업을 진행. (형태소 분리가 필요하지만 일단 테스트)
  * ReviewMapper.xml 에 추가하는 쿼리문에서는 emojiMap 데이터 전체 SELECT 쿼리 , Service 계층에서 리뷰 본문과 emojiMap 테이블의 keyward 컬럼 데이터를 비교해서 일치하는 emoji 컬럼 데이터를 추출 후 모델에 저장하는 작업을 수행.
  * **‼결과‼** 리뷰 작성 form (insertReview) 에서 리뷰 본문을 모두 입력 하고, 이모티콘 조회 버튼을 클릭하면 ajax로 리뷰 하단 div에 추출된 이모티콘이 출력되는 방식으로 비동기처리 이모티콘 조회 기능을 구현. 

</details>





