# CoreS

## <RecoilRoot />

atom을 사용할 수 있는 컨텍스트를 제공합니다. recoil state를 사용하기 위해 RecoilRoot을 반드시 상위 컴포넌트에 존재해야 합니다. RecoilRoot는 여러개 공존할 수 있으며 recoil state는 RecoilRoot에 따라 관리됩니다. 

* props
  * initializeState? :({set, setUnvalidatedAtomValues}) => void
    * recoil store가 처음 렌더링 될 때 호출되는 함수. 원자의 초기값 설정할 수 있습니다.
      * set: <T> (RecoilValue<T>, T) => void
        * 단일 atom의 초기값 제공
      * setUnvaliddatedAtomValues: (Map<string, mixed>) => vvoid
        * 전달된 Map<string, mixed>에서 atom state를 만들 때 설정한 key에 대해 초기값 설정한다.  useSetUnvalidedAtomValues와 마찬가지로, 다음에 읽을 때 각 원자에 대한 검증자가 호출되며, 구성된 검증자가 없는 원자를 설정하면 예외가 된다.

### example

```jsx
import {RecoilRoot} from 'recoil';

function AppRoot() {
  return (
    <RecoilRoot>
      <ComponentThatUsesRecoil />
    </RecoilRoot>
  );
}
```

## atom(options)

쓰기 가능한 recoil state 반환

---

* options 
  * key: atom 내부적으로 식별하는데 사용하는 고유한 문자열입니다. 키는 다른 atom 또는 selector를 호출할 때 전달한 key와 곂치면 안됩니다.
  * default: atom 상태의 초기값

atom과 상호작용을 위해 다음과 같은 훅스가 있습니다.

* useRecoilState(): 이 훅스는 atom에 대한 읽기와 쓰기를 할 때 사용합니다.
* useRecoilValue(): 이 훅스는 atom에 대해 읽기만 할 때 사용합니다.
* useSetRecoilState(): 이 훅스는 atom에 대해 쓰기만 할 때 사용합니다.

atom를 구독하지 않고 값을 읽어야 하는 경우 recoilCallback()을 사용합니다. recoilCallback()을 사용하면 atom이 업데이트 되더라도 랜더링을 하지 않습니다.

### example

```jsx
import {atom, useRecoilState} from 'recoil';

const counter = atom({
  key: 'myCounter',
  default: 0,
});

function Counter() {
  const [count, setCount] = useRecoilState(counter);
  const incrementByOne = () => setCount(count + 1);

  return (
    <div>
      Count: {count}
      <br />
      <button onClick={incrementByOne}>Increment</button>
    </div>
  );
}
```

## isRecoilValue(value)

값이 atom 또는 selector일 경우 true를 반환하고, 아니면 false를 반환합니다.

---

### example

```js
import {atom, isRecoilValue} from 'recoil';

const counter = atom({
  key: 'myCounter',
  default: 0,
});

const strCounter = selector({
  key: 'myCounterStr',
  get: ({get}) => String(get(counter)),
});

isRecoilValue(counter); // true
isRecoilValue(strCounter); // true

isRecoilValue(5); // false
isRecoilValue({}); // false
```



## selector(option)

함수에 전달된 옵션에 따라 쓰기 가능 또는 읽기 전용 recoil state를 반환합니다.

Selector는 상태를 가져와 보여줍니다. 가져온 상태는 최신 상태입니다. 가져온 상태는 어떤 주어진 상태를 수정하는 순수한 함수에 상태를 전달한 결과로 생각할 수 있습니다.

---

* options
  * key: atom 내부적으로 식별하는데 사용하는 고유한 문자열입니다. 키는 다른 atom 또는 selector를 호출할 때 전달한 key와 곂치면 안됩니다.
  * get: 다음 속성을 포함하는 첫 번째 배개 변수로 객체에 전달되는 함수 입니다.
    * get: 다른 atom/selector에서 값을 검색하는데 사용하는 함수
  * set?: 이 속성이 설정되면 선택기는 쓰기 가능한 상태를 반환합니다. 다음 속성을 포함하는 첫 번째 매개변수로 객체에 전달되는 함수입니다.
    * get: 다른 atom/selector에서 값을 검색하는데 사용하는 함수. 이 함수는 값을 검색하는데만 사용하고 구독하지 않습니다.
    * set: recoil state 값을 설정하는 데 사용, 첫 번째 매개 변수는 recoil state 값이고 두 번째 배개 변수는 새로운 값입니다.

### example

```jsx
import {atom, selector, useRecoilState} from 'recoil';

const tempFahrenheit = atom({
  key: 'tempFahrenheit',
  default: 32,
});

const tempCelcius = selector({
  key: 'tempCelcius',
  get: ({get}) => ((get(tempFahrenheit) - 32) * 5) / 9,
  set: ({set}, newValue) => set(tempFahrenheit, (newValue * 9) / 5 + 32),
});

function TempCelcius() {
  const [tempF, setTempF] = useRecoilState(tempFahrenheit);
  const [tempC, setTempC] = useRecoilState(tempCelcius);

  const addTenCelcius = () => setTempC(tempC + 10);
  const addTenFahrenheit = () => setTempF(tempF + 10);

  return (
    <div>
      Temp (Celcius): {tempC}
      <br />
      Temp (Fahrenheit): {tempF}
      <br />
      <button onClick={addTenCelcius}>Add 10 Celcius</button>
      <br />
      <button onClick={addTenFahrenheit}>Add 10 Fahrenheit</button>
    </div>
  );
}
```

## useRecoilCallback()

### example

```js
import {atom, useRecoilCallback} from 'recoil';

const itemsInCart = atom({
  key: 'itemsInCart',
  default: 0,
});

function CartInfoDebug() {
  const logCartItems = useRecoilCallback(async ({getPromise}) => {
    const numItemsInCart = await getPromise(itemsInCart);

    console.log('Items in cart: ', numItemsInCart);
  });

  return (
    <div>
      <button onClick={logCartItems}>Log Cart Items</button>
    </div>
  );
}
```

## useRecoilValue(state)

주어진 recoil 상태 반환

이 훅스는 컴포넌트를 주어진 상태로 내재적으로 구독합니다.

---

* state: **`atom`** 또는 **`selector`**

이 훅스는 읽기 전용 상태와 쓰기 가능한 상태 모두에서 작동하므로 구성 요소에 쓰지 않고 상태를 읽으려고 할 때 권장되는 훅스입니다. atom는 쓰기 가능 상태이지만 선택기는 읽기 전용이거나 쓰기 가능할 수 있습니다. 자세한 내용은 selector()를 참조하십시오.

### example

```js
import {atom, selector, useRecoilValue} from 'recoil';

const namesState = atom({
  key: 'namesState',
  default: ['', 'Ella', 'Chris', '', 'Paul'],
});

const filteredNamesState = selector({
  key: 'filteredNamesState',
  get: ({get}) => get(namesState).filter((str) => str !== ''),
});

function NameDisplay() {
  const names = useRecoilValue(namesState);
  const filteredNames = useRecoilValue(filteredNamesState);

  return (
    <>
      Original names: {names.join(',')}
      <br />
      Filtered names: {filteredNames.join(',')}
    </>
  );
}
```

## useRecoilValueLoadable(state)

**`Loadable`** 반환

이 훅스는 비동기 selectors를 읽을 때 사용합니다. 이 후크는 컴포넌트를 주어진 상태로 내재적으로 구독합니다.

`useRecoilValue()` 와 달리 비동기 selector를 읽을 때 `pending` 상태인 promise 객체를 던지지 않습니다. 대신 훅스는 다음 인터페이스를 가진 Loadable을 반환합니다.

---

* state: 선택기의 상태를 나타냅니다. 사용 가능한 값은 `hasValue`, `hasError`, `loading` 입니다.
* getValue(): 오류가 있는 경우 이 함수는 오류가 발생합니다. 선택기가 로드중인 경우 Promise를 던집니다. 이 두가지 상황이 아니라면 선택기가 확인한 값을 반환합니다.
* toPromise(): 선택기는 Promise 객체를 반환합니다. 선택기가 동기식이거나 이미 해결된 경우, resolve된 Promise 객체를 반환합니다.

---

* state: 비동기식 작동이 가능한 **`selector`** 반환. 반환된 laudable 상태는 선택기에 전달된 상태에 의존적입니다.

### example

```

```

## useResetRecoilState(state)

주어진 상태의 값을 기본값으로 재설정하는 함수를 반환합니다.

---

* state: 쓰기 가능한 recoil state

### example

```js

```

## useRecoilState(state)

첫 번째 요소가 state의 값이고, 두 번째 요소가 state를 변경하는 setter 함수인 튜플을 반환합니다.

이 훅스는 컴포넌트를 주어진 상태로 내재적으롤 구독합니다.

---

* state: **`Atom`** 또는 쓰기 가능한 **`selector`** 는 정의에 `get`, `set` 둘 다 있으며 읽기 전용 selector는 get만 있습니다.

이것은 컴포넌트가 상태를 읽고 쓰려고 할 때 사용하는 권장 훅스입니다.

### example

```jsx
import {atom, selector, useRecoilState} from 'recoil';

const tempFahrenheit = atom({
  key: 'tempFahrenheit',
  default: 32,
});

const tempCelcius = selector({
  key: 'tempCelcius',
  get: ({get}) => ((get(tempFahrenheit) - 32) * 5) / 9,
  set: ({set}, newValue) => set(tempFahrenheit, (newValue * 9) / 5 + 32),
});

function TempCelcius() {
  const [tempF, setTempF] = useRecoilState(tempFahrenheit);
  const [tempC, setTempC] = useRecoilState(tempCelcius);

  const addTenCelcius = () => setTempC(tempC + 10);
  const addTenFahrenheit = () => setTempF(tempF + 10);

  return (
    <div>
      Temp (Celcius): {tempC}
      <br />
      Temp (Fahrenheit): {tempF}
      <br />
      <button onClick={addTenCelcius}>Add 10 Celcius</button>
      <br />
      <button onClick={addTenFahrenheit}>Add 10 Fahrenheit</button>
    </div>
  );
}
```

## useRecoilStateLoadable()

## useSetResoilState(state)

쓰기 가능한 recoil state을 업데이트하기 위한 setter 함수를 반환합니다.

---

* state: 쓰기 가능한 recoil state

### example

```js

```



