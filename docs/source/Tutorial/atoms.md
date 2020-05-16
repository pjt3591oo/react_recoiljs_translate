# Atoms

Atoms은 애플리케이션 상태에 대한 신뢰를 제공합니다. 우리의 Todo List의 신뢰는 각각의 객체로 저장된 할 일 입니다. 

atom() 함수를 호출하여 리스트로 정의된 Atom을 생성합니다.

```js
const todoListState = atom({
  key: 'todoListState',
  default: [],
});
```

아톰에 고유키를 부여하고 기본값을 빈 배열로 초기화 합니다. 이 아톰의 값을 읽기위해 useRecoilValue() 후크를 사용합니다.

> 참고: useRecoilValue()는 아톰의 값을 오직 읽는것만 가능, useRecoilState()는 useState()와 유사한 형태로 동작

```jsx
function TodoList() {
  const todoList = useRecoilValue(todoListState);

  return (
    <>
      {/* <TodoListStats /> */}
      {/* <TodoListFilters /> */}
      <TodoItemCreator />

      {todoList.map((todoItem) => (
        <TodoItem item={todoItem} />
      ))}
    </>
  );
}
```

주석 처리된 컴포넌트는 다음 섹션에서 구현합니다.

할일 요소를 생성하기 위해 `todoListState` 내용을 업데이트 할 setter 함수에 접근해야 합니다. useSetRecoilState() 훅스를 사용하여 TodoItemCreator 컴포넌트에서 setter 함수를 가져올 수 있습니다.

```jsx
function TodoItemCreator() {
  const [inputValue, setInputValue] = useState('');
  const setTodoList = useSetRecoilState(todoListState);

  const addItem = () => {
    setTodoList((oldTodoList) => [
      ...oldTodoList,
      {
        id: getId(),
        text: inputValue,
        isComplete: false,
      },
    ]);
  };

	return (
    <div>
      <input type="text" value={inputValue} onChange={e => setInputValue(e.target.value)} />
      <button onClick={addItem}>Add</button>
    </div>
  );
}

// utility for creating unique Id
let id = 0;
function getId() {
  return id++;
}
```

이전 할 일 목록을 기반으로 새 할 일 목록을 만들 수 있도록 업데이터 형식의 setter 함수를 사용합니다.

TodoItem의 구성 요소는 텍스트를 변경하고 항목을 삭제하며 목록의 값을 표시해야 합니다. useRecoilState()를 사용항여 todoListState를 읽고 항목 텍스트를 업데이트하고 완료된 것으로 표시한 후 삭제하는데 사용되는 setter 함수를 가져옵니다.

> 참고: useRecoilState() 훅스는 useState() 훅스처럼 첫 번째 인자는 상태를 두 번째 인자는 setter 함수를 반환합니다.

```jsx
function TodoItem({item}) {
  const [todoList, setTodoList] = useRecoilState(todoListState);
  const index = todoList.findIndex((listItem) => listItem === item);

  const editItemText = ({target: {value}}) => {
    const newList = replaceItemAtIndex(todoList, index, {
      ...item,
      text: value,
    });

    setTodoList(newList);
  };

  const toggleItemCompletion = () => {
    const newList = replaceItemAtIndex(todoList, index, {
      ...item,
      isComplete: !item.isComplete,
    });

    setTodoList(newList);
  };

  const deleteItem = () => {
    const newList = removeItemAtIndex(todoList, index);

    setTodoList(newList);
  };

  return (
    <div>
      <input type="text" value={item.text} onChange={editItemText} />
      <input
        type="checkbox"
        checked={item.isComplete}
        onChange={toggleItemCompletion}
      />
      <button onClick={deleteItem}>X</button>
    </div>
  );
}

function replaceItemAtIndex(arr, index, newValue) {
  return [...arr.slice(0, index), newValue, ...arr.slice(index + 1)];
}

function removeItemAtIndex(arr, index) {
  return [...arr.slice(0, index), ...arr.slice(index + 1)];
}
```

우리는 다음 섹션에서는 selector를 이용하여 목록을 다음단계로 변경하는 방법을 다룹니다.