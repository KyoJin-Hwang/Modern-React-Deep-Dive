# 7.6 Next.js 환경 디버깅하기

사용자의 기기의 성능과 스펙에 따라 같은 메모리 누수라도 다른 결과를 낳게 될 것이다. 그러나 만약 **서버 사이드 렌더링을 수행하는 자바스크립트 환경에서 메모리가 누수가 발생**하면 서버 자체에 부담이 발생할 것이고 **모든 사용자가 사용할 수 없는 심각한 상황을 초래**하게 될 것이다.

## 7.6.1 Next.js 프로젝트를 디버그 모드로 실행하기

-   다음과 같이 명령어를 설정한 뒤 `next dev` 를 실행하면 디버거가 활성된다.

```tsx
"dev": NODE_OPTIONS='--inspect' next dev
```

-   chrome://inspect/#devices 에 접속하여 Open dedicated DevTools for Node로 새로운 개발자 도구 실행

## 7.6.2 Next.js 서버에 트래픽 유입시키기

새로고침이나 다른 컴퓨터를 이용해서 접속하는 것보다 ab 라이브러리를 통해 트래픽을 발생시킬 수 있다. [ab](https://httpd.apache.org/docs/current/ko/programs/ab.html)는 아파치 재단에서 제공하는 웹서버 성능 검사 도구이다.

```tsx
ab -k -c 50 -n 10000 "http://127.0.0.1:3000/"
```

“http://127.0.0.1:3000/”(로컬에서는 IP주소를 입력해야 한다.)를 향해 한 번에 50개의 요청을 총 10,000회 시도하는 명령어이다.

ab를 사용하면 요청으로부터 응답받는 데 걸린시간, 바이트 크기 등 다양한 정보를 확인할 수 있다.

## 7.6.3 Next.js의 메모리 누수 지점 확인하기

```tsx
import type { GetServerSidePropsContext, NextPage } from "next";

const access_users = [];

function Home({ currentDateTime }: { currentDateTime: number }) {
    return <>{currentDateTime}</>;
}

export const getServerSideProps = (ctx: GetServerSidePropsContext) => {
    const currentDateTime = new Date().getTime();

    access_users.push({
        user: `user-${Math.round(Math.random() * 100000)}`,
        currentDateTime,
    });

    return {
        props: {
            currentDateTime,
        },
    };
};
```

이 코드는 `getServerSideProps` 의 다수 실행과 메모리 누수가 발생한다.

페이지 접근 요청이 있을 때마다 실행되는 함수이므로 **최대한 부수 효과가 없는 순수함수로 만들어야 한다.**

❌ 이렇게 하면 안됨 
```tsx
const access_users = []; // ❌ 서버 전역 상태 유지
```

✅ 이렇게 해야 함
함수 내부에서 변수를 만들면 요청이 끝날 때 GC가 정리함
혹은 DB, 캐시(redis 등)에 저장하고 서버 메모리에 들고 있지 않기
```tsx
export const getServerSideProps = () => {
    const access_users = []; // 요청마다 새로 생성됨
    access_users.push(...);
    ...
}
```