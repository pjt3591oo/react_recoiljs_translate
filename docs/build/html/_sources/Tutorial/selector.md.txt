# Selectors

Selector는 상태를 가져와 보여줍니다. 가져온 상태는 최신 상태입니다. 가져온 상태는 어떤 주어진 상태를 수정하는 순수한 함수에 상태를 전달한 결과로 생각할 수 있습니다.

selector를 이용하여 가져온 상태는 다른 데잉터에 의해 의존하는 동적 데이터를 구축할 수 있습니다. 여기서 selector로 가져온 상태를 파생된 상태(derived state)라고 합니다. 할 일 목록 애플리케이션에서 다음을 파생된 상태로 취급합니다.

* Filtered Todo List: 필터링 한 데이터 파생
* Todo list statistics: 목록의 총 항목 수, 완료된 항목 수, 백분율과 같은 통계 데이터 파생

할일 목록을 필터링하기 위해 값을 아톰에 저장 할 수 있는 필터 기준을 선택해야 합니다. 여기서 사용할 필터 옵션은 모두 표시, 완료 표시, 미완료 표시 입니다. 기본값은 모두 표시입니다.

```js
const todoListFilterState = atom({
  key: 'todoListFilterState',
  default: 'Show All',
});
```

`todoListFilterState` 및 `todoListState`를 사용하여 필터링 된 목록을 파생시키는 `filtersTodoListState` 선택기를 작성할 수 있습니다.

```js
const filteredTodoListState = selector({
  key: 'filteredTodoListState',
  get: ({get}) => {
    const filter = get(todoListFilterState);
    const list = get(todoListState);

    switch (filter) {
      case 'Show Completed':
        return list.filter((item) => item.isComplete);
      case 'Show Uncompleted':
        return list.filter((item) => !item.isComplete);
      default:
        return list;
    }
  },
});
```

filteredTodoListState는 todoListFilterState와 todoListState 중 하나라도 변경되면 다시 실행합니다.

필터링 된 todoList를 표시하기 위해 TodoList 컴포넌트에서 주석처리 된 TodoListStats와 TodoListFilters 컴포넌트 주석을 해제합니다.

```jsx
function TodoList() {
  const todoList = useRecoilValue(todoListState);

  return (
    <>
      
        <TodoListStats />
        <TodoListFilters />
        <TodoItemCreator />

        {todoList.map((todoItem) => (
          <TodoItem item={todoItem} />
        ))}
    </>
  );
}
```

필터 변경을 이용해 todoListFilters 컴포넌트를 구현합니다.

```jsx
function TodoListFilters() {
  const [filter, setFilter] = useRecoilState(todoListFilterState);

  const updateFilter = ({target: {value}}) => {
    setFilter(value);
  };

  return (
    <>
      Filter:
      <select value={filter} onChange={updateFilter}>
        <option value="Show All">All</option>
        <option value="Show Completed">Completed</option>
        <option value="Show Uncompleted">Uncompleted</option>
      </select>
    </>
  );
}
```

짧은 줄로 필터링 컴포넌트를 구현했습니다. 동일한 개념을 사용하여 `TodoListStats` 구성 요소를 구현합니다.

표시할 통계항목은 다음과 같습니다.

* 할 일 항목의 총 수
* 완료된 항목 수
* 완료되지 않은 항목의 총 수
* 완료된 항목의 백분율

각 통계에 대해 selector를 만들 수 있지만 필요한 데이터가 포함된 객체를 반환하는 하나의 선택기를 만들 수 있습니다. 여기서는 selector를 todoListStatsState로 구현합니다.

```js
const todoListStatsState = selector({
  key: 'todoListStatsState',
  get: ({get}) => {
    const todoList = get(filteredTodoListState);
    const totalNum = todoList.length;
    const totalCompletedNum = todoList.filter((item) => item.isComplete).length;
    const totalUncompletedNum = totalNum - totalCompletedNum;
    const percentCompleted = totalNum === 0 ? 0 : totalCompletedNum / totalNum;

    return {
      totalNum,
      totalCompletedNum,
      totalUncompletedNum,
      percentCompleted,
    };
  },
});
```

`todoListStatsState` 의 값을 읽기 위해 useRecoilValue()를 사용합니다.

```jsx
function TodoListStats() {
  const {
    totalNum,
    totalCompletedNum,
    totalUncompletedNum,
    percentCompleted,
  } = useRecoilValue(todoListStatsState);

  const formattedPercentCompleted = Math.round(percentCompleted * 100);

  return (
    <ul>
      <li>Total items: {totalNum}</li>
      <li>Items completed: {totalCompletedNum}</li>
      <li>Items not completed: {totalUncompletedNum}</li>
      <li>Percent completed: {formattedPercentCompleted}</li>
    </ul>
  );
}
```

지금까지 구현된 할 일 목록 애플리케이션 기능 목록은 다음과 같습니다. 

* 할 일 추가
* 할 일 수정
* 할 일 삭제
* 필터
* 통계

다음 섹션에서 중요한 고려사항을 다룹니다.