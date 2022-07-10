# 1장 소개

## 왜 타입스크립트를 사용하는가?

1. 실수를 방지한다.
2. 자신과 미래의 개발를 위한 문서화를 제공한다.
3. 리팩토링을 쉽게 만든다.
4. 단위 테스트의 숫자를 반으로 줄인다.

   ⇒ 프로그래머의 생산성을 배로 늘려준다.

## ‘안전한’은 무슨 의미인가?

- 타입 안정성(Type Safety)을 의미한다.
  → **타입을 이용**해 프로그램이 유효하지 않은 작업을 수행하지 않도록 한다.
  - **유효하지 않은 동작의 예시**
    1. 숫자와 리스트 곱하기
    2. 객체 리스트만 받는 함수에 문자열 리스트를 인수로 전달하기
    3. 객체에 존재하지 않는 멤버 함수로 호출하기
    4. 최근에 다른 곳으로 이동 된 모듈 임포트하기

자바스크립트를 포함한 일부 언어는 **유효하지 않은 코드**(실수가 있는 코드) 도 실행을 시도한다.

⇒ 하지만, 이런 실행은 버그를 찾는게 어려움을 준다.

---

## 예시

위에서 제시한 유효하지 않은 코드 작성해보자.

```js
const case1 = 3 + [];
const case2 = {}.foo;
const case3 = (n) => n / 2;
const case4 = async () => await import("./somewhere");
```

![유효하지 않은 코드 js](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F232ae93d-931e-49b3-bcbd-1c85f377f9b7%2FScreen_Shot_2022-07-09_at_9.31.29_PM.png?table=block&id=56d0e80e-c797-4ecd-b20c-b2b7f7496c52&spaceId=ce3d1ab0-bd12-4299-b2bc-a48d939864d3&width=2000&userId=24eac0f7-2d07-4a1d-8713-0657ab34040f&cache=v2)

자바스크립트는 다음과 같이 유효하지 않은 코드의 실행을 시도한다. 이 기능으로 인해 실수를 인지하는 시점 실행 시점이 된다.

<aside>

> ⭐ 타입스크립트의 훌룡한 기능은 **에러를 알려주는 시점**에 있다.

</aside>

**자바스크립트**는 프로그램을 실행할 때(컴파일을 할 때), 개발자의 실수를 인지시킨다.

**타입스크립트**는 편집기에 코드를 입력하는 순간 에러를 발생시킨다.

위의 코드를 타입스크립트로 작성하면 다음과 같은 경고를 확인 할 수 있다.

![유효하지 않은 코드 ts](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe86976fe-5d6b-41e4-b3a5-7b1b2b8aec3e%2FScreen_Shot_2022-07-09_at_9.38.23_PM.png?table=block&id=a191e20c-71ee-477b-870d-a6443899afa5&spaceId=ce3d1ab0-bd12-4299-b2bc-a48d939864d3&width=2000&userId=24eac0f7-2d07-4a1d-8713-0657ab34040f&cache=v2)
