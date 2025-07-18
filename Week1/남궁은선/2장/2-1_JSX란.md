# 2.1 JSX란?
- **자바스크립트 내부에서 표현하기 어려웠던 XML 스타일의 트리구문을 작성하는데 도움을 주는 새로운 문법.**
- 런타임에서 직접적으로 실행되거나 표현되지 않기 때문에 반드시 트랜스파일러를 거쳐야한다. 
- HTML,XML외에도 다른 구문으로도 확장될 수 있게 설계되어있다. 


## 2.1.1 JSX의 정의
- JSXElement, JSXAttributes, JSXChildren, JSXStrings 라는 4가지 컴포넌트를 기반으로 구성되어있다. 


#### JSXElement
- JSX를 구성하는 가장 기본요소
- HTML의 element와 비슷한 역할


>
>JSXElement는 다음 중 하나의 형태여야한다.
>- JSXOpeningElement
>    <br/> `예)<JSXElement JSXAttributes(optional)>`
>- JSXClosingElement : JSXOpeningElement가 종료되었음을 알리는 요소로 반드시 쌍으로 사용
>    <br/>`예)</JSXElement>`
>- JSXSelfClosing : 요소가 시작되고, 스스로 종료되는 형태
>    <br/>`예)<JSXElement JSXAttributes(optional)/>`
>- JSXFragment: 아무런 요소가 없는 형태. JSXSelfClosing 형태는 불가.
>    <br/>`예)<>JSXChildren(optional)</>`
>


<br/>

#### JSXElementName
- JSXElementName은 JSXElement의 요소 이름으로 쓸 수 있는 것을 의미한다

> JSXElementName로 가능한 것은 다음과 같다. 
> - JSXIdentifier: 숫자로 시작하거나 \$와 \_외의 다른 특수문자로는 시작할 수 없다.
> `가능한 것) <$></$>  <_></_>`
> `불가능한 것) <1></1>`
> - JSXNamespacedName: JSXIdentifier:JSXIdentifier의 조합. 즉 :를 통해 서로 다른 식별자를 이어주는 것도 하나의 식별자로 취급된다. 두 개 이상은 올바른 식별자로 취급되지 않는다.
> `가능한 것) <foo:bar></foo:bar>`
> `불가능한 것) <foo:bar:baz></foo:bar:baz>`
> - JSXMemeberExpression: JSXIdentifier.JSXIdentifier 조합. 여러 개 이어서 하는 것도 가능.
> `예)<foo.bar.baz></foo.bar.baz>`

#### JSXAttributes
- JSXElement에 부여할 수 있는 속성. optional

가능한 형태
- JSXSpreadAttributes: 자바스크립트의 전개 연산자와 동일한 역할.
- JSXAttribute : 속성을 나타내는 키와 값으로 짝을 이루어 표현

#### JSXChildren
- JSXElement의 자식 값을 나타냄. 
- JSXChild : JSXChildren은 JSXChild를 0개이상 가질 수 있다. 

#### JSXStrings
- HTML에서 사용 가능한 문자열은 모두 JSXStrings에서도 가능하다. 


## 2.1.3 JSX는 어떻게 자바스크립트에서 변환될까?

- @babel/plugin-transform-react-jsx플러그인은 JSX구문을 자바스크립트가 이해할 수 있는 형태로 변환한다.
- 리액트 17, 바벨 7.9.0 이후 버전에서 자동런타임으로 트랜스파일한 결과의 차이점이 존재한다.(8장에서 다룰 예정)

#### 트랜스 파일 이후의 코드는 어떻게 생겼을까?
- JSXElement를 첫 번째 인수로 선언해 요소를 정의한다.
- 옵셔널인 JSXChildren, JSXAttributes, JSXStrings는 이후 인수로 넘겨주어 처리한다. 

위의 특징을 미뤄보아, 
JSXElement만 다르고, 나머지가 완전히 동일한 상황에서 중복 코드를 최소화 할 수 있다. 

```jsx
function TextOrHeading({
  isHeading,
  children,
}: PropsWithChildren<{ isHeading: boolean }>) {
  return isHeading ? (
    <h1 className="text">{children}</h1>
  ) : (
    <span className="text">{children}</span>
  )
}

// JSX가 변환되는 특성을 활용한다면 다음과 같이 간결하게 처리할 수 있다.
import { createElement } from 'react'

function TextOrHeading({
  isHeading,
  children,
}: PropsWithChildren<{ isHeading: boolean }>) {
  return createElement(
    isHeading ? 'h1' : 'span',
    { className: 'text' },
    children,
  )
}
```

## 2.1.4 정리
- JSX가 HTML문법과 자바스크립트문법이 뒤섞여서 코드 가독성을 해칠 수 있으므로, 주의해서 사용해야한다. 
- 때에 따라서는 직접 createElement를 사용해 컴포넌트를 구성하는 편이 더 효율적일 수 있다.