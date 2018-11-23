---
layout: post
title: React 16 Context 사용하기
feature-img: "assets/img/picture/react-context.png"
thumbnail: "assets/img/picture/react-context.png"
tags: [React, Redux]
excerpt_separator: <!--more-->
---

* TOC
{:toc}

## Context?
React 공식 블로그에서 정의한 Context는 다음과 같다.
> Context provides a way to pass data through the component tree without having to pass props down manually at every level.

쉽게 말해서, 데이터를 일일이 전달할 필요없이 한번에 특정 컴포넌트에 전달하는 방법이다.
React의 단방향 데이터 흐름상, 하위 컴포넌트가 많아질수록 일일이 값을 전달하기가 매우 번거롭다.
Context API는 그런 불편함을 해소하고자 만들어졌다.

<!--more-->

Context는 React 16.3에서 새로 추가 된 API이다. 이전 버전에도 있는데? 맞다. 이전 버전의 Context
API는 [Legacy Context](https://reactjs.org/docs/legacy-context.html){: target="_blank" }
페이지에서 확인할 수 있다. 이전 버전의 Context API는 곧 지원이 중단될 예정이니 사용중이라면 참고하자. 

언제 Context를 사용할까? `로그인 유저`, `테마`, `언어` 등 글로벌한 데이터를 관리할 때 사용한다. 
그럼 Context를 어떻게 사용하는지 예제를 통해 알아보자.

## Context 사용 예제
흔히 사용하는 웹 페이지의 구성을 예제로 한다.
예제에선 `App`, `Layout`, `NavBar` 세 가지 컴포넌트를 사용했다. 컴포넌트 트리로 표현하면 다음과 같다. 

##### 컴포넌트 트리
{:.no_toc}
```
<App>
  <Layout>
    <NavBar>
```
 
최상위 컴포넌트인 `App`은 `로그인 유저`의 데이터를 갖고 있다. 이 `로그인 유저`의 데이터를 `NavBar`
컴포넌트에 전달하는 것이 우리의 최종 목적이다.

```jsx
// App.js
import React from 'react';
import Layout from './layout';

const CurrentUserContext = React.createContext(null);

class App extends React.Component {
  state = { currentUser: null };

  render() {
    return (
      <CurrentUserContext.Provider currentUser={this.state.currentUser}>
        <Layout>
      </CurrentUserContext.Provider>
    );
  }
}
```

Context를 사용하기 위해서 가장 먼저 해야할 일은 `React.createContext(defaultValue)`로
Context 객체를 만드는 것이다. 로그인 유저의 기본값은 `null`이므로 다음과 같이 Context를 생성했다.

```jsx
const CurrentUserContext = React.createContext(null);
```

Context를 만들면 `Provider`와 `Consumer`를 사용할 수 있다.
이 두 가지의 역할은 변수를 전달하는 `공급자`와 전달받은 변수를 사용하는 `소비자`이다.

예제를 보면 `Layout` 컴포넌트를 `CurrentUserContext.Provider`가 감싸고 있다.

```jsx
<CurrentUserContext.Provider currentUser={this.state.currentUser}>
  <Layout>
</CurrentUserContext.Provider>
```

`Layout`을 포함한 모든 하위 컴포넌트들은 이제부터 공급자로부터 전달받은 `currentUser` 변수에 접근할 수 있다.

이제 `currentUser`를 '공급'받았으니 '소비'할 차례다.
하위 컴포넌트에서 `currentUser` 변수를 어떻게 접근하는지 다음의 예제를 보자. 

```jsx
// Layout.js
// NavBar 컴포넌트를 별도의 js파일에서 import했다고 가정한다.
class Layout extends React.Component {
  render() {
    return (
      <div>
        <NavBar />
        <h1>Hello, World</h1>
      </div>
    );
  }
}

// NavBar.js
// CurrentUserContext 객체를 별도의 js파일에서 import했다고 가정한다.
class NavBar extends Component {
  render() {
    return (
      <CurrentUserContext.Consumer>
        {currentUser => (
          <a href="/">Home</a>
          <a href="/about">About</a>
          <a href=`/profile/${currentUser.id}`>
        )}
      </CurrentUserContext.Consumer>
    );
  }
}
```

`NavBar` 컴포넌트에서 `Consumer`를 어떻게 사용하고 있는지 주목하자.

```jsx
<CurrentUserContext.Consumer>
  {currentUser => (
    <a href="/">Home</a>
    <a href="/about">About</a>
    <a href=`/profile/${currentUser.id}`>
  )}
</CurrentUserContext.Consumer>
```

`a` 링크들을 `CurrentUserContext.Consumer`가 감싸고 있고, 그 안에 함수를 통해서 구현하고 있다.
함수의 인자를 통해서 `currentUser`를 전달받았다. 일단 우리의 목표는 달성했다.

이 함수는 현재 Context의 value(여기선 `currentUser`)을 인자로 받고 React 노드를 반환하는 함수이다.
이 value는 Context의 가장 가까운 공급자의 값과 같다. 예제에선 하나의 공급자가 있지만 여러 개의 공급자가
존재할 수도 있다. `Consumer`는 컴포넌트 트리의 최상위에서부터 가장 가까운 공급자의 value를 가져온다.

위의 예제에선 함수를 통해 값을 전달받았지만, 클래스에 `Context`를 주입하는 방법도 가능하다.

```jsx
// NavBar.js
// CurrentUserContext 객체를 별도의 js파일에서 import했다고 가정한다.
class NavBar extends Component {
  render() {
    const { currentUser } = this.context;
  
    return (
      <React.Fragment>
        <a href="/">Home</a>
        <a href="/about">About</a>
        <a href=`/profile/${currentUser.id}`>
      </React.Fragment>
    );
  }
}

NavBar.contextType = CurrentUserContext;
```

이 방법의 장점은 React 컴포넌트의 모든 lifecycle에서 사용이 가능하다는 점이다.

다음은 예제에서 생략된 CurrentUserContext의 js파일이다.
```jsx
// currentUserContext.js
import React from 'react';

const CurrentUserContext = React.createContext(null);

export default CurrentUserContext;
```

## Redux vs Context API
Context API를 보면 떠오르는 라이브러리가 있다. 바로 [Redux](https://redux.js.org/){: target="_blank" }다.
React와 함께 대표적으로 사용되는 React-redux 라이브러리의 출시일은 2015년 7월 12일이다. 
React의 출시일이 2015년 3월인걸 생각해보면 둘은 굉장히 오랫동안 함께 쓰여왔다는 걸 알 수 있다.

Redux는 어떻게 특정 컴포넌트에게 데이터를 전달했을까?
정답은 Context API다. 최신이 아닌, [옛날 버전](https://reactjs.org/docs/legacy-context.html){: target="_blank" }으로.

이제 최신 Context API가 나온 시점에서, Redux를 사용할지 Context API를 사용할지 고민이 될 것이다.
Context API와 Redux 각각의 장점을 보면서 살펴보기로 하자. 

### Redux의 장점
#### 1. pure한 `connect`
Redux의 `connect`를 사용하면 자동으로 컴포넌트를 "pure"하게 만든다. 그 말인 즉슨, props가 변경될
때만 다시 렌더링을 한다. 즉, Redux의 state의 일부가 변경될 때만 렌더링이 된다. 따라서 불필요한 렌더링이
알아서 방지되고, 앱 실행 속도를 향상시킬 수 있다.
수동 구현 방법: `React.PureComponent`를 extends 하거나 `shouldComponentUpdate`를 이용한다.

#### 2. 쉬운 디버깅
[Redux DevTool Extension](https://github.com/zalmoxisus/redux-devtools-extension){: target="_blank" }을 이용하면
모든 action에 대한 로그를 볼 수 있다. action이 실행될 때마다 실행 전/후의 state를 살펴볼 수 있다.

#### 3. middleware를 이용한 커스터마이징
Redux는 다음과 같은 멋진 문구와 함께 middleware를 지원하고 있다.
> a function that runs every time an action is dispatched.

- _FETCH로 시작되는 action이 실행될 때마다 API 요청을 하고싶을 때
- 분석 소프트웨어에 이벤트를 기록하고 싶을 때
- 특정 시간에 특정 action 실행을 방지하고 싶을 때
- JWT 토큰을 가로채서 로컬 저장소에 자동으로 저장하게 만들고 싶을 때

middleware를 이용하면 모두 가능하다. middleware 커스터마이징에 대한 좋은 글이 있다.
[middle를 작성하는 법](https://medium.com/@jacobp100/you-arent-using-redux-middleware-enough-94ffe991e6){: target="_blank" }

### Context API의 장점
#### 1. 유연하다
##### - Consumer를 HOC로 만들기
`CurrentUserContext`를 매번 필요할 때마다 추가하는 것이 번거로울 수 있다.

만약 value를 props로 받고 싶다면, 다음과 같이 구현할 수 있다.

```jsx
function withCurrentUser(Component) {
  return function ConnectedComponent(props) {
    return (
      <CurrentUserContext.Consumer>
        {currentUser => <Component {...props} currentUser={currentUser}/>}
      </CurrentUserContext.Consumer>
    );
  }
}
```

그러면 `NavBar` 컴포넌트를 `withCurrentUser`를 이용하여 다음과 같이 바꿀 수 있다.

```jsx
// NavBar.js
class NavBar extends Component {
  render() {
    const { currentUser } = this.props;
  
    return (
      <React.Fragment>
        <a href="/">Home</a>
        <a href="/about">About</a>
        <a href=`/profile/${currentUser.id}`>
      </React.Fragment>
    );
  }
}

export default withCurrentUser(NavBar);
```

그럼 context가 Redux의 `connect`처럼 작동한다. 단, 자동 pure화 기능은 빠졌다.

##### - Provider에서 state 관리하기
context의 Provider는 단순히 값을 전달하는 연결고리일 뿐이다. 데이터를 유지하는 기능은 없다.
대신에 wrapper를 만들어서 데이터를 관리하게 만들 수 있다.

위의 예제에서는 `App` 컴포넌트가 데이터를 관리하고 있다. Redux의 store와 비슷한 컨셉으로
context를 통해 state를 전달하는 컴포넌트를 구현할 수 있다:

```jsx
class UserStore extends React.Component {
  state = {
    currentUser: {
      avatar:
        "https://www.gravatar.com/avatar/5c3dd2d257ff0e14dbd2583485dbd44b",
      name: "Harim",
      email: "harim123@gmail.com",
    }
  };

  render() {
    return (
      <CurrentUserContext.Provider value={this.state.currentUser}>
        {this.props.children}
      </CurrentUserContext.Provider>
    );
  }
}

// ... 중도 생략 ...

ReactDOM.render(
  <UserStore>
    <App />
  </UserStore>,
  document.querySelector("#root")
);
```

이제 유저 데이터는 유일한 관심사가 유저 컴포넌트인 `UserStore`에서 관리한다. 이를 통해
`App`을 stateless하게 만들 수 있다. 깔끔해진 코드는 덤이다.

##### - Context를 통해서 Action 전달하기
`Provider`에서 전달하는 값엔 어떤 데이터든지 전달이 가능하다. 즉, 함수도 전달이 가능하다.
value 뿐만 아니라 'Action'도 전달이 가능하다는 말이다.

### Context API vs Redux, 그래서 승자는?
지금까지 두 가지를 모두 살펴보았다. 결과적으로 말하면, **기호와 상황에 맞게 사용하면 된다**.

현재 앱의 규모가 얼마나 큰지, 또는 얼마나 커질 것인에 따라 다르다. 얼마나 많은 사람이 사용할 것인지
(개인 프로젝트인지 아니면 협업 프로젝트인지),
당신 혹은 당신의 팀이 기능적인 개념에 대해서 얼만큼 경험이 있는지 (ex: immutability 또는 pure function 등 Redux가 의존하는 개념)
생각해보자.

개인적으로는 Context API가 React에 내장되있고, 유연한 구현이 가능하다는 점에서 Context API를 더
자주 쓰게 될거 같다.

## 참고 자료

[React Official Context Document](https://reactjs.org/docs/context.html){: target="_blank" }, https://reactjs.org/docs/context.html

[Context api vs Redux](https://daveceddia.com/context-api-vs-redux/){: target="_blank" }, https://reactjs.org/docs/context.html
