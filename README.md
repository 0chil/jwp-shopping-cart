# jwp-shopping-cart

## 기능 목록

### 컨트롤러

- 웹페이지
    - 상품 목록 `/`
    - 관리자 `/admin`
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

### 상품

- [ ] 이름은 20글자 이하이다.
- [ ] 금액은 1,000,000원 이하이다.

### 상품 DAO

상품 DB에 대한 저장, 조회, 삭제 로직을 추상화 및 캡슐화한다.

- 상품을 저장한다
- 상품 하나를 조회한다
- 모든 상품을 조회한다
- 상품을 수정한다
  - [ ] 영향받은 행 수를 반환한다.
- 상품을 제거한다
  - [ ] 영향받은 행 수를 반환한다.

### 상품 Repository

상품 DAO 사용, 예외, 도메인의 저장, 조회, 삭제를 추상화 및 캡슐화한다.

- 상품을 저장한다.
- 상품을 전체 조회한다.
- 상품을 수정한다.
  - [ ] 대상이 없으면 예외를 던진다.
- 상품을 제거한다.
  - [ ] 대상이 없으면 예외를 던진다.