# react-query

## installed

```
    "react-query": "^3.39.1",
```

## Learned

기존의 상태관리 라이브러리는 client 상태에 대한 고려만 했다면, react-query는 서버의 상태를 관리하기 위해 나왔다.  
(서버로부터 받아온 상태를 react에서 잘 맞춰주기 위해)

## Concept

Query / Mutate / Query Invalidate

### QueryKey

```js
useQuery('todos' ,...)  // queryKey === ['todos']
```

QueryKey의 default는 배열
fetch하는 url을 구성하는 요소들을 QueryKey로 전달할 수 있다.

### Query Functions

- returns a promise (resolve the data | throw an error)
- Query Keys가 params로 넘어온다.
- useQuery에 params대신 객체로 넘길 수도 있다.

```js
useQuery({ queryKey, queryFn, config });
```

```js
function Todos({ status, page }) {
  const result = useQuery(["todos", { status, page }], fetchTodoList);
}

//  query function에 page, key, status를 가져올 수 있다
function fetchTodoList({ queryKey }) {
  const [_key, { status, page }] = queryKey;
  return new Promise();
}
```

parameters 대신, query Object로 전달 가능하다.(useQueries에서 쓰임)

```js
useQuery({
  queryKey: ["todos", 7],
  queryFn: fetchTodo,
  ...config,
});
```

### Parallel Queries

기본이 병렬 수행이다.
suspense사용하면 제대로 동작하지 않는다. 대신 useQueries를 사용한다.

```js
// Dynamic Parallel Queries
useQueries([{ queryKey, QueryFn }]);
```

```js
function App() {
  const userQuery = useQuery("user", fetchUsers);
  const teamQuery = useQuery("teams", fetchTeams);
  const projectsQuery = useQuery("projects", fetchProjects);
}
```

기본적으로 Query들은 병렬로 동작한다.
하지만 Suspense모드일 경우 병렬이 제대로 동작하지 않는다.
따라서 useQueries를 사용한다.

### Dependent Queries

```js
const { data: user } = useQuery(["user", mail], getUserByEmail);

const userId = user?id

const {isIdle, data : projects} = useQuery(['projects', userId], getProjectsByUser, {enabled :!! userId,})
```

config enabled에 `{enabled :!!userId}` 'userId'값이 없다면 useQuery는 실행되지 않음
