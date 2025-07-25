
# Next.js 톺아보기

Next.js는 **React 프레임워크**로, 프로덕션 환경에서 사용할 수 있는 **강력한 기능**들을 제공합니다. **서버 사이드 렌더링(SSR)**, **정적 사이트 생성(SSG)**, **API 라우트** 등 다양한 렌더링 방식과 편리한 개발 환경을 지원하여 효율적인 웹 애플리케이션 개발을 돕습니다.

## 4.3.1 Next.js란

Next.js는 React 기반의 웹 애플리케이션을 위한 오픈 소스 웹 개발 프레임워크입니다. **PHP**에 영감을 받아 웹 페이지의 **빠른 로딩과 SEO 최적화**를 위해 최초 설계 당시부터 **서버 사이드 렌더링(SSR)** 을 염두에 두었습니다.

## 4.3.2 Next.js 시작하기

### `package.json`

프로젝트의 의존성과 스크립트를 정의하는 파일입니다. Next.js 프로젝트를 생성하면 필요한 패키지들이 자동으로 추가됩니다.

### `next.config.js`

Next.js 애플리케이션의 동작을 설정하는 파일입니다.

* **`reactStrictMode`** : React의 **엄격 모드**를 활성화할지 여부를 설정합니다. 개발 중 잠재적인 문제를 감지하고 경고를 표시하여 안정적인 애플리케이션 개발을 돕습니다.
* **`swcMinify`**: **SWC**를 사용하여 JavaScript/TypeScript 코드를 **최소화**할지 여부를 설정합니다. SWC는 **Rust**로 작성된 컴파일러로, Babel보다 **훨씬 빠른 빌드 속도**를 제공합니다. 특별한 이유가 없다면 `swcMinify: true`를 사용하는 것을 권장합니다.
* **`compiler.styledComponents`**: (Next.js 12부터) SWC 컴파일러를 통해 **Styled Components**를 최적화하는 옵션입니다. 개발 모드에서는 스타일이 올바르게 적용되도록 돕고, 프로덕션 모드에서는 `SPEEDY_MODE`를 활성화하여 성능을 향상시킵니다.

### `pages/_app.tsx` (App Router에서는 `app/layout.tsx` 또는 `app/template.tsx`, `app/page.tsx` 등에 통합)

Next.js 애플리케이션에서 **모든 페이지에 공통적으로 적용**되어야 하는 로직이나 레이아웃을 정의합니다.

* **전역 CSS 선언**: `reset.css`와 같은 전역 스타일을 이곳에서 임포트하여 전체 애플리케이션에 적용합니다.
* **공통 데이터 제공**: 모든 페이지에서 필요한 공통 데이터를 `getInitialProps` (최신 버전에서는 권장되지 않음) 또는 React Context API를 통해 제공할 수 있습니다.
* **에러 바운더리**: 애플리케이션 전반의 에러를 처리하기 위한 **에러 바운더리**를 설정할 수 있습니다.
* `_app.tsx`의 `render()`는 **최초에는 서버 사이드에서, 이후에는 클라이언트 사이드에서** 실행될 수 있습니다 (이 동작은 데이터 페칭 방식에 따라 달라질 수 있습니다).

### `pages/_document.tsx` (App Router에서는 사용되지 않음)

애플리케이션의 **HTML 문서 구조를 초기화**하는 파일입니다. `_app.tsx`가 애플리케이션 페이지 전체를 초기화한다면, 이곳은 `<html/>` 및 `<body/>` 태그와 같은 HTML의 뼈대를 구성합니다.

* **DOM 속성 직접 추가**: `<html>` 또는 `<body>` 태그에 사용자 정의 속성(예: `lang` 속성)을 추가할 때 사용합니다.
* **서버 전용**: `_document.tsx`는 **무조건 서버에서만 실행**됩니다. 따라서 이벤트 핸들러와 같은 클라이언트 사이드 스크립트를 직접 추가할 수 없습니다.
* **Head 태그의 차이**:
    * `next/document`에서 제공하는 `Head`: 오직 `_document.tsx`에서만 사용 가능하며, `<title>` 태그는 사용할 수 없습니다.
    * `next/head`에서 기본적으로 제공하는 `Head`: 각 페이지에서 사용 가능하며, SEO에 필요한 메타 정보나 `<title>` 등을 담을 수 있습니다.
* Next.js로 만드는 웹사이트의 **HTML 뼈대**와 관련된 코드를 추가하는 곳이며, 반드시 서버에서만 렌더링됩니다.

### `pages/_error.tsx` (App Router에서는 `app/not-found.tsx` 및 `app/error.tsx`로 분리)

클라이언트 또는 서버에서 발생하는 에러(주로 **500 에러**)를 처리할 목적으로 만들어진 **커스텀 에러 페이지**입니다.

* **전역 에러 처리**: 애플리케이션 전반에서 발생하는 에러를 적절하게 처리하고 사용자에게 친숙한 에러 메시지를 제공할 때 사용됩니다.
* **개발 모드**: 개발 모드에서는 Next.js가 제공하는 기본 에러 페이지가 표시되므로, `_error.tsx`를 확인하려면 **애플리케이션을 빌드하여 실행**해야 합니다.

### `pages/404.tsx` (App Router에서는 `app/not-found.tsx`)

**404 Not Found** 에러를 위한 커스텀 페이지입니다. 사용자가 존재하지 않는 URL로 접근했을 때 표시됩니다.

### `pages/500.tsx` (App Router에서는 `app/error.tsx`)

**500 Internal Server Error**를 위한 커스텀 페이지입니다. 서버에서 예상치 못한 에러가 발생했을 때 표시됩니다. `_error.tsx`보다 **우선적으로 실행**됩니다.

### `pages/[그 외에].tsx` (App Router에서는 Dynamic Segments)

`pages` 디렉토리 내의 파일 시스템 기반 라우팅 규칙입니다.

* **`index.tsx`**: 웹사이트의 **루트 경로**(`\` 또는 `/`)에 해당합니다.
* **`/pages/a/[variable].tsx`**: **동적 라우팅**을 처리합니다. `[variable]` 안의 내용은 URL 경로의 **변수**로 처리되며, `getStaticProps` 또는 `getServerSideProps`의 `context.query.variable`을 통해 해당 값에 접근할 수 있습니다. 예: `/a/hello`는 `variable`이 `hello`가 됩니다.
* **`/pages/a/[...props].tsx`**: **Catch-all 라우팅**을 처리합니다. `/a`를 제외한 `/a/foo`, `/a/foo/bar` 등 `/a` 하위의 **모든 경로**를 이 파일로 연결합니다. `props`는 배열 형태로 해당 경로의 나머지 부분을 담게 됩니다. `context.query.props`를 통해 접근합니다.

#### 서버 라우팅과 클라이언트 라우팅의 차이

* **SSR의 최초 페이지 렌더링**은 **서버에서 수행**되어 초기 로딩 속도를 빠르게 하고 SEO에 유리합니다.
* **`next/link`**는 Next.js에서 제공하는 **클라이언트 사이드 라우팅 컴포넌트**입니다. 일반적인 HTML의 `<a/>` 태그와 유사하지만, 동작 방식이 다릅니다.
    * `<a/>` 태그를 사용하여 페이지를 이동할 때는 **모든 리소스를 처음부터 다시 다운로드**합니다.
    * `next/link`를 통해 이동할 때는 **새로운 페이지 이동에 필요한 내용만(새로운 데이터와 변경된 UI 부분)** 을 클라이언트 사이드에서 비동기적으로 가져와 렌더링합니다. 이는 **SPA(Single Page Application)처럼 동작하여 사용자 경험을 향상**시킵니다.
* 이러한 클라이언트 라우팅의 장점을 활용하기 위해 **`next/link`** 를 사용하고, `window.location.href`나 `window.location.push` 대신 **`next/router`의 `router.push`** 를 사용하는 것이 좋습니다.

#### 페이지에서 `getServerSideProps`를 제거하면 어떻게 될까?

* `getServerSideProps`가 있는 페이지는 Next.js 빌드 시 **서버 사이드 런타임 체크가 필요한 페이지** 로 분류됩니다.
* `getServerSideProps`가 없는 페이지는 **서버 사이드 렌더링이 필요 없는 정적인 페이지** 로 분류됩니다. 이 경우 Next.js는 `typeof window`와 같은 브라우저 전용 코드를 빌드 시점에 **트리 쉐이킹(Tree Shaking)** 하여 번들 크기를 최적화합니다. 이는 SSG(Static Site Generation) 페이지의 기본 동작입니다.

### `/pages/api/a.ts` (App Router에서는 `app/api/route.ts`로 변경)

Next.js의 **API 라우트** 기능입니다. `/api` 경로 아래에 파일을 생성하면 해당 경로로 HTTP 요청을 처리하는 서버리스 함수 또는 API 엔드포인트를 만들 수 있습니다.

* **`api/a` 경로로 호출**: 클라이언트에서 `/api/a`로 요청을 보내면 해당 파일의 코드가 실행됩니다. 이는 HTML 요청이 아닌, **단순히 서버 요청(API 호출)** 을 주고받는 데 사용됩니다.
* **BFF (Backend For Frontend)**: 서버에서 여러 데이터를 조합하여 클라이언트에 최적화된 형태로 제공하는 BFF 형태로 활용할 수 있습니다.
* **풀스택 애플리케이션**: 프론트엔드와 백엔드 로직을 하나의 Next.js 프로젝트 내에서 구축하는 **풀스택 애플리케이션**을 만들 때 유용합니다.
* **CORS 문제 우회**: 클라이언트에서 직접 외부 API를 호출할 때 발생하는 **CORS(Cross-Origin Resource Sharing) 문제**를 API 라우트를 통해 우회할 수 있습니다.

---

## 4.3.3 Data Fetching (App Router에서는 `fetch` API 및 RSC(React Server Components) 패턴 권장)

`pages/` 폴더에 있는 라우팅이 되는 파일에서만 사용할 수 있습니다. 이 함수들은 Next.js의 **예약어로 지정**되어 있으며, 반드시 `export`를 사용하여 함수를 파일 외부로 내보내야 합니다.

### `getStaticPaths`와 `getStaticProps` (pages 라우터에서 SSG를 위한 함수)

이 두 함수는 주로 **CMS(콘텐츠 관리 시스템)**, 블로그, 게시판과 같이 **사용자 요청과 관계없이 정적으로 결정된 페이지**를 미리 생성하여 보여주고자 할 때 사용됩니다.

* **반드시 함께 사용**: `getStaticPaths`와 `getStaticProps`는 SSG를 위해 **반드시 함께 사용**되어야 합니다.
* **`getStaticPaths`**: **접근 가능한 주소(경로)**를 미리 정의합니다. 빌드 시점에 이 함수가 반환하는 경로들을 기반으로 페이지를 생성합니다.
* **`getStaticProps`**: `getStaticPaths`에서 정의한 각 경로에 대해 **해당 페이지로 요청이 왔을 때 제공할 `props`를 반환**하는 함수입니다.
* **빌드 시점 생성**: 이렇게 사용자가 접근할 수 있는 페이지를 빌드 시점에 모두 생성하고 배포하면, 사용자가 페이지를 요청했을 때 **렌더링을 기다릴 필요 없이 즉시 콘텐츠를 제공**할 수 있습니다.
* **`fallback` 옵션**: `getStaticPaths` 함수의 반환값 중 `fallback` 옵션은 미리 빌드해야 할 페이지가 많은 경우 유용합니다.
    * `paths`에 미리 빌드해 둘 **일부 페이지만 리스트로 반환**하고, `fallback`을 `true`나 `'blocking'`으로 설정할 수 있습니다.
    * **`fallback: true`**: `paths`에 기재되지 않은 페이지에 대한 요청이 오면, Next.js는 **"폴백(fallback)" 페이지**를 즉시 제공합니다 (로딩 상태를 보여줄 수 있음). 이어서 서버에서 해당 페이지를 빌드하고, 빌드가 완료되면 클라이언트에서 빌드된 페이지를 렌더링합니다. 이후 같은 요청이 오면 빌드된 페이지를 바로 제공합니다.
    * **`fallback: 'blocking'`**: `paths`에 기재되지 않은 페이지에 대한 요청이 오면, Next.js는 **서버에서 해당 페이지를 빌드할 때까지 기다린 후**, 빌드가 완료되면 완성된 페이지를 사용자에게 제공합니다. 클라이언트에게는 빈 페이지 대신 완전한 페이지가 로딩됩니다.

### `getServerSideProps` (pages 라우터에서 SSR을 위한 함수)

이 함수는 **요청이 들어올 때마다 서버에서 실행**되며, 해당 함수가 있는 페이지는 **페이지 진입 전에 무조건 이 함수를 실행**합니다.

* **실행 시점**: 사용자가 매 페이지를 호출할 때마다 서버에서 실행됩니다.
* **반환 값**: `props` 객체를 반환하여 페이지 컴포넌트에 데이터를 전달하거나, `redirect` 옵션을 통해 다른 페이지로 리다이렉트시킬 수도 있습니다.
* **`context` 객체**: `context` 객체를 통해 요청 정보(예: `context.query.id`로 URL 경로의 ID 값에 접근)를 얻을 수 있습니다.
* **`__NEXT_DATA__`**: `getServerSideProps`를 통해 렌더링된 페이지의 결과물에는 `<script id="__NEXT_DATA__">` 태그가 포함됩니다. 이 스크립트는 `props`뿐만 아니라 현재 페이지 정보, 쿼리 등 다양한 정보를 담고 있습니다. 이는 **서버에서 가져온 정보를 HTML에 스크립트 형태로 내려줌으로써 클라이언트 사이드에서 초기 데이터를 활용**할 수 있게 합니다. 이 정보는 `window` 객체에도 저장됩니다.
    * **직렬화 가능 데이터**: `__NEXT_DATA__` 안에는 **직렬화 가능한 정보(JSON으로 변환 가능한 데이터 타입)**만 담을 수 있습니다. `class` 인스턴스나 `Date` 객체 등은 직접 담을 수 없습니다. (예: `Reason: object ("[object Date]") cannot be serialized as JSON. Please only return JSON serializable data types.` 에러 발생).
* **서버 전용**: `getServerSideProps`는 **서버에서만 실행**됩니다. 따라서 클라이언트에서만 실행 가능한 `window` 객체 접근과 같은 코드는 이곳에 직접 사용할 수 없습니다.
* **API 호출**: API 호출 시, 브라우저 환경과 달리 서버에서는 자신의 호스트를 유추할 수 없으므로, **`protocol`과 `domain`을 포함한 전체 URL**로 `fetch` 요청을 보내야 합니다. (예: `https://your-domain.com/api/data`).
* **성능 고려**: 이 함수는 사용자가 페이지를 호출할 때마다 실행되고, **함수 실행이 끝나기 전까지는 사용자에게 아무것도 보여주지 않습니다.** 따라서 **간결하게 작성**해야 하며, **최초에 보여줘야 할 데이터가 아니라면 클라이언트에서 데이터를 호출**하는 것이 유리할 수 있습니다.
* **조건부 리다이렉트**: 특정 조건에 따라 다른 페이지로 리다이렉트시키는 로직을 구현할 수 있습니다.

### `getInitialProps` (pages 라우터에서 레거시 데이터 페칭 방식, 현재는 권장되지 않음)

`getServerSideProps`와 달리 `props` 객체를 반환하는 것이 아니라 바로 객체를 반환합니다.

* **실행 환경**: 라우팅 방식(서버 라우팅 또는 클라이언트 라우팅)에 따라서 **서버와 클라이언트 모두에서 실행**될 수 있습니다.
* **현재는 권장되지 않음**: `getInitialProps`는 Next.js 초기 버전에서 사용되던 데이터 페칭 방식이며, 현재는 `getStaticProps`나 `getServerSideProps`를 사용하는 것이 더 권장됩니다. 이들을 통해 **렌더링 방식(SSG/SSR)을 명확하게 분리**하고 **성능 최적화**를 더 쉽게 할 수 있기 때문입니다.

---

## 4.3.4 스타일 적용하기

### 전역 스타일

애플리케이션 전체에 공통으로 적용하고 싶은 스타일(예: 스타일 초기화, 폰트 설정)이 있다면 **`pages/_app.tsx`** (App Router에서는 `app/layout.tsx` 또는 전역 CSS 파일을 임포트)를 활용합니다. 이곳에서 전역 CSS 파일을 임포트하여 사용합니다.

### 컴포넌트 레벨 CSS

클래스명 충돌을 방지하고 컴포넌트별로 스코프된 스타일을 적용하기 위해 **CSS 모듈(`*.module.css`)**을 활용합니다.

### CSS-in-JS (Styled Components, Emotion 등)

Styled Components, Emotion과 같은 CSS-in-JS 라이브러리를 Next.js에서 사용할 때는 **서버 사이드 렌더링 시 스타일이 올바르게 적용**되도록 추가 설정이 필요합니다.

* **`_document.tsx` 커스텀**: (pages 라우터의 경우) Styled Components의 `ServerStyleSheet`를 사용하여 서버에서 모든 스타일을 수집한 후, 이를 HTML에 주입합니다.
    * 이 과정은 각 Styled Components 스타일을 모두 모아 유니크한 클래스명을 부여하고, `_document.tsx`가 서버에서 렌더링될 때 `React.Context` 형태로 제공하게 됩니다.
    * 이러한 과정을 거치지 않고 서버에서 미리 스타일을 모아 SSR에서 한꺼번에 제공하지 않는다면, 브라우저에서 뒤늦게 스타일이 추가되어 **FOUC(Flash Of Unstyled Content)** 현상이 발생할 수 있습니다.
* **SWC 컴파일러 설정**: Next.js 12부터는 Babel 대신 SWC를 사용하므로, `next.config.js`에 `compiler.styledComponents` 옵션을 추가하여 Styled Components를 최적화할 수 있습니다.

#### 프로덕션에서는 `<style>` 태그 내부가 비어있는데 스타일은 정상적으로 적용이 되어있다?

* Styled Components는 개발 모드와 다르게, 프로덕션 모드에서는 **`SPEEDY_MODE`**라는 설정을 사용합니다.
* 이 설정이 켜져 있으면 HTML에 `<style>` 태그를 직접 삽입하는 대신, **자바스크립트를 활용하여 CSSOM(CSS Object Model) 트리에 직접 스타일을 삽입**합니다. 이는 기존 스타일링 방식보다 훨씬 빠르고 효율적입니다.
* **Styled-jsx, styled-components, Emotion**과 같은 CSS-in-JS 라이브러리들은 별도의 Babel 설정 없이 SWC와 함께 사용 가능하므로, 빠른 빌드의 이점을 누릴 수 있습니다.

---

## 4.3.5 `_app.tsx` 응용하기 (App Router에서는 다른 패턴으로 대체)

`_app.tsx`의 `getInitialProps`를 활용하여 **클라이언트 사이드 라우팅 시에도 서버 관련 로직을 한 번만 실행**하도록 최적화하는 패턴이 있었습니다.

* `next/link`나 `next/router`를 이용하면 이후 라우팅은 클라이언트 렌더링처럼 작동합니다.
* 이때 `getServerSideProps`와 같은 서버 관련 로직이 있다고 하더라도, Next.js는 전체 페이지를 다시 가져오는 것이 아니라 **해당 페이지의 `getServerSideProps` 결과를 JSON 파일 형태로만 요청하여 가져옵니다.**
* 이러한 특성을 이용하여, **최초 페이지 접근 시에만 실행하고 싶은 내용**을 `_app.tsx`의 `getInitialProps` 내부에 담아둘 수 있었습니다. 하지만 이는 현재 권장되는 패턴은 아니며, App Router에서는 **React Server Components (RSC)** 및 `fetch` API를 사용하여 데이터 페칭 로직을 관리하는 것이 일반적입니다.

---

## 4.3.6 `next.config.js` (추가 옵션)

Next.js 애플리케이션의 동작을 더욱 세밀하게 제어할 수 있는 다양한 설정 옵션을 제공합니다.

* **`basePath`**: 서비스의 시작 경로를 정의합니다. 예를 들어, 웹사이트가 `/docs` 경로에서 서비스될 경우 `basePath: '/docs'`로 설정하면 클라이언트 렌더링을 트리거하는 모든 주소에 자동으로 `basePath`가 붙은 채로 렌더링 및 작동하게 됩니다.
* **`swcMinify`**: SWC로 코드를 압축할지에 대한 여부입니다. Next.js 13부터 기본값이 `true`입니다.
* **`poweredByHeader`**: Next.js는 기본적으로 응답 헤더에 `X-Powered-By: Next.js` 정보를 제공합니다. 이는 보안적으로 취약점으로 분류될 수 있으므로, `poweredByHeader: false`로 설정하여 이 헤더를 제거할 수 있습니다.
* **`redirects`**: 특정 주소를 다른 주소로 영구적(308) 또는 임시적(307)으로 리다이렉트시키고 싶을 때 사용합니다.
* **`reactStrictMode`**: React의 엄격 모드를 활성화할지 여부입니다.
* **`assetPrefix`**: 빌드된 정적 리소스(CSS, JS, 이미지 등)를 다른 CDN(Content Delivery Network)에 업로드하고자 할 때 사용합니다. 이 옵션에 해당 CDN 주소를 명시하면, Next.js는 모든 정적 리소스 요청을 해당 주소로 보내게 됩니다.

---

**Next.js는 현재 App Router를 중심으로 크게 변화하고 있습니다.** 위에 설명된 많은 `pages` 라우터 기반의 API 및 파일 시스템 규칙은 App Router에서 다른 방식으로 대체되거나 통합되었습니다.

**App Router의 주요 특징:**

* **`app` 디렉토리**: 새로운 라우팅 시스템의 기반이 되는 디렉토리입니다.
* **React Server Components (RSC)**: 서버에서 컴포넌트를 렌더링하여 초기 로딩 속도를 향상시키고 클라이언트 번들 크기를 줄이는 새로운 패러다임입니다.
* **Layouts**: 여러 페이지에서 공유되는 UI를 정의하는 기능입니다.
* **Data Fetching**: 서버 컴포넌트 내에서 `fetch` API를 사용하거나, `use client` 지시어를 사용하여 클라이언트 컴포넌트에서 데이터를 가져오는 방식이 권장됩니다.
* **Nested Routes & Layouts**: 폴더 구조를 통해 중첩된 라우트와 레이아웃을 쉽게 구성할 수 있습니다.

만약 Next.js의 최신 기능과 App Router에 대해 더 자세히 알아보고 싶으시다면, Next.js 공식 문서를 참고하시는 것을 강력히 추천합니다!
