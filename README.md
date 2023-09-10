# Vanilla Javascript로 간단한 SPA 라우터 구현하기

## Result
<img src = "https://velog.velcdn.com/images/gusdh2/post/82d78720-f692-4ecf-a366-31c9d3036ea3/image.gif"/>

## How to run
```
npm install
npm run dev
```

## 1. a 태그 변경하기

```js
$(".navbar").addEventListener("click", (e) => {

    const target = e.target.closest("a");
    if (!(target instanceof HTMLAnchorElement)) return;

    e.preventDefault();
    const targetURL = e.target.href.replace(BASE_URL, "");
    navigate(targetURL);
});
```

a 태그는 기본적으로 페이지를 이동시키는 역할을 한다.

navbar에 있는 a 태그들은 클릭시 페이지를 이동시키는 역할을 하지 않고, 페이지를 이동시키는 역할을 하지 않도록 변경해야 한다.

그리고 navigate 함수를 호출하도록 변경한다.

## 2. navigate 함수 구현하기

```js
export const navigate = (to, isReplace = false) => {
  const historyChangeEvent = new CustomEvent('historychange', {
    detail: {
      to,
      isReplace,
    },
  });

  dispatchEvent(historyChangeEvent);
};
```

CustomEvent와 dispatchEvent를 이용해서 historychange 이벤트를 발생시킨다.

to는 이동할 경로이고, 
isReplace는 history에 push할지 replace할지 결정한다.

## 3. historychange 이벤트 리스너 구현하기

```js
    window.addEventListener('historychange', ({ detail }) => {
      const { to, isReplace } = detail;

      if (isReplace || to === location.pathname) 
        history.replaceState(null, '', to);
      else 
        history.pushState(null, '', to);

      route();
    });
```

historychange 이벤트가 발생했다는 건 페이지의 이동을 요청했다는 것이고,
이때 history의 pushState나 replaceState를 이용해서 history를 변경한다.

그리고 route 함수를 호출한다.

## 4. route 함수 구현하기

```js 
  const findMatchedRoute = () => routes.find(
    (route) => route.path.test(location.pathname)
  );

  const route = () => {
    currentPage = null;
    const TargetPage = findMatchedRoute()?.element || NotFound;
    currentPage = new TargetPage(this.$container);
  };
```

현재 페이지를 null로 초기화하고, findMatchedRoute 함수를 호출해서 현재 페이지를 찾는다.

현재 페이지를 찾았다면 해당 페이지를 currentPage에 할당하고, 해당 페이지를 렌더링한다.