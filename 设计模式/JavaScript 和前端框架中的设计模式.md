JavaScript中的设计模式

- 原型模式
	- 定义：复制已有对象来创建新的对象
	- 应用场景：JS中对象创建的基本模式
	- 思想：创建一个原型，用于变化，类似于class
- 代理模式
	- 可以自定义来对原对象的访问方式，并且允许更新前后的一些额外处理
	- 代理工具，前端框架实现，监控等等
	
	这是一个实现用户订阅的代码，以下是详细的注释和关于代理模式的分析：

```typescript
// 定义一个通知回调类型，用于在用户状态变化时通知订阅者
type Notify = (user: User) => void;

// 定义 User 类，代表一个用户
export class User {
  name: string; // 用户的名字
  status: "offline" | "online"; // 用户的状态，可以是“offline”或“online”
  followers: { user: User; notify: Notify }[]; // 订阅此用户的订阅者列表，每个订阅者包含一个用户对象和一个通知回调

  // 构造函数，用于初始化用户的名字、状态和订阅者列表
  constructor(name: string) {
    this.name = name;
    this.status = "offline"; // 默认状态为离线
    this.followers = []; // 初始化为空的订阅者列表
  }

  // 订阅方法，用于让另一个用户订阅此用户的状态变化
  subscribe(user: User, notify: Notify) {
    // 将订阅者及其通知回调添加到 followers 列表中
    user.followers.push({ user, notify });
  }

  // 改变用户状态为在线，并通知所有订阅者
  online() {
    this.status = "online"; // 更新用户状态为“在线”

    // 遍历所有订阅者，调用其通知回调并传递当前用户对象
    this.followers.forEach(({ notify }) => {
      notify(this);
    });
  }
}
```

利用代理模式优化，使online方法只做一件事，这是一个通过 `Proxy` 对象实现用户状态代理的代码

```typescript
// 定义一个函数类型 Notify，用于表示订阅者的通知回调函数
// 参数是 User 类型，返回值是 void
type Notify = (user: User) => void;

// 定义一个用户类 User，表示用户及其状态和订阅功能
class User {
  name: string; // 用户名
  status: "offline" | "online"; // 用户的状态，可能是 "offline" 或 "online"
  followers: { user: User; notify: Notify }[]; // 关注者列表，包含用户及其通知回调函数

  // 构造函数，用于初始化 User 类的实例
  constructor(name: string) {
    this.name = name; // 初始化用户名
    this.status = "offline"; // 默认状态设置为 "offline"
    this.followers = []; // 初始化为空的关注者列表
  }

  // 定义一个方法，用于订阅其他用户的状态更新
  // 参数：
  // - user: 要订阅的用户
  // - notify: 当订阅的用户状态更新时调用的回调函数
  subscribe(user: User, notify: Notify) {
    // 将当前用户及其通知回调添加到目标用户的关注者列表中
    user.followers.push({ user, notify });
  }

  // 定义一个方法，用于将当前用户的状态设置为 "online"
  online() {
    this.status = "online"; // 更新用户状态为 "online"
  }
}
```

```typescript
// 创建一个代理用户
export const createProxyUser = (name: string) => {
  const user = new User(name); // 创建一个普通的用户实例

  // 创建代理对象，用于拦截对用户属性的修改
  const proxyUser = new Proxy(user, {
    // 拦截属性赋值操作
    set: (target, prop: keyof User, value) => {
      target[prop] = value; // 将新值赋给目标对象的属性
      if (prop === "status") { // 如果被修改的属性是 status
        notifyStatusHandlers(target, value); // 调用状态处理程序
      }
      return true; // 返回 true 表示设置成功
    },
  });

  return proxyUser; // 返回代理用户对象
};

// 定义一个函数，用于通知所有订阅者用户的状态变化
const notifyStatusHandlers = (user: User, status: "online" | "offline") => {
  if (status === "online") { // 如果状态为 online
    // 遍历用户的所有订阅者，并调用它们的通知回调函数
    user.followers.forEach(({ notify }) => {
      notify(user);
    });
  }
};
```



### 代码中体现代理模式的部分

1. **代理的核心：**
    
    - 使用 `Proxy` 对象动态拦截对用户实例的操作（特别是对 `status` 属性的修改）。
    - 代理对象通过 `set` 方法拦截对 `status` 属性的更新，在属性更新的同时自动调用 `notifyStatusHandlers` 来处理订阅者通知逻辑。这种动态拦截机制是代理模式的重要特征。
2. **职责分离与控制访问：**
    
    - 用户类本身的职责只包括用户属性和基础方法（如订阅功能），而状态变化的拦截和通知逻辑被独立到代理对象中。
    - 通过代理对象，开发者可以对用户状态的修改进行统一的管理和扩展，而无需直接修改用户类的实现。这是代理模式的一种体现，即通过代理控制对目标对象的访问方式。
3. **动态行为注入：**
    
    - 使用代理的好处是可以动态注入额外的行为（如状态更新时通知订阅者）而不破坏原有的类设计。这种灵活性在代理模式中非常常见。

### 总结

与之前代码的不同点在于，这段代码显式地使用了 `Proxy` 来实现动态拦截和扩展功能，这是一种更为典型的代理模式应用方式。代理对象扮演了“控制器”的角色，统一处理 `status` 属性的更新和订阅者的通知逻辑，使得用户类和额外行为的耦合度进一步降低。


- 迭代器模式
	- 不暴露数据类型的情况下访问集合的数据
	- 为数据结构提供操作接口
	- 类似Java中定义一个iterator，以使用for each（JS中为for of）


前端框架中的设计模式

- 代理模式

代理模式（Proxy Pattern）的核心思想是通过一个代理对象控制对目标对象的访问。代理对象作为中间层，可以对目标对象的行为进行扩展、控制或修改，而不需要改变目标对象的代码。

### 核心思想

代理模式的核心是 **“间接性”**。它让对象的调用者不直接与目标对象交互，而是通过代理对象完成。这样可以实现以下功能：

1. **控制访问**：可以控制目标对象的访问权限，保护目标对象。
2. **延迟加载**：可以在需要时才创建目标对象，实现懒加载。
3. **增强功能**：在调用目标对象的方法之前或之后增加额外的功能。
4. **远程代理**：通过网络代理访问远程目标对象。

### 组成部分

1. **抽象主题（Subject）**： 定义目标对象和代理对象的共同接口，保证代理类能够替代目标对象。
    
2. **真实主题（RealSubject）**： 实现抽象主题，是真正需要代理的对象。
    
3. **代理（Proxy）**： 持有真实主题的引用，负责控制对真实主题的访问，并可以在调用真实主题的方法前后添加额外功能。


- 组合模式

**组合模式**（Composite Pattern）的核心思想是通过树形结构来组合对象，使得客户端可以一致地处理单个对象和对象的组合。它是一种结构型设计模式，适用于需要以统一方式处理 **部分-整体关系** 的场景。

---

### **核心思想**

1. **统一接口**：
    
    - 定义一个统一的抽象接口，无论是单个对象（叶子节点）还是对象组合（容器节点），都实现相同的接口。
    - 客户端无需区分操作的是单个对象还是组合对象，提升了系统的灵活性和一致性。
2. **树形结构**：
    
    - 组合模式将对象组织成一个树形结构，叶子节点表示单个对象，非叶子节点表示由多个对象组合而成的容器。
    - 容器可以递归地嵌套，形成层级关系。
3. **递归操作**：
    
    - 通过递归调用，组合模式可以对树形结构中的所有节点进行统一操作。

---

### **组成部分**

1. **组件（Component）**：
    
    - 定义组合对象和叶子对象的公共接口。
    - 声明了客户端可以使用的方法，比如 `add`、`remove` 和 `operation`。
2. **叶子（Leaf）**：
    
    - 实现组件接口，表示树的叶子节点，不能包含子节点。
    - 通常实现一些基本操作。
3. **容器（Composite）**：
    
    - 实现组件接口，表示树的非叶子节点。
    - 容器可以包含子组件（叶子或其他容器），并实现管理子组件的方法，比如 `add`、`remove`。
4. **客户端（Client）**：
    
    - 通过组件接口与对象交互，不需要关心是单个对象还是组合对象。