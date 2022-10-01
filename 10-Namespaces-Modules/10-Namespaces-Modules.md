# 10. Namespaces.Modules

### 1. Module 해석 방법

1. **컴파일러(TSC)가 모듈을 해석하는 방법**
2. 빌드시스템(Webpack, Gulp)이 모듈을 해석하는 방법
3. 모듈이 실제로 런타임에 응용프로그램으로 로드(<script />태그, SystemJs 등)되는 방법

### 2. JavaScript 모듈의 역사

1. 모듈 시스템을 지원하지 않음 (~1995)
    - 단점
        - 전역 네임스페이스에 정의 → 확장이 어려움
        - 사용할 수 있는 변수명 고갈 → 같은 변수명으로 인한 충돌 발생
        - 외부에서 어떤 기능을 사용할 수 있고, 어떤 부분은 사용하면 안되는지 구별 어려움
    - 해결방안
        - 객체 이용
            
            ```tsx
            // 객체 이용 
            window.memberModule = {
            	memberList: [],
            	getMemberList() { return this.memberList },
            	setMemberList(member: Member) { this.memberList.push(member); }
            }
            
            memberModule.setMemberList({ name: 'soo', age: 30 });
            console.log(memberModule.memberList); // [{name: 'soo', age: 30}]
            ```
            
        - 즉시 실행 함수 사용
            
            ```jsx
            const memberMoudle = (function () {
              const memberList = []; 
              return {
                getMemberList: () => {
                  return memberList;
                },
                setMemberList: (member) => {
                  memberList.push(member);
                },
              };
            })();
            
            memberMoudle.setMemberList({ name: 'soo', age: 30 });
            console.log(memberMoudle.getMemberList()); // [{name: 'soo', age: 30}]
            memberMoudle.setMemberList({ name: 'jin', age: 30 });
            console.log(memberMoudle.getMemberList()); // [{name: 'soo', age: 30}, {name: 'jin', age: 30}]
            ```
            
        
    - 문제점
        - 자바스크립트 코드 → 브라우저 렌더링의 블로킹 요소: 즉, 응용 프로그램이 커질수록 첫 렌더링 속도 느려짐
        
2. 페이지를 한번에 로드하는 대신, 필요한 파일만 동적으로 로드하는 방식 채택(2004~2009)
    - 특징: 첫 페이지가 로딩된 다음 자바스크립트를 게으르게(그리고 비동기로) 로딩하는 모듈 로더 제공
    - 비동기적으로 모듈을 로딩한다는 뜻
        1. 모듈은 잘 캡슐화되어야 한다.
        2. 모듈 간의 의존성은 명시적이다.
        3. 모든 모듈은 앱 내에서 고유 식별자를 가져야 한다.
        
3. NodeJS의 개발(2009)
    - 특징: 모듈 시스템을 플랫폼 자체에 추가
    
4. Browserify(2011)의 출시: `require('modules')  in the browser`
    - 특징: 프론트엔드 엔지니어도 CommonJS 사용 가능
        
        ```jsx
        export const test = () => {
            return 'hi';
        }
        ```
        
        ```jsx
        "use strict";
        Object.defineProperty(exports, "__esModule", { value: true });
        // defineProperty(obj: 정의할 object명, prop: ojbect 프로퍼티 , descriptor)
        // console.log(exports.__esModule) // output: true
        exports.test = void 0; // void 연산자로 undefined를 반환한다 (undefined 값을 주기 위해 사용)
        const test = () => 
            return 'hi';
        };
        exports.test = test;
        ```
        
    - CommonJS 모듈 방식의 문제점
        - require 호출은 반드시 동기 방식이어야 한다
        - CommonJS 모듈 해석 알고리즘이 웹에 적합하지 않다
        - **정적 분석이 불가능하다 → ?? 아마도 Object.defineProperty 메서드를 통해 동적으로 프로퍼티가 추가되니까 런타임 때 분석이 가능하므로 정적 분석이 불가능하다고 한게 아닐까 싶음**
        
5. ES2015 개정판 출시
    - 특징: 정적 분석이 가능한 새로운 표준 import/export 소개
    - 문제점
        - 표준을 지원하지 않는 자바스크립트 런타임도 있었으므로 환경에 맞는 타입으로 컴파일 필요
        
6. 타입스크립트의 탄생
    - 특징: 모듈의 코드를 소비하고 익스포트할 수 있는 여러 방식 추가
        - 전역 선언, 표준 ES2015의 import와 export, 과거 호환성을 제공하는 CommonJS 모듈의 import 등
        

### 3. Import, Export

- **정적 임포트 vs 동적 임포트**
    
    
    |  | 정적 임포트 | 동적 임포트 |
    | --- | --- | --- |
    | 장점 | 안정성  | - 코드를 분할하여 병렬로 로딩할 수 있다. (응용 프로그램 최적화 가능)
    - 필요할 때만 코드를 로딩할 수 있음 |
    | 단점 | 코드의 양이 많아질수록 첫 렌더링 시간이 점점 길어진다 | 안정성의 이슈  |
    - 정적 임포트
        
        ```tsx
        import user from './user';
        export function userModule() { }
        ```
        
    - 동적 임포트
        
        ```tsx
        // test.ts
        export function foo() {
        	alert('foo');
        }
        
        export function boo() {
        	alert('boo');
        }
        
        // app.ts
        let {foo, boo} = await import('./test.ts');
        
        foo();
        boo();
        
        ---------------
        
        import(경로).then(obj => {...}).catch(err => {...})
        
        ```
        

- 모듈 모드 vs 스크립트 모드
    - import나 export를 포함하느냐를 기준으로 모드 결정
        - 모듈 모드: import, export 포함 → 권장
        - 스크립트 모드: import, export 불포함
        

### 4. 네임스페이스

- namespace 키워드 → **캡슐화 수단으로 권장하지 않음 (WHY?)**
- 특징
    - 이름이 있어야 한다
    - 함수, 변수, 타입, 인터페이스, 다른 네임스페이스 익스포트 가능
    - namespace 블록 안의 모든 코드는 명시적으로 익스포트하지 않는 한 외부에서 볼 수 없다
    - 언제사용? 모듈이 커져서 여러 서브 모듈로 쪼개야 하는 상황을 때
        - 왜? 모든 파일에서 Network.HTTP.get, Network.TCP.listenOn 등의 형태로 호출 가능 → 파일이 다르더라도 하나의 Network 네임스페이스에 속하도록 가능
            
            ```jsx
            // HTTP.ts 
            namespace Network {
                export namespace HTTP {
                    export function get<T>(url: string): Promise<T> {
                        return new Promise((resvole, reject) => {
                            
                        });
                    }
                }
            }
            
            // UDP.ts 
            namespace Network {
                export namespace UDP {
                    export function send(url: string, packets): Promise<void> {
                        return new Promise((resolve, reject) => {
            
                        });
                    }
                }
            }
            
            // app.ts
            **Network**.HTTP.get<string[]>('http://url.com/dogs');
            **Network**.UDP.send('http://url.com/cats', 'buffer');
            ```
            
            - 어떻게 다른 파일에서 사용 가능?
                - Network가 전역으로 이미 정의되어 있는 경우, 타입스크립트는 Network 변수를 새로 생성하는 것이 아니라, 기존의 변수를 확장하기 때문에  → 즉 그렇기 때문에 일반 모듈을 사용하는 것이 더 좋다
                
- 컴파일 코드 예시
    
    ```tsx
    namespace Flowers {
        export function give(count: number) {
            return count + 'flowers'
        }
    }
    ```
    
    ```jsx
    "use strict";
    var Flowers;
    (function (Flowers) {
        function give(count) {
            return count + 'flowers'; // 클로저
        }
        Flowers.give = give;
    })(Flowers || (Flowers = {}));
    ```
    

### 5. 선언 합치기

- 값과 타입 합치기(6.3.4 컴패니언 객체 패턴) → 같은 이름을 공유하는 객체와 타입을 쌍으로 묶는 패턴 / 타입과 값은 별도의 네임스페이스를 갖는다
    
    ```tsx
    type Currency = {
    	unit: 'EUR' | 'GBP' | 'JPY' | 'USD'
    }
    
    let Currency = {
    	DEFAULT: 'USD',
    	from(value: number, unit = Currency.DEFAULT): Currency {
    		return {unit, value}
    	}
    }
    ```
    
- 여러 네임스페이스를 하나로 합치기
- 여러 인터페이스를 하나로 합치기
    
    ```tsx
    interface User {
    	name: string
    }
    
    interface User {
    	age: number
    }
    
    let a: User = {
    	name: 'soo',
    	age: 30
    }
    ```
