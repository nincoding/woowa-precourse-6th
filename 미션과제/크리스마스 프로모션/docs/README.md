## 🚀 REQUIREMENTS

> 방문 이벤트 주문할인 서비스

### 기능구현 목록

#### 도메인

- [✅] **크리스마스 프로모션 서비스** (도메인)

  - 각 이벤트 모델들을 가진다. (배지, 증정샴페인, 디데이, 메뉴할인, 특별이벤트)
  - [x] **할인 전 총 주문 금액을 계산**한다.
    - 사용자 주문내역을 받고 총 주문금액을 계산한다.
    - menuName마다 가지고 있는 각 prize를 orderCount만큼 곱해 총 주문 금액을 계산한다.
  - [x] **증정 샴페인 개수**반환
    - 총 주문 금액을 가지고 샴페인 이벤트 인스턴스 생성
    - 계산된 증정 샴페인의 개수를 반환한다.
  - [x] **할인 혜택이벤트 조건판별**
    - 할인 전 총 주문 금액이 `10_000 미만`이면 총 혜택내역은 `없음`, 총 할인금액도 `0`원이다.
    - 이벤트 조건에 충족한 경우에만 할인 이벤트를 진행한다.
  - [x] **전체 혜택 내역을 계산**한다. (각 이벤트들의 할인 금액 객체)
    - 각 모델들을 호출해서 각 이벤트 할인금액을 가진 객체를 만든다.
    - 이벤트명은 출력에 필요한 혜택이벤트 명과 일치시킨다.
    ```js
    // ex.
    { '크리스마스 디데이 이벤트': number, '특별 할인': number }
    ```
  - [x] **총 할인 금액을 계산**한다. (= 총 혜택금액)
    - 전체 혜택 내역을 토대로 할인금액들을 모두 더해 총 혜택 금액을 계산한다.
    - 이때 증정된 샴페인의 가격도 계산하여 같이 더한다.
    - `할인 금액의 합계 + 증정 메뉴의 가격(증정 이벤트계산 값)`
  - [x] 할인 후 **예상 결제 금액을 계산**한다.
    - 총 혜택 금액을 전달받는다.
    - `할인 전 총 주문금액 - 할인 금액(총 혜택금액)`
    - 이때 할인 금액에서 증정 샴페인의 가격은 제외한다.
  - [x] 총 할인된 금액으로 **사용자의 이벤트 배지를 반환**한다.

- [✅] **일일 메뉴 할인 이벤트** (모델)

  - 방문날짜와 주문메뉴를 받는다. (input: visitDate, orderMenu)
  - orderMenu는 주문메뉴와 주문개수를 포함하고 있다. (ex. `[{ menuName: orderCount }]` )

  - [x] **방문날짜 평일/주말 체크 기능**
    - 주말(금, 토): `[1, 2, 8, 9, 15, 16, 22, 23, 29, 30]`
    - 평일(일~ 목): 주말을 제외한 모든 날
    - 방문날짜가 평일인지 주말인지 확인한다.
  - [x] 주말에는 **주말 메인메뉴 할인계산**을, 평일에는 **평일 디저트메뉴 할인계산**을 한다.
  - [x] **할인 메뉴 포함 분석** 기능 (= 주말엔 메인메뉴, 평일엔 디저트메뉴가 할인메뉴가 된다.)
    - 주문메뉴에서 할인 메뉴를 포함하고 있는지 확인한다.
  - [x] **할인메뉴 할인 계산 기능** 및 반환
    - 주문메뉴에서 `할인 메뉴`를 포함하고 있으면, 주문개수 1개당 `2_023원 할인`한다.
    - 할인 메뉴를 1개라도 포함하고 있지 않으면 0을 반환한다.
    - 할인된 전체 금액을 반환한다.

- [✅] **크리스마스 디데이 할인 이벤트** (모델)

  - 1일부터 26일 이전 방문 날짜까지 적용되는 이벤트 (1 ~ 26일 이전 방문날짜)
  - 방문날짜를 받는다. (input: number => 크리스마스 할인이벤트 가격: number)
  - [x] **일차별 할인금액이 증가되는 기능**
    - 방문날짜를 기준으로 1일부터(1000원시작) 방문날짜까지(추가되는 1일당 +100원) 할인금액이 증가된다.
    - 방문날짜가 25일 이후라면 0을 반환한다. (추후 출력뷰에서 0이면 '없음'으로 처리한다.)
    - 전체 할인된 크리스마스 할인이벤트 금액을 반환한다.

- [✅] **특별 할인 이벤트** (모델) (별표시된 날만 해당: 3, 10, 17, 24, 25, 31)

  - 방문날짜를 받는다. (input: number => 특별이벤트 가격: number)
  - [x] **방문날짜가 특별할인 날짜와 일치하면 할인**한다.
    - 방문날짜가 별 표시된 날과 일치하면 1_000을 반환한다.
    - 일치하지 않으면 0을 반환한다. (추후 출력뷰에서 0이면 '없음'으로 처리한다.)

- [✅] **샴페인 증정 이벤트** (모델)

  - [x] **증정 샴페인 개수**를 계산한다.
    - 할인 전 총 주문금액을 전달받는다.
    - 120_000으로 나누어 떨어지는 값만큼 샴페인 개수를 반환한다. (input: number => 샴페인: number)
    - 120_000으로 나누어 떨어지는 값이 없는 경우 0을 반환한다. (추후 출력뷰에서 0이면 '없음'으로 처리한다.)
  - [x] **증정 이벤트 가격**을 계산한다.
    - 증정샴페인 개수를 전달받는다. (input: number => 증정이벤트 가격: number)
    - 샴페인 1개당 가격은 25_000이다.
    - 개수당 25_000을 곱한 값을 반환한다. (= 증정이벤트 가격)

- [✅] **배지 이벤트** (모델)

  - 사용자의 배지를 가지고 있다.
  - [x] 총할인 금액을 전달받아 **해당하는 조건의 이벤트 배지를 반환**한다. (총할인금액: number => 배지: string)
    - 5천원 미만: 없음
    - 5천원 이상 : 별
    - 1만원 이상: 트리
    - 2만원 이상: 산타

- [✅] **유효성 검증** 기능
  - [x] **방문 날짜**의 유효성을 검증한다.
    - `유효 범위`: 1 이상 31 이하의 숫자
    - `숫자`인지 검증한다. (유틸함수로 분리시킴)
      - 타입: 하나의 숫자타입만 가능
      - `정수`: 정수만 가능 (소수 불가능)
    - 공통 오류메세지: `[ERROR] 유효하지 않은 날짜입니다. 다시 입력해 주세요.`
  - [x] **주문메뉴**의 유효성을 검증한다.
    - `메뉴 포함여부`: 주문한 모든 메뉴가 메뉴판에 포함되있어야 함.
    - 주문개수 `범위`: 메뉴의 개수는 1 이상의 20이하 숫자만 입력가능
    - 주문개수 `타입`: 숫자인지 검증한다. (유틸함수 재사용)
    - `중복` 검증: 주문한 모든 메뉴가 중복되지 않아야 한다.
    - 공통 오류메세지: `[ERROR] 유효하지 않은 주문입니다. 다시 입력해 주세요.`
    - 주문한 메뉴의 `총 개수를 검증`한다.
      - 20개를 넘어갈 시 error
    - 주문한 메뉴가 `모두 음료인지 검증`
      - 모든 메뉴가 음료라면 error

#### 컨트롤러 및 뷰

- [✅] 컨트롤러

  - [x] 서비스 시작 및 플로우 관리
  - [x] 유효성 검사 및 입력 처리 컨트롤
    - 입력값 예외발생시 에러메세지를 호출하고 재입력 요청을 한다.
  - [x] 핵심 정보 입출력 요청 및 데이터 연결

- [✅] 입력뷰 (사용자 입력을 받는 객체)

  - 사용자 입력 기능 담당
  - [x] **방문날짜**를 입력받는다. (visitDate)
    - `26` 이런식으로 문자열을 입력받으면
    - 숫자타입으로 변환하여 반환한다.
  - [x] 주문할 **메뉴와 개수**를 입력받는다. (orderMenu)
    - `티본스테이크-1,바비큐립-1,초코케이크-2,제로콜라-1` 이런식으로 문자열을 입력받으면
    - `[{ 티본스테이크: 1}, {바비큐립: 1}, {초코케이크: 2}, {제로콜라: 1 }]` 이런 객체배열로 반환한다. (입력값 포맷유틸함수로 분리시킴)

- [✅] 출력뷰 (메세지를 화면에 출력하는 객체)

  - [x] 기본 메세지 출력 기능

    - 서비스 시작문구를 출력한다.
    - 각 에러메세지를 출력한다.
    - 방문날짜와 함께 서비스 혜택 미리보기 메세지를 출력한다.
    - 할인 전 총주문 금액을 출력한다.
    - 증정 메뉴를 출력한다.
    - 혜택 내역을 출력한다.
    - 총혜택 금액을 출력한다.
    - 할인 후 예상 결제 금액을 출력한다.
    - 12월 이벤트 배지를 출력한다.

  - [x] 출력 템플릿 기능
    - 입력받은 주문 메뉴를 줄바꿈하여 개수와 함께 출력한다.
    - 전달받은 금액에 1000원단위마다 (`,`)를 추가한다. (유틸함수로 분리후 사용함)
    - 총혜택 금액이 0원보다 많으면 금액 앞에 `-` 를 추가한다.
    - 혜택 조건에 해당이 안된다면 `없음`을 출력한다.
    - 혜택 내역을 템플릿에 맞춰 출력한다.
    - 고객에게 적용된 혜택 이벤트 내역만 출력한다.
