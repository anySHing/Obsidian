# 라우터 선택하기

> 6.4버전에서 다음과 같은 새로운 라우터들이 추가되었다.

1. createBrowserRouter
2. createMemoryRouter
3. createHashRouter
4. createStaticRouter

이 녀석들은 v6.4에서 새롭게 추가된 data API를 제공한다.

data API는

1. route.action
2. route.errorElement
3. route.lazy
4. route.loader

등이 있다.

## v6.4의 새로운 라우터로 업그레이드 하기

공식 문서에는 v6.4에서 새롭게 추가된 라우터들 중 하나로 업데이트 하는 것을 권장하고 있다.

이미 잘 사용하고 있는 라우터를 삭제하고 새로운 라우터를 만드는 것은 까다로운 작업일 수 있다. 때문에 `createRoutesFromElements`를 사용하여 업데이트 하는 것을 공식 문서에서 소개하고 있다.

```jsx
const router = createBrowserRouter(
	createRoutesFromElements(
		<Route path="/" element={<Root />}>
			<Route paht="dashboard" element={<Dashboard />} />
		</Route>
	)
)

// 다음과 같이 RouterProvider에 라우터를 넘겨줘야 한다.
ReactDOM.createRoot(document.getElementById('root')).render(
	<React.StrictMode>
		<RouterProvider router={router} />
	</React.StrictMode>
)
```

## 언제 어떤 라우터를 사용해야 할까?

> Web Project

웹 프로젝트를 진행할 때는 `createBorwserRouter`가 권장된다. 다른 대안으로는 `createHashRouter`가 있는데, 이는 프로젝트 규모 및 특징에 따라 정하면 된다. 이 둘의 특징을 간단히 정리하자면 다음과 같다.

- createBrowerRouter:
	- 동적인 페이지에 적합
	- 검색엔진 최적화(SEO)
	- github-pages 배포가 까다로움

- createHashRouter
	- 정적인 페이지에 적합(개인 포트폴리오)
	- 검색 엔진으로 읽지 못함(#값 때문에 서버가 읽지 못하고 서버가 페이지의 유무를 모름)
	- github-pages 배포가 간편

만약 data API를 사용하지 않는다면 기존의 라우터인 `BrowserRouter`, `HashRouter`를 그대로 사용해도 된다.

> Test 환경

React Router API를 사용한 컴포넌트를 테스트하기 위해선 `createMemoryRouter`가 유용하다. `Storybook`과 같은 테스트 및 개발 도구에 주로 사용하면 된다.

# RouterProvider

어떤 라우터 객체이든 RouterProvider에 등록되어야 한다.

가장 최상위 루트(보통 index.tsx, main.tsx 등)에 다음과 같이 RouterProvider를 추가하면 된다.

```tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { RouterProvider } from 'react-router-dom';
import App from './App';

ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
	<React.StrictMode>
		<RouterProvider router={router} />
	</React.StrictMode>
)
```

`RouterProvider`에는 라우터 속성뿐 아니라 `fallbackElement` 속성도 있는데 이는 loader가 모두 완료되기 전 실행된다. 조금 더 설명하자면 `Route` 속성 중 `loader`는 특정 주소가 렌더되기 전 실행되는데, `loader`가 완료되기 전까지 `fallbackElement`가 실행된다. 즉, 아직 데이터가 불러와지지 않았기 때문에 로딩 화면을 보여준다는 것이다.

예를 들면 다음 코드는 1초동안 `Loading...`이 보이고 `App` 컴포넌트가 렌더링된다.

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { RouterProvider, createBrowserRouter } from 'react-router-dom';
import App from './App';

const router = createBrowserRouter([
	{
		path: "",
		loader: async () => {
			return new Promise((res) => {
				setTimeout(() => {
					return res('finish!');
				}, 3000);
			});
		},
		element: <App />
	}
]);

ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
	<React.StrictMode>
		<RouterProvider router={router} fallbackElement={<div>Loading...</div>}/>
	</React.StrictMode>
)
```

# creatBrowserRouter

`createBrowserRouter`는 위에서 설명했다시피 웹 프로젝트를 진행할 때 보통 사용하는 라우터이다.

DOM History API를 사용하여 URL을 업데이트하고, history stack을 관리한다.