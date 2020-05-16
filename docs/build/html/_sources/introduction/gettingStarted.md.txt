# Getting Started



## Create React App

Recoil은 리액트를 위한 상태관리 라이브러리입니다. Recoil을 사용하기 위해 리엑트 설치와 실행해야 합니다. 가장 쉬운 방법으로 프로젝트를 생성해주는 Create React App(CRA)를 사용하는 것 입니다.

```bash
$ npx create-react-app recoil_practice
```

> **npx**는 npm 5.2 이상과 함께 제공되는 패키지 실행 도구입니다. 

Create React App를 설치하는 다른 방법은 (공식문서)[https://github.com/facebook/create-react-app#creating-an-app]를 참고해주세요.



## installation

recoil 패키지는 npm을 이용하여 최신버전을 다운받을 수 있습니다. 

```bash
$ npm install recoil
```

또는 yarn을 이용한다면 다음과같이 설치할 수 있습니다.

```sh
$ yarn add recoil
```



## RecoilRoot

recoil 상태를 사용하는 컴포넌트는 부모 트리안에 `RecoilRoot` 필요합니다. RecoilRoot을 사용하기 좋은 위치는 루트 컴포넌트에 집어넣는 것 입니다.

```jsx
// src/App.js
import React from 'react';
import {
  RecoilRoot,
  atom,
  selector,
  useRecoilState,
  useRecoilValue,
} from 'recoil';

function App() {
  return (
    <RecoilRoot>
      <CharacterCounter />
    </RecoilRoot>
  );
}

export default App
```

우리는 다음 섹션에서 CharacterCounter 컴포넌트를 개선할 것 입니다. 

> 참고: 앞의 코드만 작성했다면 CharacterCounter 컴포넌트가 존재하지 않는다는 에러가 발생합니다.



## Atom

**Atom**은 상태를 보여줍니다. 모든 컴포넌트에서 Atom을 읽고 쓸 수 있습니다. Atom의 값을 구독하는 컴포넌트는 Atom의 값이 업데이트 되면 구독하고 있는 모든 컴포넌트들은 다시 렌더링합니다.

```js
const textState = atom({
  key: 'textState', // 고유 ID (with respect to other atoms/selectors)
  default: '', // 기본값 (일명 초기 값)
});
```

Atom에서 읽고 쓰기위해 useRecoilState()를 사용해야 합니다.

```jsx
function CharacterCounter() {
  return (
    <div>
      <TextInput />
      <CharacterCount />
    </div>
  );
}

function TextInput() {
  const [text, setText] = useRecoilState(textState);

  const onChange = (event) => {
    setText(event.target.value);
  };

  return (
    <div>
      <input type="text" value={text} onChange={onChange} />
      <br />
      Echo: {text}
    </div>
  );
}
```



## Selector

Selector는 상태를 가져와 보여줍니다. 가져온 상태는 최신 상태입니다. 가져온 상태는 어떤 주어진 상태를 수정하는 순수한 함수에 상태를 전달한 결과로 생각할 수 있습니다.

```js
const charCountState = selector({
  key: 'charCountState', // unique ID (with respect to other atoms/selectors)
  get: ({get}) => {
    const text = get(textState);

    return text.length;
  },
});
```

우리는 useRecoilValue() 훅스를 사용하여 charCountState 값을 읽을 수 있습니다.

```jsx
function CharacterCount() {
  const count = useRecoilValue(charCountState);

  return <>Character Count: {count}</>;
}
```





