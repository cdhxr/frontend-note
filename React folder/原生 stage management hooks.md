---
tags:
  - react
date: 2025-03-25
---

# Reduce hook

Reducer 是处理状态的另一种方式。
你可以通过三个步骤将 `useState` 迁移到 `useReducer`：

1. 将设置状态的逻辑 **修改** 成 dispatch 的一个 action；

```js
function handleAddTask(text) {
  dispatch({
    type: 'added',
    id: nextId++,
    text: text,
  });
}

function handleChangeTask(task) {
  dispatch({
    type: 'changed',
    task: task,
  });
}

function handleDeleteTask(taskId) {
  dispatch({
    type: 'deleted',
    id: taskId,
  });
}
```

即将handle函数内部的逻辑进行一个身份的标识（即定义action对象）
随后将据此在reducer中找到对应的行为

2. **编写** 一个 reducer 函数；

```js
function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('未知 action: ' + action.type);
    }
  }
}
```


3. 在你的组件中 **使用** reducer。

最后，你需要将 `tasksReducer` 导入到组件中。记得先从 React 中导入 `useReducer` Hook：

```
import { useReducer } from 'react';
```

接下来，你就可以替换掉之前的 `useState`:

```
const [tasks, setTasks] = useState(initialTasks);
```

只需要像下面这样使用 `useReducer`:

```
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```


useReducer 和 useState 很相似——你必须给它传递一个初始状态，它会返回一个有状态的值和一个设置该状态的函数（在这个例子中就是 dispatch 函数）。但是，它们两个之间还是有点差异的。

useReducer 钩子接受 2 个参数：

一个 reducer 函数
一个初始的 state
它返回如下内容：

一个有状态的值
一个 dispatch 函数（用来 “派发” 用户操作给 reducer）

## 对比 `useState` 和 `useReducer` 

Reducer 并非没有缺点！以下是比较它们的几种方法：

- **代码体积：** 通常，在使用 `useState` 时，一开始只需要编写少量代码。而 `useReducer` 必须提前编写 reducer 函数和需要调度的 actions。但是，当多个事件处理程序以相似的方式修改 state 时，`useReducer` 可以减少代码量。
- **可读性：** 当状态更新逻辑足够简单时，`useState` 的可读性还行。但是，一旦逻辑变得复杂起来，它们会使组件变得臃肿且难以阅读。在这种情况下，`useReducer` 允许你将状态更新逻辑与事件处理程序分离开来。
- **可调试性：** 当使用 `useState` 出现问题时, 你很难发现具体原因以及为什么。 而使用 `useReducer`时， 你可以在 reducer 函数中通过打印日志的方式来观察每个状态的更新，以及为什么要更新（来自哪个 `action`）。 如果所有 `action` 都没问题，你就知道问题出在了 reducer 本身的逻辑中。 然而，与使用 `useState` 相比，你必须单步执行更多的代码。
- **可测试性：** reducer 是一个不依赖于组件的纯函数。这就意味着你可以单独对它进行测试。一般来说，我们最好是在真实环境中测试组件，但对于复杂的状态更新逻辑，针对特定的初始状态和 `action`，断言 reducer 返回的特定状态会很有帮助。
- **个人偏好：** 并不是所有人都喜欢用 reducer，没关系，这是个人偏好问题。你可以随时在 `useState`和 `useReducer` 之间切换，它们能做的事情是一样的！

如果你在修改某些组件状态时经常出现问题或者想给组件添加更多逻辑时，我们建议你还是使用 reducer。当然，你也不必整个项目都用 reducer，这是可以自由搭配的。你甚至可以在一个组件中同时使用 `useState` 和 `useReducer`。

## 编写一个好的 reducer 

编写 `reducer` 时最好牢记以下两点：

- **reducer 必须是纯粹的。** 这一点和 [状态更新函数](https://zh-hans.react.dev/learn/queueing-a-series-of-state-updates) 是相似的，`reducer` 是在渲染时运行的！（actions 会排队直到下一次渲染)。 这就意味着 `reducer` [必须纯净](https://zh-hans.react.dev/learn/keeping-components-pure)，即当输入相同时，输出也是相同的。它们不应该包含异步请求、定时器或者任何副作用（对组件外部有影响的操作）。它们应该以不可变值的方式去更新 [对象](https://zh-hans.react.dev/learn/updating-objects-in-state) 和 [数组](https://zh-hans.react.dev/learn/updating-arrays-in-state)。
- **每个 action 都描述了一个单一的用户交互，即使它会引发数据的多个变化。** 举个例子，如果用户在一个由 `reducer` 管理的表单（包含五个表单项）中点击了 `重置按钮`，那么 dispatch 一个 `reset_form`的 action 比 dispatch 五个单独的 `set_field` 的 action 更加合理。如果你在一个 `reducer` 中打印了所有的 `action` 日志，那么这个日志应该是很清晰的，它能让你以某种步骤复现已发生的交互或响应。这对代码调试很有帮助！

# Context hook

- Context 使组件向其下方的整个树提供信息。
- 传递 Context 的方法:
    1. 通过 `export const MyContext = createContext(defaultValue)` 创建并导出 context。
    2. 在无论层级多深的任何子组件中，把 context 传递给 `useContext(MyContext)` Hook 来读取它。
    3. 在父组件中把 children 包在 `<MyContext value={...}>` 中来提供 context。
- Context 会穿过中间的任何组件。
- Context 可以让你写出 “较为通用” 的组件。
- 在使用 context 之前，先试试传递 props 或者将 JSX 作为 `children` 传递。

作用：
	跨组件通信，类似于vue中的provide inject

1. 首先，你需要创建这个 context，并 **将其从一个文件中导出**，这样你的组件才可以使用它
```js
import { createContext } from 'react';
export const LevelContext = createContext(1)
```

2. 使用context

```Js
import { useContext } from 'react';  
import { LevelContext } from './LevelContext.js';

//···
const level = useContext(LevelContext);
```

3. 提供context

```js
import { LevelContext } from './LevelContext.js';  

export default function Section({ level, children }) {  
  return (  
    <section className="section">  
	  <LevelContext value={level}>  
		{children}  
	  </LevelContext>  
	</section>  
  );  
}
```

这告诉 React：“如果在 `<Section>` 组件中的任何子组件请求 `LevelContext`，给他们这个 `level`。”组件会使用 UI 树中在它上层最近的那个 `<LevelContext>` 传递过来的值。

1. 你将一个 `level` 参数传递给 `<Section>`。
2. `Section` 把它的子元素包在 `<LevelContext value={level}>` 里面。
3. `Heading` 使用 `useContext(LevelContext)` 访问上层最近的 `LevelContext` 提供的值。

技巧：
- 由于 context 让你可以从上层的组件读取信息，每个 `Section` 都会从上层的 `Section` 读取 `level`，并自动向下层传递 `level + 1`。
- 这样就不需要每次进行手动传递

```Js
import { useContext } from 'react';  
import { LevelContext } from './LevelContext.js';  

export default function Section({ children }) {  
  const level = useContext(LevelContext);  
  return (  
	<section className="section">  
	  <LevelContext value={level + 1}>  
	    {children}  
	  </LevelContext>  
	</section>  
  );  
}
```



Context 的工作方式可能会让你想起 [CSS 属性继承](https://developer.mozilla.org/zh-CN/docs/Web/CSS/inheritance)。在 CSS 中，你可以为一个 `<div>` 手动指定 `color: blue`，并且其中的任何 DOM 节点，无论多深，都会继承那个颜色，除非中间的其他 DOM 节点用 `color: green` 来覆盖它。类似地，在 React 中，覆盖来自上层的某些 context 的唯一方法是将子组件包裹到一个提供不同值的 context provider 中。

## 写在你使用 context 之前

使用 Context 看起来非常诱人！然而，这也意味着它也太容易被过度使用了。**如果你只想把一些 props 传递到多个层级中，这并不意味着你需要把这些信息放到 context 里。**

在使用 context 之前，你可以考虑以下几种替代方案：

1. **从 [传递 props](https://zh-hans.react.dev/learn/passing-props-to-a-component) 开始。** 如果你的组件看起来不起眼，那么通过十几个组件向下传递一堆 props 并不罕见。这有点像是在埋头苦干，但是这样做可以让哪些组件用了哪些数据变得十分清晰！维护你代码的人会很高兴你用 props 让数据流变得更加清晰。
2. **抽象组件并 [将 JSX 作为 `children` 传递](https://zh-hans.react.dev/learn/passing-props-to-a-component#passing-jsx-as-children) 给它们。** 如果你通过很多层不使用该数据的中间组件（并且只会向下传递）来传递数据，这通常意味着你在此过程中忘记了抽象组件。举个例子，你可能想传递一些像 `posts` 的数据 props 到不会直接使用这个参数的组件，类似 `<Layout posts={posts} />`。取而代之的是，让 `Layout` 把 `children` 当做一个参数，然后渲染 `<Layout><Posts posts={posts} /></Layout>`。这样就减少了定义数据的组件和使用数据的组件之间的层级。

如果这两种方法都不适合你，再考虑使用 context。

## Context 的使用场景 

- **主题：** 如果你的应用允许用户更改其外观（例如暗夜模式），你可以在应用顶层放一个 context provider，并在需要调整其外观的组件中使用该 context。
- **当前账户：** 许多组件可能需要知道当前登录的用户信息。将它放到 context 中可以方便地在树中的任何位置读取它。某些应用还允许你同时操作多个账户（例如，以不同用户的身份发表评论）。在这些情况下，将 UI 的一部分包裹到具有不同账户数据的 provider 中会很方便。
- **路由：** 大多数路由解决方案在其内部使用 context 来保存当前路由。这就是每个链接“知道”它是否处于活动状态的方式。如果你创建自己的路由库，你可能也会这么做。
- **状态管理：** 随着你的应用的增长，最终在靠近应用顶部的位置可能会有很多 state。许多遥远的下层组件可能想要修改它们。通常 [将 reducer 与 context 搭配使用](https://zh-hans.react.dev/learn/scaling-up-with-reducer-and-context)来管理复杂的状态并将其传递给深层的组件来避免过多的麻烦。

Context 不局限于静态值。如果你在下一次渲染时传递不同的值，React 将会更新读取它的所有下层组件！这就是 context 经常和 state 结合使用的原因。

# 结合使用

Reducer 可以整合组件的状态更新逻辑。Context 可以将信息深入传递给其他组件。你可以组合使用它们来共同管理一个复杂页面的状态。

使用reducer，需要dispatch函数和reducer函数，前者存储key，后者存储key对应的逻辑

我们可以将这两个函数作为context提取，

```js
export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

再将这两个context作为reducer使用
这样，reducer就可以在任意支持context的地方使用了

```js
export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
  // ...
  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        ...
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```

原本需要使用Props传递的事件处理函数和tasks数组，现在都可以通过实例化context使用，而不需要再组件tag中传递Props

# 将逻辑移至一个文件中

这不是必须的，但你可以通过将 reducer 和 context 移动到单个文件中来进一步整理组件。目前，“TasksContext.js” 仅包含两个 context 声明：

```js
import { createContext } from 'react';
export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

来给这个文件添加更多代码！将 reducer 移动到此文件中，

然后声明一个新的 `TasksProvider` 组件。此组件将所有部分连接在一起：

1. 它将管理 reducer 的状态。
2. 它将提供现有的 context 给组件树。
3. 它将 [把 `children` 作为 prop](https://zh-hans.react.dev/learn/passing-props-to-a-component#passing-jsx-as-children)，所以你可以传递 JSX。

```js
export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```

于是，taskprovider就实现了逻辑的集中，**这将使 `TaskApp` 组件更加直观**

```js
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksProvider } from './TasksContext.js';

export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}
```

你可以将 `TasksProvider` 视为页面的一部分，它知道如何处理 tasks。`useTasks` 用来读取它们，`useTasksDispatch` 用来从组件树下的任何组件更新它们。

- 你可以将 reducer 与 context 相结合，让任何组件读取和更新它的状态。
- 为子组件提供 state 和 dispatch 函数：
    1. 创建两个 context (一个用于 state,一个用于 dispatch 函数)。
    2. 让组件的 context 使用 reducer。
    3. 使用组件中需要读取的 context。
- 你可以通过将所有传递信息的代码移动到单个文件中来进一步整理组件。
    - 你可以导出一个像 `TasksProvider` 可以提供 context 的组件。
    - 你也可以导出像 `useTasks` 和 `useTasksDispatch` 这样的自定义 Hook。
- 你可以在你的应用程序中大量使用 context 和 reducer 的组合。