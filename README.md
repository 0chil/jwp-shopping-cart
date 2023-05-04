# jwp-shopping-cart

## 기능 목록

### 컨트롤러

- 웹페이지
    - 상품 목록 `/`
    - 관리자 `/admin`
    - 설정 `/settings`
- API
    - 추가 `POST /products`
    - 수정 `PUT /products/{id}`
    - 삭제 `DELETE /products/{id}`
    - 상품 요청 페이로드
        - 이름은 필수이다.
        - 이미지는 필수이다.
            - 2000글자 이내이다.
            - URL이어야 한다.
        - 가격은 필수이다.
    - [ ] 장바구니 상품 추가
    - [ ] 장바구니 상품 제거

### 사용자

사용자 도메인 모델을 표현한다.

- 이메일, 패스워드를 담는다.

### 사용자 DAO

- 사용자를 삽입한다.
    - [ ] 사용자가 이미 존재할 경우 예외를 던진다. -> 어디서 처리할지 고민해본다.
- 사용자를 전체 조회한다.

### 상품

- 이름은 20글자 이하이다.
- 금액은 1,000,000원 이하이다.

### 상품 DAO

상품 DB에 대한 저장, 조회, 삭제 로직을 추상화 및 캡슐화한다.

- 상품을 저장한다
- 상품 하나를 조회한다
- 모든 상품을 조회한다
- 상품을 수정한다
    - 수정된 상품 수를 반환한다.
- 상품을 제거한다
    - 제거된 상품 수를 반환한다.

### 상품 Repository

상품 DAO 사용, 예외, 도메인의 저장, 조회, 삭제를 추상화 및 캡슐화한다.

- 상품을 저장한다.
- 상품을 전체 조회한다.
- 상품을 수정한다.
    - 수정된 대상이 없으면 예외를 던진다.
- 상품을 제거한다.
    - 제거된 대상이 없으면 예외를 던진다.

### 장바구니

- 상품을 추가한다.
- 상품을 제거한다. - 상품을 무엇으로 식별해야 하는가
- 상품들을 가져온다.

### 장바구니 서비스

- 장바구니에 상품을 추가한다.
    - 특정 유저의 장바구니를 가져온다.
    - 장바구니에 추가한다.
    - 장바구니를 저장한다.

- 장바구니에서 상품을 제거한다.
    - 특정 유저의 장바구니를 가져온다.
    - 장바구니에서 물품을 제거한다.
    - 장바구니를 저장한다.

- 장바구니를 가져온다.
    - 특정 유저의 장바구니를 가져온다.

### 장바구니 아이템 DAO

- 유저의 장바구니 아이템을 저장한다.
- 유저의 장바구니 아이템을 가져온다.
- 유저의 장바구니 아이템을 비운다.

# 학습 + 복습

## 엔티티 객체에 별도 식별자가 필요한 이유

### 도메인 객체 인스턴스의 생명주기는 데이터의 생명주기와 일치하지 않을 수 있다.

예를 들어, 카트 도메인 객체를 저장했다가 꺼낸다고 해보자.
조회를 통해 다시 도메인 객체를 꺼내면, 해당 객체 및 하위 객체의 인스턴스는 바뀐다.
이 경우 동일한 데이터지만 다른 상품 객체, 다른 카트 객체가 된다.

따라서 객체를 동일성으로 식별하는 경우, 데이터를 올바르게 식별하려면 동일성 외 다른 방법이 필요하다.
하지만, 별도로 정의된 동등성이 있다면 필요하지 않다.
위 이유로 별도의 식별자를 사용하며, 이 식별자의 값은 데이터를 저장하는 측에서 관리한다.

### 결론

(대상이 DB가 아니더라도) 저장 및 조회의 대상이 되는 객체를 식별하기 위해서는 식별자가 따로 필요하다.
(+ 이 식별자를 식별 외 다른 곳에 사용하면 오용이다)
(+ 식별이 필요 없다면 Id도 필요없다)
엔티티는 별도 식별자를 가지는 도메인 객체이고, 그 별도 식별자의 목적은 저장과 조회다.

### 의문점

- 그렇다면 상품의 Id를 식별에 어떻게 활용할 것인가?
    - Id를 상품 객체의 동등성 비교(equals)에 사용해도 되는가?
        - 중복 허용/방지등의 요구사항이 변경되면, 어떻게 대응해야 하는가?
            - 처음에는 장바구니에 중복 상품을 허용했다고 가정해보자.  
            같은 상품이 여러 번 담길 수 있다.  
            하지만 중복에 대해 예외를 던진다는 요구사항이 추가된다면?  
            저장한 장바구니를 다시 구성할 때 예외가 발생한다.
            - 이 때 식별자가 id였다면, 이 요구사항을 반영하기 위해 객체가 아니라 기존 테이블의 데이터를 변경해야 한다.
            - 이 때 식별자가 id가 아니라,
        - Id로 객체를 식별하는 것은 객체의 식별을 외부 저장 기술에 의존하는 것이다.
        - 객체의 동등성이 저장 방식에 따라 달라진다는 문제가 존재한다.
        - DB 테이블의 제약 조건(ex, UNIQUE)이 객체지향 세계까지 영향을 미친다.
            - 그럼 이것을 끊어낼 필요가 있나?
            - 어떻게 끊어낼 수 있나?
    - 아직 저장되지 않은 객체끼리는 어떻게 식별할 것인가? (Id가 null이므로)

- Id를 갖는 것 자체가 객체를 특정 저장 기술(DB)에 종속시키는 걸까? 아니면 DB가 관리하는 Id를 객체에 삽입할 때 종속시키는 걸까?

- Id를 사용하는 기준은 '상태가 완전 동일해도 다른 데이터일 수 있는가?'일 수 있을 것 같다.
  -> 상품끼리 모든 상태가 동일해도, 다른 데이터일 수 있다. -> Id 사용
  -> 모든 상태가 같으면 같은 데이터로 봐야한다. -> Id 사용 X

- 객체 자체를 저장하는 방법은 없나? (완료)
  -> 직렬화/역직렬화. 하지만 이 경우도 메모리 주소가 변하므로 통제 가능한 별도 식별자가 필요하다.
  -> DB에 저장하고, 불러오는 것도 직렬화/역직렬화의 일종인 것 같다. (실제로 BeanPropertySqlParameterSource는 JavaBean 정의를 기반으로 직렬화한다)
  다른 점은 '정보를 원하는 형태로 저장'하도록 하는 것이 목적이라는 점이다. (테이블은 성능, 명확성, 유연성 등을 위해 객체의 형태와 다를 수도 있다)

## 엔티티 객체 EQ & HC 오버라이딩 기준(뇌피셜)

### Id로 비교해야 하는 경우

- **DB가 없다고 가정했을 때, 상품을 동일성(메모리 주소)으로 식별하도록 설계했다면?**
    - 동일한 필드를 가지는 도메인 객체를 허용하고, 동일한 인스턴스를 추가할 수 없게 막는 것이 비즈니스적 결정사항이다.
    - 즉, 메모리 주소로 도메인 객체를 식별했다.
    - 따라서 영속화 맥락을 고려했을 땐 Id로 식별을 이어나가야 한다.
        - Id의 목적은 '데이터 자체'의 식별이기 때문이다.
            - (ex) 테이블이라면 한 행을 식별하는 것이 목적이다)
            - 인스턴스의 생명 주기와 데이터의 생명 주기는 다르다.
            - 영속화 후 인스턴스화 했을 때는 메모리 주소가 달라진다.
            - 따라서 '데이터 자체'를 식별하기 위해서는 Id를 사용해야 한다.

### Id를 빼고 비교해야 하는 경우

- **비즈니스 룰이 별도로 존재하는 경우**
    - 비즈니스 로직 안에서 결정해버리면 잘못된 데이터가 DB까지 내려갈 일이 없다.
        - (정말 불안하다면, DB에다가도 동일한 제약조건을 추가하면 오히려 가장 안전한 것 아닌가?)
    - 이 경우에도 Id로 식별하거나 Id를 포함해 식별한다면, `도메인의 저장 방식`을 도메인이 신경쓰는 꼴이므로,
    - 비즈니스 룰이 있는 경우에 한해서 Id를 도메인의 식별에 활용하는 것은 책임을 훨씬 벗어난 행위다.

> 이렇게 직접 판단을 끝내고 찾아보니 이런 Stackoverflow 답변이 있다.  
> 아쉽지만 누군가와 의견을 나눈 셈 쳐야겠다.  
> 미리 찾아봤으면 시간을 아꼈겠지만, 과연 이렇게 직접 판단해봤을까 싶긴 하다.  
> https://stackoverflow.com/a/4388453

## Service 레이어의 목적, 필요성 (복습)

- 여러 도메인이 연관되는 비즈니스 로직은 수행하는 방법도 간단하지 않으며, 로직 실행 순서마저 비즈니스 로직이다. (절차)
- 따라서 이런 비즈니스 로직을 수행하는 방법에 대한 관심사를 분리하고 캡슐화하기 위해 서비스 레이어가 필요하다. (이것이 도메인 서비스일까요?)

## DAO가 도메인에 의존성을 가지지 않아야 할 이유

간단한 도메인도 Repository가 필요할까?

- [x] UserRepository를 만들지 않는다.
- [ ] User 도메인을 그대로 전달하고, 조회할 때도 완전한 도메인을 만들어 반환해준다.

- 후기
    - (아직 안했습니다!)

## Repository의 목적, 필요성 (복습)

**DAO에 도메인 객체 구성 및 반환, 도메인 객체를 저장할 책임을 부여해본다.**

- 요구 사항
    - CartRepository를 만들지 않는다.

- 후기
    - Cart는 여러 도메인들이 연관된 도메인이므로, 이 도메인을 조회하고 구성하는 것은 간단하지는 않다. (뚝딱 불가능)
    - 따라서 (Cart 도메인을 저장하고, 꺼내는 방법)을 캡슐화해서 관심사를 분리할 필요가 있다.
    - 즉, 여러 도메인을 포함한 도메인이라면 Repository를 사용할 때 유지보수 관점에서 캡슐화의 이점이 크다.